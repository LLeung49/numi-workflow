# numi-workflow

**一个帮助人类与 Agent 长期协作完成复杂软件交付的自适应工作流。**

> 不替代 Coding Agent，而是帮助人类把复杂目标转化为 Agent 可以可靠完成的工作单元，并在长期协作中保持目标、上下文和决策一致性。

## 为什么需要 numi-workflow

今天 Coding Agent 已经越来越擅长：

- 编写代码；
- 调用工具；
- 执行测试；
- 修复问题。

真正困难的问题逐渐变成：

> 如何把一个长期、复杂、不确定的软件目标可靠地委托给 Agent，而不会丢失产品意图、架构约束、历史决策和验收标准？

numi-workflow 关注的是：

```text
Human Intent
      ↓
Project Understanding
      ↓
Feature Definition
      ↓
Executable Work Units
      ↓
Delivery State & Evidence
      ↓
Evidence-based Acceptance
```

## 核心理念

### 1. Goal > Conversation

重要信息不应该只存在聊天上下文。

用户应该定义：

- 目标；
- 约束；
- 非目标；
- 验收标准；
- 决策边界。

Agent 负责探索、实现和验证；当问题超出已批准边界时，再升级给人判断。

### 2. Artifacts Preserve Intent

长期项目依靠工程资产保持记忆：

- Project baseline；
- Requirements；
- Architecture；
- ADR；
- Research；
- Feature Spec；
- Task Graph；
- Delivery State；
- Execution Evidence。

Agent 可以更换 session，但项目依据、当前状态和完成证据不能只留在旧对话中。

### 3. Human-Agent Co-evolution

numi-workflow 不只是帮助 Agent 工作。

它也帮助用户持续提升：

- 目标定义能力；
- 任务拆解能力；
- Agent 协作能力；
- 风险判断能力。

随着：

- 基础模型能力提升；
- Agent Harness 能力提升；
- 用户经验提升；
- 项目成熟度提升；

协作方式应该自适应演进，而不是永久固定在同一套流程上。

## numi-workflow 的边界

numi 不替代：

- Codex；
- Claude Code；
- Gemini CLI；
- 其它 Coding Agent。

也不重复建设：

- TDD；
- Code Review；
- Worktree；
- Debugging；
- Implementation Workflow；
- Agent Runtime；
- Multi-Agent Orchestration；
- session handoff 工具。

这些能力优先复用成熟方案。

numi 关注：

> 人类目标与 Agent 执行之间的协作协议：做什么、为什么做、什么不能改变、当前做到哪里、以及如何证明已经完成。

## 核心 Skills

### project-discovery

建立长期项目依据。

解决：

> Agent 如何理解项目？

主要产物：

- 项目资料索引；
- 项目基线；
- 按需现状事实记录。

### feature-shape

将模糊需求变成可安全实施的 Feature。

负责：

- 需求澄清；
- 事实闭环；
- 决策就绪；
- 范围定义；
- 验收设计。

解决：

> 这次究竟要改变什么、为什么做、边界是什么？

### feature-slice

将已批准 Feature 转化为 Agent 可执行工作单元。

保证：

- fresh session 可接手；
- 任务边界明确；
- 可独立验证；
- 不依赖隐藏聊天上下文。

解决：

> 如何把 Feature 拆成 Agent 可以可靠完成的工作单元？

### feature-deliver

管理 Feature 交付过程中的状态、证据和协作边界，使人和 Agent 能围绕同一个交付目标持续协作。

负责：

- Feature / 开发任务状态；
- 执行证据；
- 当前阻塞；
- 开发中新发现的分类；
- 需要人工判断或返回上游复审的变化；
- handoff 工具所需的 Feature 级最小上下文。

不负责：

- 代码实现；
- Agent 调度；
- 执行路径规划；
- 模型选择；
- session handoff。

解决：

> 当前交付到哪里、为什么认为某项工作完成、下一步可以继续什么、什么时候必须升级？

### feature-accept

规划中的功能验收 Skill。

目标：

> 基于原始目标、验收标准、项目约束和实际证据判断 Feature 是否真正完成。

`feature-deliver` 最多把 Feature 推进到“待验收”；最终验收状态属于 `feature-accept`。

## 与其它 Agent Workflow 的关系

numi-workflow 不竞争 Superpowers 或 Matt Pocock Skills。

关系：

```text
numi-workflow
(project goals / decisions / state / evidence)
              |
              v
Superpowers / Matt Skills / Agent Harness
(implementation discipline / execution)
              |
              v
Coding Agent
(code / test / tools)
```

numi 解决：

> 做什么、为什么做、什么不能改变、当前做到哪里、如何证明完成。

其它工具解决：

> 如何更高质量地实现。

## 使用场景

适合：

- 长周期项目；
- 多 session Agent 开发；
- 多 Agent / 多工具协作；
- 架构约束明显的项目；
- 需要长期维护的软件。

不适合：

- 一次性脚本；
- 小型实验；
- 几分钟即可完成、几乎没有上下文连续性风险的修改。

## 当前状态

Pre-1.0。

当前已实现的核心链路：

```text
project-discovery
        ↓
feature-shape
        ↓
feature-slice
        ↓
feature-deliver
```

后续：

```text
feature-accept
architecture-health
```

其中 Workflow 阶段代表**协作状态**，不是强制所有任务机械经过的固定 SOP。

## North Star

最终目标：

> 让人类和 Agent 发挥各自优势，通过持续学习和协作能力提升，以更高效率、更高质量地完成复杂目标。

进一步说：

> 帮助人类成为能够驾驭长期 Agent 协作的新型工程师。
