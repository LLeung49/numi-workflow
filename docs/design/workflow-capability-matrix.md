# 工作流能力矩阵（Workflow Capability Matrix）

- 版本：v0.7-draft
- 状态：设计评审中
- 目标：建立 numi-workflow 能力边界和复用策略

相关文档：

- workflow-capability-review.md
- SKILL-ARCHITECTURE.md
- WORKFLOW.md
- feature-deliver-design.md（规划中）

---

# 1. 文档目的

`workflow-capability-matrix.md` 用于描述：

- Agent 软件开发工作流中的核心能力；
- 当前生态已有能力提供情况；
- numi 对每项能力的责任边界；
- 后续演进方向。

它回答：

> 一个 Agentic Coding Workflow 需要哪些能力？哪些应该由 numi 负责，哪些应该站在成熟生态之上复用？

---

# 2. 能力分类模型

numi 使用三类能力归属：

## Own

numi 自主拥有。

适用于：

- 长期项目一致性；
- 人与 Agent 的责任边界；
- 项目基线维护；
- 决策和证据管理。

---

## Adapt

吸收成熟实践并进行适配。

适用于：

- 已存在优秀方法；
- 但需要结合长期项目治理。

---

## Reuse

直接复用。

适用于：

- 已有成熟工程实践；
- 不属于 numi 核心问题。

---

# 3. 核心能力矩阵

---

# 3.1 项目基线与长期项目依据

## 能力目标

解决：

> Agent 如何长期理解一个不断演进的软件项目。

| 能力     | 来源                          | numi定位 | 状态  |
| ------ | --------------------------- | ------ | --- |
| 项目目标管理 | numi                        | Own    | 已有  |
| 项目资料索引 | numi                        | Own    | 已有  |
| 架构约束管理 | ADR / numi                  | Own    | 已有  |
| 工程规范管理 | numi                        | Own    | 已有  |
| 领域术语管理 | numi / Matt domain modeling | Adapt  | 演进中 |
| 长期决定记录 | numi                        | Own    | 已有  |

说明：

外部 Agent Workflow 通常默认：

> 开发者已经理解项目背景。

numi 解决：

> 如何让不同时间、不同上下文的 Agent 持续理解项目。

---

# 3.2 意图理解与目标澄清

## 能力目标

解决：

> 用户真正想完成什么。

| 能力     | 来源                                        | numi定位 | 状态  |
| ------ | ----------------------------------------- | ------ | --- |
| 目标澄清   | Matt grill-me / Superpowers brainstorming | Adapt  | 可复用 |
| 方案探索   | Superpowers brainstorming                 | Reuse  | 成熟  |
| 成功标准定义 | numi                                      | Own    | 已有  |
| 非目标定义  | numi                                      | Own    | 已有  |
| 目标长期保存 | numi                                      | Own    | 已有  |

说明：

外部 Workflow 擅长帮助用户思考。

numi 负责：

> 将思考结果转化为长期项目依据。

---

# 3.3 功能定义

## 能力目标

解决：

> 一个 Feature 是否已经足够明确，可以安全进入开发。

| 能力     | 来源                        | numi定位 | 状态   |
| ------ | ------------------------- | ------ | ---- |
| 需求规格生成 | Matt to-spec              | Adapt  | 已有方向 |
| 功能范围定义 | numi feature-shape        | Own    | 已有   |
| 事实闭环   | numi                      | Own    | 已有   |
| 决策归纳   | numi                      | Own    | 已有   |
| 验收标准定义 | Matt / Superpowers + numi | Adapt  | 演进中  |
| 未决事项管理 | numi                      | Own    | 已有   |

---

# 3.4 任务拆解

## 能力目标

解决：

> 如何把明确目标转换为可执行开发任务。

| 能力      | 来源              | numi定位 | 状态   |
| ------- | --------------- | ------ | ---- |
| 开发任务生成  | Matt to-tickets | Adapt  | 已有方向 |
| 任务依赖关系  | Matt task graph | Adapt  | 已有   |
| 任务边界定义  | numi            | Own    | 已有   |
| 新会话接手检查 | numi            | Own    | 已有   |
| 上下文传递   | numi            | Own    | 已有   |
| 最小可运行路径 | numi            | Own    | 已有   |

---

# 3.5 开发执行

## 能力目标

解决：

> 如何完成具体工程实现。

| 能力           | 来源                    | numi定位 |
| ------------ | --------------------- | ------ |
| 代码实现         | Codex / Claude Code 等 | Reuse  |
| TDD          | Superpowers           | Reuse  |
| Debug 流程     | Superpowers           | Reuse  |
| Git Worktree | Superpowers           | Reuse  |
| 代码重构         | Agent / 工程实践          | Reuse  |
| Code Review  | Superpowers / Matt    | Reuse  |

原则：

> 如果能力主要解决“如何执行工程动作”，numi 不应该重复建设。

---

# 3.6 功能交付

## 能力目标

解决：

> 如何让多个 Agent 安全完成一个 Feature。

| 能力        | 来源   | numi定位 | 状态              |
| --------- | ---- | ------ | --------------- |
| 交付状态管理    | numi | Own    | feature-deliver |
| 当前可执行任务判断 | numi | Own    | 规划              |
| 执行后端选择    | numi | Own    | 规划              |
| Agent 调度  | 执行后端 | Adapt  | 规划              |
| 证据收集      | numi | Own    | 规划              |
| 阻塞处理      | numi | Own    | 规划              |
| 升级判断      | numi | Own    | 规划              |

---

# 3.7 功能验收

## 能力目标

解决：

> 如何判断 Feature 是否真正完成。

| 能力        | 来源   | numi定位 | 状态  |
| --------- | ---- | ------ | --- |
| 自动测试执行    | 外部工具 | Reuse  | 成熟  |
| 验收标准检查    | numi | Own    | 规划  |
| 证据汇总      | numi | Own    | 规划  |
| 需求覆盖检查    | numi | Own    | 规划  |
| 项目基线一致性检查 | numi | Own    | 规划  |

---

# 3.8 项目演进

## 能力目标

解决：

> 长期项目如何持续保持健康。

| 能力         | 来源        | numi定位 |
| ---------- | --------- | ------ |
| 技术债发现      | 外部 + numi | Adapt  |
| 架构健康检查     | numi      | Own    |
| 工作流优化      | numi      | Own    |
| Agent 能力评估 | numi      | Own    |
| 自主程度调整     | numi      | Own    |

---

# 4. 外部 Workflow 能力映射

---

# 4.1 Superpowers

定位：

> 软件工程执行纪律工作流。

优势：

- brainstorming；
- 设计验证；
- TDD；
- Debug；
- Review；
- Worktree。

numi 策略：

## Reuse

直接复用：

- TDD；
- Debug；
- Review；
- Worktree。

## Adapt

吸收：

- 设计验证；
- 执行纪律。

不重复建设：

- 编码流程；
- 测试流程。

---

# 4.2 Matt Skills

定位：

> 可组合的 Agent 软件开发工作流。

优势：

- 用户需求澄清；
- Spec；
- 开发任务；
- 领域建模；
- 实现流程。

numi 策略：

## Adapt

吸收：

- Spec 思想；
- 任务拆解；
- 领域建模；
- 新会话可执行任务。

## Reuse

未来可作为执行后端：

- implement-spec。

---

# 5. numi 不应该建设的能力

为了避免工作流膨胀，明确：

numi 不应该建设：

- 自己的代码生成模型；
- 自己的 TDD 框架；
- 自己的 Git 管理系统；
- 自己的 IDE Agent；
- 自己的代码评审体系；
- 自己的 Prompt 集合库。

原因：

这些问题已经有成熟生态解决。

numi 应关注：

> 如何让这些能力围绕长期项目目标协同工作。

---

# 6. 能力缺口分析

当前主要缺口：

| 能力     | 优先级 | 计划              |
| ------ | --- | --------------- |
| 功能交付编排 | 高   | feature-deliver |
| 功能验收   | 高   | feature-accept  |
| 执行后端契约 | 高   | v0.7            |
| 自主程度控制 | 中   | v1.x            |
| 项目健康分析 | 中   | v1.x            |

---

# 7. v0.7-v1.0 演进路线

## v0.7

目标：

完成：

- 能力边界定义；
- feature-deliver；
- 执行后端模型。

---

## v0.8

目标：

完成：

- feature-accept；
- 验收闭环；
- 交付质量模型。

---

## v1.x

目标：

探索：

- Adaptive Autonomy；
- 多 Feature 协同；
- 项目健康管理；
- Agent 能力自适应。

---

# 8. 总结

numi-workflow 的核心竞争力不是拥有最多 Skill。

而是：

> 用最少的治理能力，让人和越来越强的 Agent 更可靠地完成长期软件项目。

随着 Agent 能力增强：

- 执行能力应该越来越外部化；
- 项目基线越来越重要；
- 决策边界越来越清晰；
- 人应该从操作 Agent 转向管理目标和判断。
