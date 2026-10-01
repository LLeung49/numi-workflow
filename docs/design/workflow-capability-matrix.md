# Workflow Capability Matrix v0.7 修订建议

## 修订目标

基于 Numi Workflow v0.7 Architecture Reset，重新收敛能力边界，避免 numi
演化为 Agent Runtime。

核心原则：

> numi 负责 Human-Agent Collaboration，不负责替代成熟 Agent Harness。

------------------------------------------------------------------------

# 1. Capability Registry 调整

## 原定位

Workflow Capability → Provider → Execution Backend

## 问题

容易演化为：

-   Agent Marketplace
-   Provider Router
-   自动调度系统

这些不是 numi 当前目标。

## 修订

Capability Registry 降级为：

# Capability Catalog

职责：

-   描述能力边界；
-   记录已有能力来源；
-   指导复用策略。

不负责：

-   自动注册；
-   自动发现；
-   动态调度。

------------------------------------------------------------------------

# 2. Execution Backend 调整

## 原定位

numi 负责执行后端选择。

## 修订

改为：

# Execution Compatibility Declaration

numi 描述：

-   需要什么执行能力；
-   当前环境有哪些可用能力；
-   验证交付需要什么条件。

执行主体选择由：

-   用户；
-   Coding Agent；
-   Harness。

负责。

------------------------------------------------------------------------

# 3. 核心 Own 能力

numi Own：

-   项目基线；
-   长期项目依据；
-   决策边界；
-   Feature 定义；
-   工作单元设计；
-   交付状态；
-   证据可信度。

------------------------------------------------------------------------

# 4. 明确 Reuse

复用：

-   TDD；
-   Code Review；
-   Debug；
-   Worktree；
-   Implementation Workflow。

来源：

-   Superpowers；
-   Matt Pocock Skills；
-   Coding Agent。

------------------------------------------------------------------------

# 5. v0.7 原则

保持简单：

> 描述协作能力，而不是控制执行能力。
