# numi-workflow

**A goal-driven, artifact-grounded and adaptive workflow for long-running agentic software development.**

> Define outcomes, not conversations.  
> Preserve intent in artifacts, not chat history.  
> Grow autonomy with evidence and capability.  
> Escalate exceptions, not routine work.

`numi-workflow` 是一套面向长期、复杂软件项目的 Agentic Software Engineering Workflow。

它不是另一个“如何提示 Coding Agent 写代码”的 Prompt 集合，也不试图规定唯一正确的软件开发流程。

它关注的问题是：

> 当 AI Agent 可以持续工作几十分钟、数小时，甚至跨多个 session、多个 subagent 完成软件开发时，我们如何让它长期保持对目标、产品边界、架构约束和历史决策的正确理解，并尽可能自主完成交付？

---

# Why numi-workflow?

今天的 Coding Agent 已经越来越擅长写代码。

真正困难的问题正在从：

```
How do I make the model write this code?
```

转向：

```
How do I delegate an engineering goalwithout losing intent, architecture,evidence and control over time?
```

长程 Agent 开发中常见的问题包括：

```
需求只存在于聊天里        ↓context 被压缩 / session 被替换        ↓Agent 重新解释目标        ↓局部实现看似合理        ↓Feature 行为逐渐漂移        ↓架构和工程约束被悄悄侵蚀
```

更多 Prompt 并不能根治这些问题。

`numi-workflow` 的基本判断是：

> **长期 Agent 开发首先是 delegation、authority 和 state management 问题，其次才是 coding 问题。**

---

# Design Philosophy

## 1. Define outcomes, not conversations

用户不应该需要设计 Agent 下一轮说什么。

更成熟的协作方式是定义：

```
GoalConstraintsNon-goalsAcceptance criteriaDecision authorityRisk boundary
```

然后让 Agent 自主决定达到目标所需要的研究、规划、实现、验证和修复步骤。

目标是从：

```
“做这个”“继续”“不是这样”“再改一下”
```

逐渐走向：

```
“这是目标、边界和完成条件。已有原则覆盖的事情你自己处理。真正需要我判断时再来找我。”
```

---

## 2. Goal > Plan

Plan 是实现 Goal 的当前最佳假设。

它不是法律。

Ticket、Task Graph、执行顺序都可以随着实现证据变化而调整，只要没有静默改变：

- 已批准功能行为；

- 产品取舍；

- 架构边界；

- 风险授权；

- 项目 invariant。

因此：

```
Goal  ↓Plan  ↓Evidence  ↓Plan may change  ↓Goal remains
```

---

## 3. Artifacts preserve intent

重要信息不应只存在于 Agent 的 context window。

numi-workflow 将长期知识、Feature 决策和执行状态外化为工程资产：

```
PROJECT.mdCONTEXT.mdrequirementsarchitectureADRresearchfeature spectask graphexecution evidencecurrent-state records
```

Agent 可以失去聊天上下文。

项目不能失去自己的记忆。

---

## 4. Facts and decisions are different

Agent 不应该因为一个事实暂时不知道，就问用户：

> “你希望 API 的真实行为是什么？”

numi-workflow 区分：

```
Objective fact→ Agent investigatesProduct decision→ Agent analyses + user decides when necessaryArchitecture decision→ project authority / Gate ARisk trade-off→ evidence + human judgment when requiredImplementation detail→ implementation Agent decides
```

能够客观核实的问题，应尽量由 Agent 自己闭环。

---

## 5. Decisions must be ready before humans are interrupted

即使一个问题最终属于用户决策，也不意味着应该立即打断用户。

在提交 D/A/R 决策之前，Agent 应先检查：

> 是否仍有低成本、直接影响该决定、且可能改变推荐结果的客观事实没有核实？

如果有：

```
Fact Closure    ↓recompute    ↓Decision Readiness    ↓human decision
```

人应该判断价值和风险，而不是替 Agent 完成事实调查。

---

## 6. Gates are state conditions, not ceremonies

numi-workflow 使用 Gate 表示关键状态是否成立：

```
Gate A — Project baseline readyGate B — Feature definition readyGate C — Delivery graph ready
```

Gate 并不天然意味着：

> “必须停下来等人点一次批准。”

在当前能力或高风险场景下，显式人工 Gate 很有价值。

随着 Agent、Harness、项目和用户成熟，一部分 Gate 可以自动满足。

稳定的是**状态条件**，不是固定仪式。

---

## 7. Progressive Autonomy

numi-workflow 不追求一次性“全自动开发”。

它追求：

> **Autonomy grows with evidence and capability.**

自主程度应同时取决于：

```
Base model capability        +Agent harness capability        +Project maturity        +Risk level        +User collaboration maturity
```

一个全新项目和一个经过大量测试、ADR 完整、契约稳定的成熟项目，不应该使用完全相同的人工介入频率。

---

## 8. Escalate exceptions, not routine work

正常任务完成不应该成为人工 interruption。

理想模式：

```
approved goal    ↓Agent autonomous loop    ↓planactverifyrepaircontinue    ↓goal satisfied
```

只有出现真正的例外：

```
new product decisionarchitecture boundary changeunresolved high-impact factnew risk trade-offirreversible actioncontract conflict
```

才升级给人。

---

# From Prompting to Delegation

numi-workflow 不只指导 Agent。

它也帮助用户逐渐学会如何与 Agent 合作。

```
Level 0 — Prompting“帮我做这个功能。”
```

```
Level 1 — Tasking“实现这个 API，满足这些测试。”
```

```
Level 2 — Goal Setting“完成这个业务结果，保持这些约束，达到这些验收标准。”
```

```
Level 3 — Delegation“这是目标、项目依据和风险边界。已有规则覆盖的决策自行处理。真正需要产品判断时再升级。”
```

```
Level 4 — SupervisionHuman:product judgmentarchitecture directionrisk / strategyexception handlingAgent:researchplanningimplementationtestingrepairdelivery
```

未来优秀的软件工程师不只是“更会 Prompt”。

他们需要更擅长定义：

> **What outcome matters, what must remain true, and how completion can be proven.**

---

# Lifecycle

完整生命周期设计为：

```
IDEA ↓DISCOVERY / GRILLING ↓PROJECT KNOWLEDGE ↓Gate A ↓PROJECT READY ↓FEATURE REQUIREMENT ↓FEATURE SHAPE ↓Gate B ↓FEATURE SLICE ↓Gate C ↓FEATURE DELIVER ↓FEATURE ACCEPT ↓ARCHITECTURE HEALTH
```

当前稳定核心主要集中在前半段。

---

# Current Skills

## `$project-discovery`

从模糊项目想法建立长期项目依据。

目标不是立即写代码，而是建立未来 Agent 都可以依赖的：

```
requirementsarchitectureconstraintsADRsproject knowledge
```

输出进入 Gate A。

---

## `$feature-shape`

把一个 Feature 从需求推进成可以安全实施的功能规格。

它负责：

- requirement clarification；

- research；

- unresolved-item classification；

- Fact Closure Loop；

- Decision Readiness；

- test seam；

- acceptance criteria；

- scope / non-goals；

- Gate B readiness。

核心规则：

> Gate B 不应该暴露 Agent 自己尚未完成的作业。

---

## `$feature-slice`

把已经批准的 Feature Spec 切成可执行 task graph。

任务应该是：

- observable outcome；

- fresh-session executable；

- bounded；

- explicit dependencies；

- explicit stop conditions；

- linked project context；

- independently verifiable。

Gate C 批准的是整个 delivery graph，而不是“批准下一张 ticket”。

---

## `$feature-deliver`

**Next major milestone.**

目标不是简单地：

```
for ticket in tickets:    run agent
```

而是实现：

```
approved task graph      ↓compute ready frontier      ↓dispatch independent agents      ↓validate evidence      ↓integrate      ↓update graph      ↓recompute frontier      ↓continue until goal or escalation
```

正常 ticket 完成不需要人工批准。

人只处理例外。

---

## `$feature-accept`

Planned.

负责 Feature-level acceptance，而不是重复 ticket-level tests。

它需要重新核对：

```
approved spec+acceptance criteria+task evidence+final implementation+review findings+development discoveries
```

并判断：

```
AcceptedConditionalRejected / return to deliveryReturn to feature-shape
```

---

## `$architecture-health`

Planned.

长期项目不能只靠单个 Feature 正确。

当大量 Agent-generated changes 累积以后，需要定期检查：

- module boundaries；

- duplicated concepts；

- accidental coupling；

- architecture drift；

- obsolete ADR；

- missing current-state records；

- accumulated technical debt。

---

# How numi-workflow differs from other Agentic Coding workflows

numi-workflow 并不试图替代所有现有工程 Skills。

相反，它希望成为这些能力之上的**项目级 orchestration / governance layer**。

以下比较的是代表性设计方向，而不是“谁更好”的排名。

---

## Superpowers

[Superpowers](https://github.com/obra/superpowers) 强调严格的软件工程纪律。

其 Basic Workflow 包括 brainstorming、worktree、writing plans、subagent / plan execution、TDD、code review 和 branch finishing。

它尤其擅长解决：

> Agent 已经知道要做什么以后，怎样更可靠地把软件做出来。

典型优势：

```
strong implementation disciplineTDDworktree isolationfrequent reviewexplicit implementation plansfresh subagents
```

numi-workflow 与它并不冲突。

更准确的关系可以理解为：

```
numi-workflowproject / feature authoritydecision boundariesgoal & lifecycle orchestration        ↓implementation discipline        ↓Superpowers-like execution practices
```

numi-workflow 可以继续借鉴 Superpowers：

- worktree isolation；

- RED-GREEN-REFACTOR；

- systematic code review；

- branch close-out；

- verification-before-completion。

---

## Matt Pocock Skills

[Matt Pocock Skills](https://github.com/mattpocock/skills) 更强调小而可组合、由开发者主动调用的工程 Skills。

当前主链包括：

```
grill-with-docs    ↓to-spec    ↓to-tickets    ↓implement    ↓code-review
```

`to-spec` 把前面已经形成的决定固化成可以跨 context window 使用的 Spec；`to-tickets` 会把 Spec 切成 tracer-bullet tickets，并要求每张 ticket 能被 fresh session 独立完成。

当前仓库还包含正在发展的 `implement-spec`：它直接读取 task graph，在 ready frontier 上并行启动 implementer subagents，并通过独立 worktree 和 merger agent 汇总到 integration branch。

这一方向与 numi-workflow 的未来 `$feature-deliver` 已经非常接近。

因此 numi-workflow 的差异**不是**：

> “我们有 tickets、fresh sessions 和 frontier，别人没有。”

真正关注点更偏上层：

```
long-lived project authorityproject vs feature vs implementation decisionsFact ClosureDecision ReadinessGate state semanticsconditional blockersevidence-driven escalationadaptive autonomyproject lifecycle continuity
```

Matt Skills 值得继续借鉴：

- lightweight composability；

- `grill-*` interaction design；

- domain modeling；

- fresh-session tickets；

- task graph frontier；

- worktree-based parallel delivery；

- retrospective / environment improvement。

---

# Different layers, different strengths

一个粗略的理解：

```
                     Long-lived Project                            │                            │                     numi-workflow              authority / lifecycle / goals                gates / evidence / autonomy                            │             ┌──────────────┴──────────────┐             │                             │       Superpowers                  Matt Skills  engineering discipline        composable engineering   TDD / worktree / review       collaboration / tickets
```

这不是严格的软件栈分层。

它只是说明三者重点不同，而且可以组合。

---

# When should I use numi-workflow?

它更适合：

- 一个 Feature 会跨多个 Agent session；

- 项目需要持续数月甚至数年；

- 产品和架构决定不能靠聊天记忆；

- 多个 Agent 会共同修改同一个代码库；

- 错误实现的代价明显高于多做一点前期澄清；

- 需要知道“为什么当时这么设计”；

- 需要在 Agent 能力增强后逐渐减少人工监督。

它不一定适合：

- 一次性脚本；

- throwaway prototype；

- 几十分钟即可完成的小修改；

- 明确不需要长期维护的实验代码。

这种情况下，直接使用 Coding Agent、Superpowers 或单个 Matt Skill 往往更轻。

---

# Adaptive Workflow

numi-workflow 不希望最终变成一套不可修改的 SOP。

未来应该根据四类因素动态调整流程：

```
Agent capabilityHarness capabilityProject maturityUser maturity / risk preference
```

例如：

### 新项目 + 新用户

```
more grillingexplicit gatessmaller tasksmore review
```

### 稳定项目 + 强 Agent + 强测试体系

```
goal ↓autonomous shape ↓autonomous slice ↓frontier delivery ↓exception-only escalation ↓final acceptance
```

稳定的是 invariants。

可变化的是 policies。

---

# Autonomy Profile — Future Direction

未来可以把自主权显式配置成 policy：

```
autonomy:  research: high  planning: high  implementation: high  product_decisions: approval_required  architecture_changes: approval_required  destructive_actions: approval_required
```

并结合 Harness 能力：

```
harness:  subagents: true  worktrees: true  isolated_contexts: true  automated_tests: true  persistent_goals: true
```

再结合项目成熟度自动调整实际流程。

最终 numi-workflow 更像：

```
policy engine+collaboration coach+agent orchestration layer
```

而不是固定流程模板。

---

# First end-to-end case study: daily-bars

第一个完整 smoke test 是 `daily-bars-safe-publication`。

生命周期经历：

```
project baseline ↓feature research ↓Fact Closure ↓Decision Readiness ↓Gate B ↓task slicing ↓Fresh Session tracer bullet ↓Gate C ↓frontier delivery
```

最终 6 张 delivery tickets 完成，完整测试集 `38 passed`；未授权真实发布保持关闭，未启用条件性数据源 route。

这个案例暴露并修复了多个计划阶段无法提前穷举的问题，例如：

- 缺原始响应却存在 parsed values；

- QMT 日期标签的时区解释；

- fixture 缺失除息日样本；

- required day 完全缺席时的状态生成；

- correction 与 immutable version 的同键冲突；

- timeout worker 与 publication authority 隔离。

这些问题没有迫使用户逐项参与。

它们被 Agent 在已有 Goal、Spec 和任务边界内自行处理。

这正是 numi-workflow 想要实现的协作模式。

---

# Roadmap

## Pre-1.0

### `$feature-deliver`

实现真正的：

```
goal-drivenfrontier-basedmulti-agentexception-drivendelivery loop
```

重点验证：

- concurrent agents；

- worktrees；

- merge arbitration；

- completion evidence；

- downstream invalidation；

- graph mutation；

- recovery after partial frontier failure。

### `$feature-accept`

实现独立 Feature-level 验收。

---

## v1.0

目标是形成第一条完整稳定链：

```
project-discovery ↓feature-shape ↓feature-slice ↓feature-deliver ↓feature-accept
```

并完成第二个真实 Feature 的端到端验证。

---

## After v1.0

重点方向：

### Adaptive autonomy

根据 Agent / Harness / Project / User maturity 自动调整工作流重量。

### Worktree and integration orchestration

借鉴成熟 Agentic Coding workflow 的并行隔离与集成模式。

### Architecture health

让长期 Agent 开发不会因大量局部正确修改而逐渐腐化整体架构。

### Domain modeling

增强项目领域语言、实体、不变量和业务规则的长期表达。

### Retrospective learning

Agent 不只完成任务，还应该从执行过程持续改进：

```
task templatesnavigationtest seamsproject instructionsautomationguardrails
```

### Harness-aware workflow

随着 Codex、Claude Code 和其他 Agent Harness 获得新的：

```
subagentspersistent goalsbackground executionbetter context managementverificationcomputer use
```

numi-workflow 应减少不再必要的 ceremony，而不是继续保留为历史兼容流程。

---

# Status

numi-workflow 当前仍处于 pre-1.0 阶段。

已经经过真实 Feature 验证的核心包括：

```
project discoveryfeature shapingFact ClosureDecision ReadinessGate A / B / Cfeature slicingFresh Session Testtask graph / frontier semantics
```

下一阶段重点是 `$feature-deliver`。

---

# Principle

如果 numi-workflow 最终成功，它应该让用户越来越少地管理 Agent 的每一步。

用户应该越来越擅长定义：

> **what outcome matters, what must remain true, and how completion can be proven.**

Agent 则应该越来越擅长：

> **figure out everything else.**
