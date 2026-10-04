# Numi Workflow

## 总体流程

numi-workflow 不是固定 SOP，也不是 Workflow Engine。

它是一套根据目标、风险、项目状态、Agent 能力和用户能力动态调整的 Human-Agent 协作框架。

典型协作链路：

```text
Idea
 ↓
Project Discovery
 ↓
Gate A
 ↓
Feature Shape
 ↓
Gate B
 ↓
Feature Slice
 ↓
Gate C
 ↓
Feature Deliver
 ↓
Feature Accept
```

这些阶段表示**协作状态与责任边界**，不是要求所有任务无条件逐步执行的固定流程。

## Phase 1: Project Discovery

目标：

建立项目长期依据和 Human-Agent 共享世界模型。

输出：

- 项目目标；
- 架构背景；
- 约束；
- 领域知识；
- 按需现状事实记录。

核心问题：

> Agent 是否理解项目为什么存在，以及以后应该去哪里查长期依据？

## Phase 2: Feature Shape

目标：

把模糊需求变成边界清楚、可验收、可决策的 Feature。

### Fact Closure

客观事实优先由 Agent 核实。

例如：

- API 行为；
- 数据格式；
- 当前代码事实；
- 技术限制。

Agent 可以在当前授权、范围和合理成本内闭环的事实，不应直接抛给用户拍板。

### Decision Readiness

用户主要处理：

- 产品取舍；
- 风险授权；
- 架构方向。

在提交人工判断前，Agent 应先核实会直接改变决策的低成本客观事实。

## Phase 3: Feature Slice

目标：

生成 Agent 可以可靠完成的工作单元。

每个任务应：

- 有明确交付；
- 有验证方式；
- 可由 fresh session 接手；
- 有停止与升级边界；
- 不隐式扩大已经批准的 Feature 范围。

这里形成的是**工作单元和依赖关系**，不是 Agent 调度计划。

## Phase 4: Feature Deliver

目标：

> 管理 Feature 交付过程中的状态、证据和协作边界，而不是控制 Agent 如何执行。

核心模型：

```text
Approved Feature Intent
        ↓
Work Unit State
        ↓
Execution Evidence
        ↓
Delivery Boundary Check
        ↓
Ready for Acceptance
```

`feature-deliver` 负责：

- 维护 Feature / 开发任务状态；
- 记录与核对执行证据；
- 展示当前可继续的工作；
- 暴露阻塞；
- 对开发中新发现进行分类；
- 判断何时需要返回 `$feature-shape` / `$feature-slice` / Gate A；
- 提供 handoff 工具所需的 Feature 级最小上下文。

`feature-deliver` 不负责：

- 写代码；
- 调度 Agent；
- 自动启动 subagent；
- 选择模型；
- 选择执行后端；
- 管理 worktree；
- 实现 session handoff。

执行方式交给当前 Coding Agent、Agent Harness 和被复用的成熟工程 Workflow。

### 异常升级

正常实现问题尽量在已批准边界内由 Agent 解决。

只有例如以下情况才升级人工或返回上游：

- 新产品行为选择；
- Feature 范围或验收标准变化；
- 跨 Feature 架构边界变化；
- 新风险取舍；
- 高风险 / 不可逆操作；
- 条件性 blocker 被实际激活；
- 任务拆解已经无法准确表达剩余工作。

原则：

> 升级例外，不升级日常执行。

### 交付终点

`feature-deliver` 不做最终功能验收。

当当前范围内工作单元都具备充分证据、没有未分类发现或未解决阻塞时：

```text
Feature 状态 = 待验收
```

随后进入 `feature-accept`。

## Phase 5: Feature Accept

目标：

验证 Feature 是否真正完成。

不是只检查：

> 代码是否通过测试？

而是检查：

- 是否满足原始目标；
- 是否满足验收标准；
- 是否保持项目约束；
- 执行证据是否充分；
- 是否存在未处理风险或偏差。

最终的“已验收 / 验收失败”属于这一阶段，而不是 `feature-deliver`。

## Handoff 的位置

Numi 与 handoff 工具职责互补：

```text
feature-deliver
WHAT / WHY / STATE / EVIDENCE / BOUNDARIES
        ↓
codex-handoff / 其它 handoff 工具
CONTEXT TRANSFER / SESSION CONTINUITY
        ↓
Coding Agent
```

numi 不重复实现已有 handoff 机制。

## 自适应协作

numi 不追求固定流程。

协作策略取决于：

```text
Agent capability
Harness capability
Project maturity
User maturity
Task complexity
Risk level
```

例如：

新项目或高风险 Feature：

```text
更多基线确认
更多事实核实
更小工作单元
更强证据要求
```

成熟项目或低风险 Feature：

```text
目标授权
更少人工打断
Agent 在已批准边界内执行
异常升级
```

## 用户能力成长

numi 的长期目标不是培养用户写更多 Prompt。

而是培养：

- 定义目标；
- 描述边界；
- 建立验收；
- 管理风险；
- 判断什么该授权、什么必须亲自决策；
- 与不断进化的 Agent Harness 协作。

最终从：

```text
Prompting
```

成长到：

```text
Delegation
```

## 设计原则

### 复用优先

已有成熟能力优先集成或复用，不在 numi 内重新实现。

### 简单优先

不要为了未来可能性提前建设 Runtime、Router、Marketplace 或复杂状态机。

### 证据驱动

完成与自主权都建立在可追溯证据上，而不是 Agent 自我声明。

### 状态优于对话

长期项目的重要状态不能只存在聊天记录中。

### 边界优于自动化

先明确“什么可以做、什么时候必须停”，再考虑增加自动化程度。

### 异常升级

不要让人工管理正常执行流程；人工关注真正需要判断的问题。
