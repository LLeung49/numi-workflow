# 技术方案与架构设计标杆案例 (Reference Case)

> 💡 **标杆导读**：本文档为分布式延时投递系统的完整技术方案与架构设计真实标杆案例（承接前序需求文档，回答 How）。它全面展示了一份工业级系统架构方案应具备的 18 个核心技术维度：从六大核心决策、数据/控制平面两类事实来源权威划分、强类型公共模型与 SPI 抽象契约先行，到具体的存储 Key 空间、时间轮调度与集群高可用故障接管、以及 11 个核心系统指标与风险应对矩阵。

---

# 示例项目｜分布式延时投递系统技术方案与架构设计

> 本文档承接《需求规格说明书 (PRD)》，回答 How。方案把领域模型、契约、状态、存储、集群、故障恢复、投递、可观测和部署统一成一套可实施设计。
>
> 当前代码尚未实现。本文档是后续切片原料卡、编码和验收采用的技术基准；各模块实施时不得另起一套字段、状态或存储约定。

## 1. 方案摘要

系统采用模块化架构实现，外部提供 HTTP，内部高频通信使用强类型 RPC；分布式 KV 保存消息事实，分布式协同服务保存集群元数据；每个节点运行多个内存多层时间轮；每个时间轮由 1 Master + 1 Slave 承载；唯一 Controller 根据节点事件分配、切换和均衡时间轮；消息到期后由插件化 Sink 投递到 HTTP 或 Kafka。

六个核心决策：

1. 分布式协同组件负责集群协调与选主，不保存业务消息；
2. 集中存储负责消息事实，不参与 Controller 选举；
3. 多层时间轮负责内存调度，不作为可靠性来源；
4. Master/Slave 提供快速热备，集中存储提供最终恢复来源；
5. 外部标准门面降低接入门槛，内部 RPC 提供强类型高频通信；
6. 统一契约优先于具体实现，基于 SPI 插件化隔离外部具体组件。

## 2. 整体架构

### 2.1 数据平面

数据平面处理每条延时消息：接入、校验、发号、路由、持久化、入轮、到期、投递和状态更新。

### 2.2 控制平面

控制平面管理节点和时间轮：节点注册、Lease 心跳、Controller 选举、成员 Watch、时间轮分配、故障切换和 rebalance。

### 2.3 两类事实来源

| 数据类别 | 权威来源 | 内存中的作用 |
|---|---|---|
| 消息内容、状态、到期时间、重试信息 | 持久化存储 (如 Redis) | 时间轮仅保存调度引用指针 |
| 节点、Controller、时间轮分布、同步状态 | 分布式协调元数据 (如 ETCD) | 各节点本地缓存集群视图 |

这条边界不可打破：**存储层不选主，协调元数据不存 payload，内存不承担“已确认消息”的最终可靠性**。

## 3. 领域模型与公共契约

### 3.1 Message 领域模型

```text
public record Message(
    String messageId,          // 全局唯一 ID，由 Snowflake 算法生成
    long createdAt,            // 创建时间戳 (Unix 毫秒)
    long deliverAt,            // 期望到期投递时间戳 (Unix 毫秒)
    String timeWheelId,        // 所属时间轮实例 ID (tw_id)
    SinkType sinkType,         // 目标类型 (HTTP / KAFKA)
    SinkConfig sinkConfig,     // 强类型 Sink 配置 (非自由 Map)
    byte[] payload,            // 消息载荷 (≤ 64KB)
    String businessTag,        // 业务检索标签
    MessageStatus status,      // 当前状态枚举
    int retryCount,            // 已重试次数
    long nextAttemptAt,        // 下次重试时间戳
    long deliveredAt,          // 实际成功投递时间戳
    String lastError,          // 最近一次异常摘要
    String traceId             // 链路追踪 ID (入口生成，全程透传)
)
```

### 3.2 状态机

```text
PENDING ──到期占有权──> DELIVERING ──成功──> DELIVERED
   │                         │
   │取消                     ├─可重试失败──> PENDING(nextAttemptAt)
   ▼                         └─重试耗尽────> FAILED
CANCELLED
```

只有以下流转合法：
- `PENDING → CANCELLED`
- `PENDING → DELIVERING`
- `DELIVERING → DELIVERED`
- `DELIVERING → PENDING`（安排重试）
- `DELIVERING → FAILED`

状态更新使用原子条件写 (CAS)，避免两个节点同时获得投递权。至少一次语义允许故障窗口重复，但绝不能允许正常路径并发双投。

### 3.3 公共 SPI 契约先行

```text
// 存储 SPI：解耦底层具体存储
public interface StoragePlugin {
    void create(Message message);
    Optional<Message> get(String messageId);
    boolean transition(String messageId, MessageStatus from, MessageStatus to, MessagePatch patch);
    List<Message> loadPendingByTimeWheel(String timeWheelId);
    void removeScheduleIndex(String messageId);
}

// 时间轮调度引擎 SPI
public interface TimeWheel {
    String id();
    void add(Message message);
    void remove(String messageId);
    void start();
    void stop();
}

// 下游投递 Sink SPI
public interface Sink {
    SinkType type();
    void validate(SinkConfig config);
    DeliveryResult deliver(Message message);
}

// 路由 SPI
public interface Router {
    String routeToTimeWheel(String messageId);
}

// 到期事件处理接口 (防止调度线程执行同步 IO)
public interface DueMessageHandler {
    void onDue(String messageId);
}
```

公共契约先于各模块实现定稿。模块实现只能依赖 SPI，禁止跨层直接调用具体中间件客户端。时间轮到期时只异步触发 `DueMessageHandler`；获取投递权、调用 Sink 和更新状态均在异步线程池执行。

## 4. 接口设计

### 4.1 外部标准 API
```text
POST   /api/v1/messages       // 提交延时消息
GET    /api/v1/messages/{id}  // 查询消息详情
DELETE /api/v1/messages/{id}  // 取消待处理消息
```

### 4.2 内部 RPC 通信
内部通信包括两组服务：
- `InternalMessageService`：跨节点 Submit、Query、Cancel 请求转发；
- `ReplicaSyncService`：时间轮 `add/remove` 的异步副本同步、重建与状态确认。

所有内部接口的枚举、字段语义与公共领域模型保持绝对一致。

### 4.3 管理 API
```text
GET    /admin/v1/timewheels
POST   /admin/v1/timewheels
GET    /admin/v1/messages
GET    /admin/v1/messages/{id}
GET    /admin/cluster/nodes
GET    /metrics
GET    /health
GET    /ready
```

## 5. 核心写入、查询与取消链路

### 5.1 写入主链路 (Submit)
1. 校验到期时间、payload 大小与 SinkConfig 强类型合法性；
2. 生成 `message_id` 与 `trace_id`；
3. 通过一致性 Hash 计算所属 `tw_id`；
4. 从集群视图查找该时间轮当前的 Master 节点；
5. 若 Master 不在本机，通过内部 RPC 转发至目标节点；
6. Master 在集中存储中原子创建 `PENDING` 记录和调度索引；
7. 存储成功后，把消息加入本地内存时间轮；
8. 异步通知 Slave 执行镜像 `add`；
9. 返回客户端 `message_id` 和 `PENDING`。

**核心不变量**：**集中存储未成功，绝不能入轮，绝不能返回成功！**

### 5.2 查询链路 (Query)
任意节点均可根据 `message_id` 直接读取持久化存储并返回，无需将只读查询转发至 Master。

### 5.3 取消链路 (Cancel)
1. 读取存储中持久化的 `tw_id`，定位当前 Master；
2. 原子执行 `PENDING → CANCELLED` 条件更新；
3. 从 Master 内存时间轮中移除并清理调度索引；
4. 异步通知 Slave 移除镜像引用；
5. 若当前状态已不是 `PENDING`，直接报错拒绝。

## 6. 持久化存储与 Key 命名空间规范

### 6.1 Key 结构规范
```text
app:msg:{message_id}                         HASH/JSON，保存消息事实实体
app:tw:{tw_id}:idx:{message_id}              STRING，记录 deliver_at 调度索引
app:tw:{tw_id}:slot:{slot_key}:{shard}       SET，槽位加速恢复索引 (按需分片)
```

**设计底线**：
- 每条消息使用独立小 Key，严禁使用单一大 Hash 或大 ZSet；
- Key 带 TTL 兜底，但系统正常流转绝不能依赖 TTL；
- `loadPendingByTimeWheel` 只拉取 `PENDING` 待处理消息。

### 6.2 保留与清理策略
- 到期触发只清理当前调度索引，不删除消息实体事实；
- 可重试失败时更新 `next_attempt_at` 并创建新的重试调度索引；
- 终态记录（`DELIVERED`、`FAILED`、`CANCELLED`）默认保留 7 天排查期；
- 由后台独立清理线程按保留期执行平滑回收。

## 7. 多层时间轮调度引擎

采用四层阶梯设计：
| 层级 | 槽数 × 粒度 | 覆盖周期范围 |
|---|---|---|
| L1 (秒级) | 60 × 1 秒 | 0 ～ 60 秒 |
| L2 (分钟级) | 60 × 1 分钟 | 1 ～ 60 分钟 |
| L3 (小时级) | 24 × 1 小时 | 1 ～ 24 小时 |
| L4 (天级) | 30 × 1 天 | 1 ～ 30 天 |

**调度线程运行铁律**：
- 槽位增删推进复杂度严格为 `O(1)`；
- 采用目标时刻补偿算法推进，严禁固定 `sleep` 导致时钟误差累积；
- 调度主线程只移动引用并异步派发，绝不执行数据库、RPC 或外部网络等同步 IO；
- 节点启动或角色提升时，从持久化存储重建内存时间轮后，才允许开启就绪探针。

## 8. 集群协调与节点生命周期

### 8.1 协调元数据 Key 空间
```text
/app/nodes/{node_id}             节点租约保活信息，默认 Lease 6s
/app/workers/{worker_id}         Snowflake worker ID 唯一占位，与节点绑定同一 Lease
/app/controller                  当前集群 Controller 抢占 Key
/app/timewheels/{tw_id}          时间轮实例元数据 (Master/Slave 分布)，持久化
/app/config/*                    全局集群配置，持久化
```

### 8.2 节点生命周期管理
- 节点启动时，通过单次事务原子抢占 node ID 与 worker ID；
- 独立保活线程每 2 秒续租一次；
- 优雅停机时，先将服务标记为下线，注销租约并平滑交接时间轮实例。

### 8.3 Controller 选举与决策
- 所有节点竞争抢占 `/app/controller`，获胜者行使 Controller 编排职责；
- Controller 决策必须是无状态纯计算，结果以原子事务写入元数据，新 Controller 可无缝接管。

## 9. 副本同步与故障切换机制 (1M + 1S)

### 9.1 副本模型与 Epoch Fencing
- 每个时间轮固定 1 Master + 1 Slave，严格部署于不同物理节点；
- Master 调度投递，Slave 异步热备镜像；
- 引入 **`epoch` 递增代数**：每次主备角色变更递增 epoch，旧 Master 因 epoch 过期被自动拒绝，彻底根除双主脑裂风险。

### 9.2 故障切换 SLO (≤ 10秒)
1. 节点断网或崩溃，6 秒后 Lease 到期触发 Delete 事件；
2. Controller 监听到节点离线，原子更新时间轮元数据：提升 Slave 为新 Master，递增 epoch；
3. 新 Master 从持久化存储全量补齐 `PENDING` 消息并重建内存时间轮；
4. 10 秒内新 Master 进入调度状态，恢复业务交付；
5. Controller 在其余存活节点分配新 Slave 并触发后台同步。

## 10. 下游 Sink 插件化与重试机制

### 10.1 SPI 加载与隔离
通过标准 SPI 机制动态发现 Sink 实现，新增目标协议无需改动核心调度引擎。

### 10.2 投递重试策略
- HTTP 2xx 视为成功；4xx 客户端错误不可重试；5xx 与网络超时可重试；
- 默认采用指数退避（30s、1m、2m、4m、8m），上限 5 次；
- 重试期间原子将状态改回 `PENDING(nextAttemptAt)`，重新进入时间轮等待。

## 11. 可观测性体系

### 11.1 核心度量指标
| 指标名称 | 类型 | 监控含义 |
|---|---|---|
| `app_messages_submitted_total` | Counter | 消息提交总量 (分 sink 类型) |
| `app_sink_deliveries_total` | Counter | 投递执行总量 (按成功/失败统计) |
| `app_messages_in_state` | Gauge | 各状态消息存量水位 |
| `app_delivery_lag_seconds` | Histogram | 期望投递时间与实际投递时间延迟分布 |
| `app_sink_delivery_duration_seconds` | Histogram | 下游调用耗时分布 |
| `app_master_failover_total` | Counter | 主备故障切换次数与原因 |
| `app_controller_elections_total` | Counter | 集群选举次数 |
| `app_replica_sync_queue_size` | Gauge | 副本异步同步队列积压水位 |
| `app_replica_rebuild_total` | Counter | 副本全量重建触发次数 |
| `app_storage_operations_total` | Counter | 底层存储操作与成功率 |
| `app_coordination_operations_total`| Counter | 分布式协调操作指标 |

### 11.2 日志与健康探针
- 结构化日志必须包含：`timestamp`, `level`, `node_id`, `trace_id`, `message_id`, `tw_id`, `operation`, `status`, `duration_ms`；严禁打印明文 payload 与密钥；
- `/health` 探活；`/ready` 探针必须在初始化完成、底层依赖联通且时间轮重建就绪后才返回 200。

## 12. 部署形态与配置规范

- **三形态交付**：独立 TGZ 归档包、标准 Docker 运行时镜像、Kubernetes 高可用部署清单；
- **配置外置**：所有端口、连接凭据均通过环境变量注入，镜像内部 0 凭据硬编码。

## 13. 系统级不可动摇的不变量 (Invariants)

- **INV-01 存储优先**：存储未成功写入，绝对禁止入轮调度，绝对禁止向调用方返回成功；
- **INV-02 单向依赖**：领域模型与 SPI 绝对禁止反向依赖具体存储或传输协议实现；
- **INV-03 调度线程纯净**：时间轮推进主线程绝对禁止执行任何同步网络或磁盘 IO；
- **INV-04 异节点隔离**：同一时间轮的 Master 与 Slave 绝对禁止调度到同一物理节点；
- **INV-05 幂等与防作弊**：所有投递携带唯一 ID，下游基于 ID 幂等；严禁开发 Agent 擅自放宽门禁。

## 14. 模块结构与工程边界

| 模块名 | 核心工程职责 | 依赖方向 |
|---|---|---|
| `common` | 领域模型、枚举、公共 SPI 契约 | 底层公共，无上层依赖 |
| `api` | 外部 HTTP/RPC 协议接入与校验 | 仅依赖 `common` |
| `router` | 路由编排与 Snowflake 发号 | 依赖 `common` |
| `storage-plugin` | 集中持久化实现与恢复索引 | 实现 `common.StoragePlugin` |
| `timewheel` | 多层时间轮算法与调度引擎 | 依赖 `common` |
| `cluster` | 集群元数据、节点租约与选举 | 依赖 `common` |
| `replication` | 副本同步、主备提升与故障切换 | 依赖 `cluster`, `timewheel` |
| `sink-plugin` | 下游 HTTP / Kafka 投递插件 | 实现 `common.Sink` |
| `observability`| Prometheus 指标与结构化日志 | 贯穿各层 |
| `app` | 依赖注入装配与主服务启动入口 | 聚合各模块 |

## 15. 关键技术风险与应对策略

| 潜在风险点 | 根因机制 | 防御与自愈策略 |
|---|---|---|
| **旧 Master 恢复导致双主** | 网络假死后节点恢复 | 采用递增 Epoch Fencing，旧节点操作因 Epoch 失效被拒绝 |
| **持久化大 Key 导致性能骤降** | 聚合所有消息至单集合 | 强制单消息单小 Key，按槽位分片索引 |
| **Slave 同步落后导致数据不一致** | 异步网络抖动 | 标记 `out_of_sync`，切换时由新 Master 从持久化存储全量重建 |
| **慢下游打爆调度引擎** | 下游 HTTP 接口超时挂起 | 调度与投递物理线程池隔离，设置有界超时与线程池熔断 |
