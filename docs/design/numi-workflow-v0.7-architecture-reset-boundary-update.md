# Architecture Reset v0.7 边界修订

## 1. Capability Catalog

将：

Capability Registry

统一调整为：

Capability Catalog。

原因：

Registry 容易暗示：

-   动态注册；
-   自动发现；
-   调度管理。

这些不是 numi v0.7 目标。

------------------------------------------------------------------------

## Capability Catalog 定义

用于描述：

-   已知能力；
-   能力来源；
-   使用边界；
-   复用关系。

不负责：

-   Agent 调度；
-   Provider 路由；
-   自动执行。

------------------------------------------------------------------------

## 2. Feature-deliver 边界补充

Feature-deliver 不决定 Agent 的执行路径。

它负责维护：

-   Feature 状态；
-   工作单元状态；
-   执行证据；
-   验收状态；
-   人工介入边界。

------------------------------------------------------------------------

## 3. 架构原则

Numi 管：

    Why
    What
    Boundary
    Evidence

Agent Harness 管：

    How
    Execution
    Tool Usage
    Implementation
