# README v0.7 对齐修订建议

## 修改目标

根据 Numi Workflow v0.7 Architecture Reset，进一步收敛 README 中关于
feature-deliver 和 workflow 的描述。

核心原则：

> numi-workflow 不替代 Coding Agent，而是帮助人类与 Agent
> 建立可靠的长期协作方式。

------------------------------------------------------------------------

# Feature-deliver 定位修订

## 原描述风险

如果描述为：

> 让 Agent 根据已批准目标和任务图自主推进交付。

容易被理解为：

-   Agent Orchestrator
-   自动执行引擎
-   Workflow Runtime

这与 v0.7 架构边界不一致。

------------------------------------------------------------------------

## 新描述

Feature-deliver：

> 管理 Feature 交付过程中的状态、证据和协作边界，使人和 Agent
> 能围绕同一个交付目标持续协作。

负责：

-   Feature 状态；
-   完成证据；
-   验收状态；
-   异常升级。

不负责：

-   Agent 调度；
-   执行路径规划；
-   模型选择。

------------------------------------------------------------------------

# 其它内容

README 当前：

-   Human-Agent Co-evolution；
-   Goal \> Conversation；
-   Artifacts Preserve Intent；
-   复用成熟 Agent Workflow；

均符合 v0.7 原则，无需调整。
