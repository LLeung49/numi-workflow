# numi-workflow

**一个帮助人类与 Agent 长期协作完成复杂软件交付的自适应工作流。**

> 不替代 Coding Agent，而是帮助人类把复杂目标转化为 Agent
> 可以可靠完成的工作单元，并在长期协作中保持目标、上下文和决策一致性。

## 为什么需要 numi-workflow

今天 Coding Agent 已经越来越擅长：

-   编写代码
-   调用工具
-   执行测试
-   修复问题

真正困难的问题逐渐变成：

> 如何把一个长期、复杂、不确定的软件目标可靠地委托给
> Agent，而不会丢失产品意图、架构约束、历史决策和验收标准？

numi-workflow 关注的是：

    Human Intent
          ↓
    Project Understanding
          ↓
    Feature Definition
          ↓
    Executable Work Units
          ↓
    Agent Delivery
          ↓
    Evidence-based Acceptance

## 核心理念

### 1. Goal \> Conversation

重要信息不应该只存在聊天上下文。

用户应该定义：

-   目标
-   约束
-   非目标
-   验收标准
-   决策边界

Agent 负责探索、规划、实现和验证。

------------------------------------------------------------------------

### 2. Artifacts Preserve Intent

长期项目依靠工程资产保持记忆：

-   Project baseline
-   Requirements
-   Architecture
-   ADR
-   Research
-   Feature Spec
-   Task Graph
-   Execution Evidence

Agent 可以更换 session，但项目依据不能丢失。

------------------------------------------------------------------------

### 3. Human-Agent Co-evolution

numi-workflow 不只是帮助 Agent 工作。

它也帮助用户提升：

-   目标定义能力
-   任务拆解能力
-   Agent 协作能力
-   风险判断能力

随着：

-   基础模型能力提升
-   Agent Harness 能力提升
-   用户经验提升
-   项目成熟度提升

协作方式应该自适应演进。

------------------------------------------------------------------------

## numi-workflow 的边界

numi 不替代：

-   Codex
-   Claude Code
-   Gemini CLI
-   其它 Coding Agent

也不重复建设：

-   TDD
-   Code Review
-   Worktree
-   Debugging
-   Implementation workflow

这些能力优先复用成熟方案。

numi 关注：

> 人类目标与 Agent 执行之间的协作协议。

------------------------------------------------------------------------

## 核心 Skills

### project-discovery

建立长期项目依据。

解决：

> Agent 如何理解项目？

------------------------------------------------------------------------

### feature-shape

将模糊需求变成可安全实施的 Feature。

负责：

-   需求澄清
-   事实闭环
-   决策准备
-   范围定义
-   验收设计

------------------------------------------------------------------------

### feature-slice

将 Feature 转化为 Agent 可执行任务图。

保证：

-   fresh session 可接手
-   任务边界明确
-   可独立验证

------------------------------------------------------------------------

### feature-deliver

未来核心方向：

> 让 Agent
> 根据已批准目标和任务图，自主推进交付，仅在人类决策或异常时升级。

------------------------------------------------------------------------

## 与其它 Agent Workflow 的关系

numi-workflow 不竞争 Superpowers 或 Matt Pocock Skills。

关系：

    numi-workflow
    (project goals / authority / lifecycle)
                  |
                  v
    Superpowers / Matt Skills
    (implementation discipline)
                  |
                  v
    Coding Agent
    (execution)

numi 解决：

> 做什么、为什么做、什么不能改变、如何证明完成。

其它工具解决：

> 如何更高质量地实现。

------------------------------------------------------------------------

## 使用场景

适合：

-   长周期项目
-   多 session Agent 开发
-   多 Agent 协作
-   架构约束明显的项目
-   需要长期维护的软件

不适合：

-   一次性脚本
-   小型实验
-   几分钟即可完成的修改

------------------------------------------------------------------------

## 当前状态

Pre-1.0。

核心链路：

    project-discovery
            ↓
    feature-shape
            ↓
    feature-slice
            ↓
    feature-deliver
            ↓
    feature-accept

------------------------------------------------------------------------

## North Star

最终目标：

> 让人类和 Agent
> 发挥各自优势，通过持续学习和协作能力提升，以更高效率、更高质量地完成复杂目标。
