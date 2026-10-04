# Feature Deliver Skill Design

- 版本：v0.7
- 状态：已确认
- 类型：Skill 设计文档
- 上游依据：
  - `docs/design/feature-deliver-design.md`
  - `docs/design/feature-deliver-mvp-scope.md`

## 1. Skill 定位

Feature-deliver skill 是 numi-workflow 中负责 Feature 交付状态管理与协作边界维护的能力。

它不负责：

- 替代 Coding Agent；
- 执行代码开发；
- 调度 Agent；
- 规划 Agent 执行路径；
- 实现 session handoff；
- 宣告最终功能验收通过。

它负责：

> 让人类和 Agent 围绕同一个 Feature 状态、目标、完成证据和协作边界持续协作。

## 2. 与其它 Skill 的关系

```text
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
是否有证据支持完成主张？
哪些工作可以继续？
是否需要人工介入或返回上游？

↓

feature-accept

回答：
整个 Feature 是否真正满足目标和验收标准？
```

## 3. 触发条件

Feature-deliver 适用于：

- Feature 已完成 `$feature-shape`；
- Feature 范围已经确认；
- 已通过 Gate B；
- 已存在经 Gate C 确认的可执行工作单元；
- 进入执行或验证阶段。

不应该用于：

- 需求澄清；
- 范围设计；
- 任务拆解；
- 代码调试本身；
- 最终功能验收。

遇到对应问题时：

```text
功能定义变化 → $feature-shape
任务拆解变化 → $feature-slice
代码实现 / 调试 → Coding Agent / 成熟执行 Workflow
最终功能验收 → $feature-accept
```

## 4. 输入契约

需要的不是固定文件名，而是以下语义输入：

```yaml
feature:
  id:
  title:
  objective:
  scope:
  acceptance:

work_units:
  - id:
    description:
    dependencies:
    status:

decisions:
  - decision:
    rationale:

evidence:
  - optional existing evidence

delivery_state:
  - optional existing state record
```

对应的实际载体可以是：

- `spec.md`；
- 任务依赖图；
- `issues/` 下开发任务；
- 项目已有等价文档；
- `delivery.md`。

缺少关键输入时：

- 标记缺失；
- 不自行发明上游决定；
- 返回对应上游 Skill。

## 5. 核心能力

### 5.1 Feature State Tracking

Feature 层维护：

```text
可开始
  ↓
交付中
  ↓
验证中
  ↓
待验收
```

异常可以进入：

```text
阻塞
```

“需要人工判断 / 需要上游复审”是协作状态信息，不是另一个 Runtime 状态机。

`feature-deliver` 不拥有：

```text
已验收
验收失败
```

这些属于 `$feature-accept`。

开发任务层可以使用：

```text
未开始
可开始
进行中
待验证
已完成
阻塞
需上游复审
```

### 5.2 Evidence Collection

遵循：

```text
Claim
 ↓
Evidence
 ↓
Acceptance Readiness
```

证据包括：

- 测试结果；
- 代码变更；
- Review 记录；
- 文档；
- 运行结果。

一个开发任务可以被标记为“已完成”，前提是存在与其验收标准匹配的证据。

### 5.3 Delivery Boundary

Feature-deliver 判断的是**变化属于哪一层**，不是替 Agent 决定具体怎么实现。

允许留在当前执行范围：

- 已批准范围内的普通实现和 bug 修复；
- 为满足当前任务验收标准必须补充的测试或局部实现细节。

返回 `$feature-slice`：

- 任务粒度需要拆分 / 合并；
- 任务依赖变化；
- 剩余工作单元需要重排，但功能规格不变。

返回 `$feature-shape`：

- 修改产品目标；
- 范围扩大 / 缩小；
- 用户可观察行为变化；
- 验收标准变化；
- 条件性 blocker 被实际激活；
- 新增功能决策或风险取舍。

返回 Gate A / ADR：

- 跨 Feature 公共架构边界变化；
- 长期工程规范变化；
- 项目级不变量变化。

### 5.4 Handoff Contract

Feature-deliver 不实现 handoff。

只定义 Feature 级交接信息：

```text
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
+
Boundaries
```

上下文迁移由 `codex-handoff` 等工具负责。

## 6. 交付状态记录

默认可以维护：

```text
delivery.md
```

如果项目已经有等价状态文件则复用。

它是：

> Feature 级动态执行事实记录。

它不是：

- 新功能规格；
- 新任务图；
- session handoff；
- Agent 调度计划；
- 最终验收报告。

## 7. 行为规则

### MUST

必须：

- 保持 Feature 已批准目标；
- 维护交付状态；
- 记录证据；
- 暴露阻塞；
- 对开发中新发现分类；
- 标识真正的人工决策点；
- 明确何时需要返回上游；
- 最多把 Feature 推进到“待验收”。

### MUST NOT

禁止：

- 修改 Feature 目标；
- 自主扩大范围；
- 选择模型；
- 调度 Agent；
- 自动启动 subagent；
- 替代 Coding Agent；
- 创建 Workflow Runtime；
- 创建另一套 handoff 工具；
- 宣告 Feature 最终验收通过。

## 8. 输出契约

输出：

```text
功能交付状态

当前阶段:
已完成:
当前可继续:
主要证据:
当前阻塞:
需要人工判断:
需要返回上游:
建议下一步:
```

同时更新 Feature 的交付状态记录。

## 9. 与 codex-handoff 边界

```text
feature-deliver

负责：
WHAT
WHY
STATE
EVIDENCE
BOUNDARIES

↓

codex-handoff

负责：
CONTEXT TRANSFER
SESSION CONTINUITY
```

## 10. 与成熟执行 Workflow 的边界

feature-deliver 不重新实现：

- TDD；
- Debug；
- Worktree；
- Code Review；
- 代码实现；
- Agent 调度。

这些由 Coding Agent / Agent Harness / Superpowers / Matt Pocock Skills 等成熟能力承担。

## 11. MVP 范围

实现：

- Feature State；
- Evidence Model；
- Delivery Boundary；
- Handoff Contract；
- Acceptance Readiness。

不实现：

- Agent Runtime；
- Workflow Engine；
- Multi Agent Orchestration；
- Capability Marketplace；
- 最终 Feature Acceptance。

## 12. 设计原则

### Evidence over Assertion

完成必须有证据。

### State over Conversation

长期状态不能只存在聊天记录。

### Boundary over Automation

明确边界比无限自动化更重要。

### Collaboration over Replacement

numi 的目标是提升 Human-Agent 协作，而不是替代 Agent。
