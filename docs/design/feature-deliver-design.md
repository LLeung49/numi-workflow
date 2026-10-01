# Feature Deliver Design v0.7 Revision

## 定位

feature-deliver 不负责让 numi 自动完成开发。

它负责：

> 管理 Feature 从目标确认到交付完成过程中的状态、证据和协作边界。

------------------------------------------------------------------------

# 1. 核心模型

Feature Delivery =

    Feature Intent

    ↓

    Task State

    ↓

    Execution Evidence

    ↓

    Acceptance Status

------------------------------------------------------------------------

# 2. Feature State

维护：

-   当前阶段；
-   已完成事项；
-   阻塞事项；
-   验证状态。

示例：

    planned
    ready
    executing
    blocked
    review
    accepted
    done

------------------------------------------------------------------------

# 3. Evidence Model

完成不能只依赖 Agent 声明。

需要：

-   代码变更；
-   测试结果；
-   Review 记录；
-   运行结果；
-   生成产物。

原则：

> Evidence over Assertion。

------------------------------------------------------------------------

# 4. Handoff Boundary

feature-deliver 不实现 handoff。

它只定义：

交接需要包含：

-   Feature 状态；
-   当前任务；
-   决策约束；
-   未完成事项；
-   验收要求。

具体上下文转移：

由 codex-handoff 等工具负责。

------------------------------------------------------------------------

# 5. 明确不做

v0.7 不实现：

-   Agent 调度；
-   多 Agent 编排；
-   自动选择执行模型；
-   Workflow Runtime。

------------------------------------------------------------------------

# 6. MVP 范围

v0.7：

实现 Feature 状态闭环。

不实现：

自动执行系统。
