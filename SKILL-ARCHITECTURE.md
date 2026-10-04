# Skill 架构 v0.7

## 分层

```text
Human-Agent Collaboration Layer
  ↓
项目专用 Skill / 通用 Skill
  ↓
Superpowers / Matt Skills / 其它成熟工程 Workflow
  ↓
Coding Agent / Agent Harness
```

numi-workflow 负责：

- 项目基线；
- 功能定义；
- 工作单元设计；
- 交付状态；
- 证据要求；
- 协作边界；
- 异常升级。

Coding Agent / Agent Harness 负责：

- 代码实现；
- 工具调用；
- 调试；
- 测试执行；
- 具体执行路径。

numi 不建设新的 Agent Runtime、Workflow Engine 或 Multi-Agent Scheduler。

## 当前通用 Skill

| Skill ID | 中文名称 | 主要产物 / 状态 |
|---|---|---|
| `project-discovery` | 项目基线梳理 | 项目资料索引 + 项目基线 + 按需现状事实记录 |
| `feature-shape` | 功能定义 | 功能规格 + 功能依据清单 + 按需调研证据 + 未决事项 / 决策归纳 |
| `feature-slice` | 任务拆解 | 任务依赖图 + 开发任务 + 任务上下文清单 |
| `feature-deliver` | 功能交付 | Feature 交付状态记录 + 执行证据 + 阻塞 / 升级信息 |
| `project-bootstrap` | 工程初始化 | 规划中 |
| `feature-accept` | 功能验收 | 规划中 |
| `architecture-health` | 架构健康检查 | 规划中 |
| `handoff` | 开发续接 | 独立 handoff / quota 机制；由 numi 定义所需 Feature 级上下文，不重复实现 |

v0.7 已实现 `project-discovery`、`feature-shape`、`feature-slice`、`feature-deliver` 四个核心 Skill。

## v0.6.2 保留能力：未决事项、条件性 blocker、事实闭环与决策归纳

`feature-shape` 不把问题简单归成“待确认 / 待调研 / 实现细节”，而是先明确：

```text
这个问题是什么类型？
        ↓
谁有能力把它解决？
        ↓
客观事实 → Agent 继续核实
功能 / 架构 / 风险选择 → Agent 先推荐，再由人确认
实现细节 → 延后到开发任务
        ↓
所有当前 blocker 关闭
        ↓
Gate B 可进入任务拆解
```

Research、责任归类、事实闭环循环、决策就绪检查和决策归纳都属于 `feature-shape` 内部能力，不新增新 Skill 或新 Gate。

Agent 能自行核实的当前事实 blocker 默认持续闭环；准备提交人工决定时，还要先关闭会直接改变选择且可低成本核实的前置事实。只有真正需要人的选择、客观访问限制、范围 / 成本越界或显式诊断复审才允许暂停。

## v0.7 新增能力：Feature 交付状态与证据闭环

`feature-deliver` 不负责“执行一张任务”。

它负责：

```text
已批准 Feature
      ↓
工作单元当前状态
      ↓
执行证据
      ↓
开发中新发现 / blocker
      ↓
继续 / 返回上游 / 待验收
```

核心规则：

1. 状态只描述事实，不驱动新的执行引擎；
2. 完成主张必须有证据；
3. 当前可开工任务可以展示，但不由 numi 调度 Agent；
4. 新发现先分类，再决定留在当前任务、返回 `$feature-slice`、返回 `$feature-shape` 或 Gate A；
5. `feature-deliver` 最高只推进到“待验收”；
6. 最终“已验收 / 验收失败”属于 `$feature-accept`；
7. handoff 由现有 `codex-handoff` 等机制负责，numi 只提供 Feature 级 WHAT / WHY / STATE / EVIDENCE / BOUNDARIES。

## 源码 Skill 与 Agent 实际加载 Skill 不是同一状态

`numi-workflow/skills/*` 是工作流源码。只有把对应 Skill 同步 / 安装到 Agent 实际读取的 Skill 目录后，新规则才会生效。

升级验收应检查：

1. 工作流源码仓库已经同步目标版本；
2. Agent 实际加载的 `feature-shape/SKILL.md` 包含“事实闭环循环”“诊断复审模式”“重新计算 blocker”等规则；
3. `feature-shape/agents/openai.yaml` 默认入口要求执行事实闭环；
4. `feature-slice` 能找到“不得隐式激活条件性 blocker”的规则；
5. `feature-deliver` 明确“不实现代码、不调度 Agent、不实现 handoff”，并能维护交付状态和证据；
6. `feature-deliver` 不把 Feature 直接宣告为最终验收完成。

如果 Agent 报告自己仍在使用旧规则，优先检查 Skill 安装 / 同步状态，不要先修改源码设计。

## Skill 之间的衔接

```text
$project-discovery
  → PROJECT.md
  → 规范性项目基线
  →（按需）现状事实记录

$feature-shape
  ← PROJECT.md
  ← 相关项目基线
  ←（按需）相关现状事实
  → 功能规格 + 功能依据清单
  →（按需）调研记录 / 固定测试样本
  → 未决事项表
  → 事实闭环循环
  → 决策就绪检查
  → Gate B 待确认决策（按需）

$feature-slice
  ← 已批准且“任务拆解准备状态 = 可开始”的功能规格
  → 任务依赖图
  → 开发任务
  → 任务上下文清单
  → Gate C

$feature-deliver
  ← 已批准功能规格
  ← Gate B / Gate C
  ← 任务依赖图 + 开发任务
  ← 实际执行 / 测试 / review 证据
  → 交付状态记录
  → 当前可继续工作
  → blocker / 人工判断 / 上游复审
  → 待验收
```

未来：

```text
$feature-accept
  ← 功能规格
  ← 交付状态记录
  ← 验收证据
  → 已验收 / 验收失败
```

## 默认项目布局

```text
/
├── PROJECT.md
├── CONTEXT.md
├── AGENTS.md / CLAUDE.md
├── docs/
│   ├── requirements.md
│   ├── architecture.md
│   ├── adr/
│   ├── research/
│   └── current-state/     # 仅复杂已有项目按需
└── .scratch/
    └── <feature>/
        ├── spec.md
        ├── research.md    # 按需
        ├── delivery.md    # 进入 feature-deliver 后按需建立
        └── issues/
```

不要为了工作流完整强制创建 research / current-state 文件。

`delivery.md` 也不是所有 Feature 都必须预先创建；只有进入 `$feature-deliver` 且项目没有等价交付状态文件时才使用该默认名。

## 项目专用 Skill

项目稳定后，可生成项目专用 Skill，固化真实测试 / 构建命令、模块边界、安全规则、项目特定测试边界和自动化不变量检查。

## 后续开发顺序建议

当前优先级：

1. 用真实 Feature smoke test 验证 `feature-deliver`；
2. 根据真实使用修正最小必要问题；
3. 实现 `feature-accept`；
4. 再考虑 `architecture-health`；
5. 与已有 handoff / quota hook 做接口级组合，不重建 handoff runtime。

## 中文术语

所有面向用户和 Agent 的中文说明遵循 `TERMINOLOGY.md`。
