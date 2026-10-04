# Feature Deliver Design v0.7 Final

- 版本：v0.7
- 状态：冻结
- 类型：能力设计

## 定位

feature-deliver 不负责让 numi 自动完成开发。

它负责：

> 管理 Feature 从开始交付到进入功能验收之前的状态、证据和协作边界。

feature-deliver 是 **Feature 交付状态协议**，不是：

- Agent Runtime；
- Workflow Engine；
- 自动执行系统；
- session handoff 工具。

## 1. 核心模型

```text
Feature Intent
      ↓
Work Unit State
      ↓
Execution Evidence
      ↓
Delivery Boundary
      ↓
Ready for Acceptance
```

它管理交付事实，但不决定 Agent 的具体执行路径。

## 2. Feature State

维护：

- 当前阶段；
- 已完成工作单元；
- 当前可继续工作；
- 阻塞事项；
- 验证状态；
- 需要人工判断或上游复审的变化。

Feature 层状态：

```text
可开始
交付中
验证中
阻塞
待验收
```

`feature-deliver` 不拥有最终“已验收 / 验收失败”状态。

最终功能验收属于未来 `$feature-accept`。

开发任务本身可以进入“已完成”，但必须有与任务验收标准匹配的证据。

## 3. Evidence Model

完成不能只依赖 Agent 声明。

遵循：

```text
Claim
  ↓
Evidence
  ↓
Acceptance Readiness
```

证据可以包括：

- 代码变更；
- 测试结果；
- Review 记录；
- 运行结果；
- 生成产物。

原则：

> Evidence over Assertion。

需要同时区分：

- 已证明；
- 尚未验证；
- 明确不在当前范围。

## 4. Delivery Boundary

feature-deliver 负责判断当前变化属于哪一层：

### 当前任务内

不改变已批准行为、公共接口或长期架构边界：

> 留在当前执行范围处理。

### 任务拆解变化

功能规格仍然有效，但任务粒度、依赖或工作单元需要变化：

> 返回 `$feature-slice`。

### 功能定义变化

范围、用户可观察行为、验收标准、功能决策或风险边界发生变化：

> 返回 `$feature-shape`。

### 项目基线 / 长期架构变化

跨 Feature 公共架构、长期工程规范或项目级不变量需要变化：

> 返回 Gate A / ADR。

原则：

> feature-deliver 维护边界，不静默改写上游产物。

## 5. 交付状态记录

v0.7 使用轻量的人机可读状态记录。

默认可以使用：

```text
delivery.md
```

如果项目已经有等价文件，优先复用。

记录：

- Feature 当前状态；
- 开发任务状态；
- 当前可继续工作；
- 完成证据；
- blocker；
- 开发中新发现；
- 人工判断点；
- 上游复审需求；
- handoff 所需 Feature 级最小上下文。

不引入：

- 数据库状态；
- Runtime scheduler；
- Provider registry；
- 自动 Agent routing。

## 6. Handoff Boundary

feature-deliver 不实现 handoff。

它只提供：

```text
WHAT
WHY
STATE
EVIDENCE
BOUNDARIES
```

例如：

- Feature 状态；
- 当前工作单元；
- 已批准决定；
- 已完成工作和证据；
- 剩余工作；
- 当前阻塞；
- 不可越过的边界。

具体上下文转移：

由 `codex-handoff` 或其它 handoff 工具负责。

## 7. 与执行 Workflow 的边界

feature-deliver 不重新实现：

- TDD；
- Debug；
- Worktree；
- Code Review；
- 代码实现；
- Agent 调度。

成熟执行能力由 Superpowers、Matt Pocock Skills、Coding Agent 或 Agent Harness 提供。

numi 维护：

> 为什么做、做到哪里、证据是什么、什么时候必须停下来升级。

## 8. 明确不做

v0.7 不实现：

- Agent 调度；
- 多 Agent 编排；
- 自动选择执行模型；
- 自动选择执行后端；
- Workflow Runtime；
- Capability Marketplace；
- session handoff；
- 最终功能验收。

## 9. MVP 范围

v0.7 实现：

- Feature State Tracking；
- Evidence Collection；
- Delivery Boundary；
- Handoff Contract；
- Acceptance Readiness。

目标是建立最小 Feature 交付闭环，而不是自动执行系统。
