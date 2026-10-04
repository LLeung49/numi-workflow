# Feature Deliver Skill Design

-   版本：v0.7
-   状态：设计稿
-   类型：Skill 设计文档

## 1. Skill 定位

Feature-deliver skill 是 numi-workflow 中负责 Feature
交付状态管理与协作边界维护的能力。

它不负责： - 替代 Coding Agent； - 执行代码开发； - 调度 Agent； - 规划
Agent 执行路径。

它负责：

> 让人类和 Agent 围绕同一个 Feature 状态、目标和完成证据持续协作。

------------------------------------------------------------------------

## 2. 与其它 Skill 的关系

    project-discovery

    回答：
    项目是什么？

    ↓

    feature-shape

    回答：
    要做什么？
    为什么做？
    边界是什么？

    ↓

    feature-slice

    回答：
    如何拆成 Agent 可执行工作单元？

    ↓

    feature-deliver

    回答：
    当前交付状态是什么？
    是否有证据证明完成？
    是否需要人工介入？

------------------------------------------------------------------------

## 3. 触发条件

Feature-deliver 适用于：

-   Feature 已完成 feature-shape；
-   范围已经确认；
-   已存在可执行工作单元；
-   进入执行或验证阶段。

不应该用于：

-   需求澄清；
-   范围设计；
-   任务拆解；
-   代码调试。

------------------------------------------------------------------------

## 4. 输入契约

需要：

``` yaml
feature:
  id:
  title:
  objective:
  acceptance:

work_units:
  - id:
    description:
    status:

decisions:
  - decision:
    rationale:
```

缺少关键输入时：

-   标记缺失；
-   请求补充；
-   返回上游 skill。

------------------------------------------------------------------------

## 5. 核心能力

### Feature State Tracking

维护 Feature 状态：

    planned
     ↓
    ready
     ↓
    implementing
     ↓
    verifying
     ↓
    completed

异常：

    blocked
    need-human-decision

------------------------------------------------------------------------

### Evidence Collection

遵循：

    Claim
     ↓
    Evidence
     ↓
    Acceptance

证据包括：

-   测试结果；
-   代码变更；
-   Review 记录；
-   文档；
-   运行结果。

------------------------------------------------------------------------

### Delivery Boundary

Feature-deliver 判断：

是否可以继续推进。

允许：

-   已批准范围内实现。

阻止：

-   修改产品目标；
-   修改架构边界；
-   新增未批准约束。

需要时返回：

    feature-shape

------------------------------------------------------------------------

### Handoff Contract

Feature-deliver 不实现 handoff。

只定义：

    Feature Context
    +
    Current State
    +
    Completed Work
    +
    Evidence
    +
    Remaining Work
    +
    Decisions

上下文迁移由 codex-handoff 等工具负责。

------------------------------------------------------------------------

## 6. 行为规则

### MUST

必须：

-   保持 Feature 目标；
-   维护状态；
-   记录证据；
-   暴露阻塞；
-   标识人工决策点。

### MUST NOT

禁止：

-   修改 Feature 目标；
-   自主扩大范围；
-   选择模型；
-   调度 Agent；
-   替代 Coding Agent；
-   创建 Workflow Runtime。

------------------------------------------------------------------------

## 7. 输出契约

输出：

Feature Delivery Status

包含：

-   Current State
-   Completed
-   Evidence
-   Blockers
-   Human Decision Required
-   Next Recommended Action

------------------------------------------------------------------------

## 8. 与 codex-handoff 边界

    feature-deliver

    负责：
    WHAT
    WHY
    STATE
    EVIDENCE

    ↓

    codex-handoff

    负责：
    CONTEXT TRANSFER
    SESSION CONTINUITY

------------------------------------------------------------------------

## 9. MVP 范围

实现：

-   Feature State；
-   Evidence Model；
-   Delivery Boundary；
-   Handoff Contract。

不实现：

-   Agent Runtime；
-   Workflow Engine；
-   Multi Agent Orchestration；
-   Capability Marketplace。

------------------------------------------------------------------------

## 10. 设计原则

### Evidence over Assertion

完成必须有证据。

### State over Conversation

长期状态不能只存在聊天记录。

### Boundary over Automation

明确边界比无限自动化更重要。

### Collaboration over Replacement

numi 的目标是提升 Human-Agent 协作，而不是替代 Agent。
