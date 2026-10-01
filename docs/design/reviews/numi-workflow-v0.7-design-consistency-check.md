# Numi Workflow v0.7 Design Consistency Check

-   版本：v0.7
-   类型：设计一致性审查结果
-   状态：Review Complete
-   目标：检查 Architecture Reset 后 README、核心原则、能力模型、Feature
    Delivery 设计是否保持一致。

------------------------------------------------------------------------

# 1. Review 结论

整体方向：

✅ 通过。

当前 v0.7 已经完成从：

    Workflow Framework

向：

    Human-Agent Collaboration Layer

的架构收敛。

核心使命一致：

> numi-workflow 不替代 Coding Agent，而是帮助人类将复杂目标转化为 Agent
> 可以可靠完成的工作单元，并在人和 Agent
> 长周期协作过程中保持目标、上下文和决策一致性。

------------------------------------------------------------------------

# 2. 已一致部分

## 2.1 README

当前 README 已正确表达：

-   Goal \> Conversation
-   Artifacts Preserve Intent
-   Human-Agent Co-evolution
-   不替代 Coding Agent
-   优先复用成熟 Workflow

无需修改。

------------------------------------------------------------------------

## 2.2 Architecture Reset

作为 v0.7 架构基线：

通过。

已明确：

-   numi 不做 Agent Runtime
-   numi 不做 Workflow Engine
-   numi 不重复实现 TDD / Review / Implementation Workflow

------------------------------------------------------------------------

# 3. 需要修改项

------------------------------------------------------------------------

# 修改 1：Capability Registry 命名收敛

## 当前问题

Architecture Reset 仍保留：

    Capability Registry

容易造成：

-   自动注册
-   动态发现
-   Agent Marketplace

的误解。

------------------------------------------------------------------------

## 修改建议

统一改为：

    Capability Catalog

定义：

Capability Catalog 用于描述：

-   已知能力；
-   能力来源；
-   复用关系；
-   适用边界。

不负责：

-   调度；
-   注册；
-   执行。

------------------------------------------------------------------------

涉及文档：

-   docs/design/workflow-capability-matrix.md
-   docs/design/workflow-capability-review.md
-   architecture-reset.md 第11节

------------------------------------------------------------------------

# 修改 2：feature-deliver 定位继续收缩

## 当前风险

README 中：

feature-deliver：

> 让 Agent 根据目标和任务图自主推进交付。

该描述容易重新引入 Agent Orchestration。

------------------------------------------------------------------------

## 修改建议

改为：

feature-deliver：

> 管理 Feature 交付过程中的状态、证据和协作边界，使人和 Agent
> 能围绕同一个交付目标持续协作。

------------------------------------------------------------------------

不负责：

-   Agent 自主规划
-   Agent 调度
-   Agent 执行

------------------------------------------------------------------------

涉及：

-   README-v1.md
-   feature-deliver-design.md

------------------------------------------------------------------------

# 修改 3：Feature State 成为 Feature-deliver 核心

Feature-deliver 应明确围绕：

    Feature Intent

    ↓

    Work Unit State

    ↓

    Execution Evidence

    ↓

    Acceptance

而不是：

    Feature

    ↓

    Agent Execution Pipeline

------------------------------------------------------------------------

# 修改 4：Capability Matrix 增加三类边界

建议统一能力分类：

  类别        说明
  ----------- -------------------------
  Numi Own    必须由 numi 提供
  Reuse       复用成熟 Agent Workflow
  Reference   仅描述，不实现

------------------------------------------------------------------------

示例：

## Numi Own

-   Project baseline
-   Feature definition
-   Decision boundary
-   Delivery state

## Reuse

-   TDD
-   Code Review
-   Worktree
-   Debugging

## Reference

-   Agent Provider
-   Execution Backend
-   Model Capability

------------------------------------------------------------------------

# 4. 建议删除或延期

以下能力不要进入 v0.7：

## Capability Runtime

延期。

------------------------------------------------------------------------

## Agent Router

延期。

------------------------------------------------------------------------

## Adaptive Workflow Engine

保留理念，不实现。

原因：

需要真实使用数据。

------------------------------------------------------------------------

# 5. v0.7 最终边界

最终架构：

    Human

    Goal
    Judgment
    Domain Knowledge

            ↓

    Numi

    Authority
    Decision
    Feature State
    Evidence

            ↓

    Agent Harness

    Codex
    Claude Code
    Superpowers
    Matt Skills

            ↓

    Execution

    Code
    Test
    Delivery

------------------------------------------------------------------------

# 6. 下一步执行顺序

建议：

1.  合并本文档建议修改；
2.  冻结 v0.7 Architecture Boundary；
3.  开始 feature-deliver MVP 实现；
4.  实际使用后再决定 v0.8 是否增加能力。

------------------------------------------------------------------------

# 总结

当前最大价值：

不是增加更多 Workflow。

而是建立一个稳定边界：

> Numi 管理"人为什么让 Agent 做这件事，以及如何确认它正确完成"。

Agent Harness 管理：

> Agent 如何执行这件事。
