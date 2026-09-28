# Skill 架构 v0.5

## 分层

```text
AI 开发工作流
  ↓
项目专用 Skill
  ↓
Spec-Kit / Superpowers
  ↓
编程 Agent
```

工作流负责阶段、评审、工作产物、事实回写和证据要求。

## 当前通用 Skill

| Skill ID | 中文名称 | 主要产物 |
|---|---|---|
| `project-discovery` | 项目基线梳理 | 项目资料索引 + 项目基线 + 按需现状事实记录 |
| `feature-shape` | 功能定义 | 功能规格 + 功能依据清单 |
| `feature-slice` | 任务拆解 | 任务依赖图 + 开发任务 + 任务上下文清单 |
| `project-bootstrap` | 工程初始化 | 后续实现 |
| `feature-deliver` | 单任务开发 | 后续实现 |
| `feature-accept` | 功能验收 | 后续实现 |
| `architecture-health` | 架构健康检查 | 后续实现 |
| `handoff` | 开发续接 | 已有独立 quota/handoff 机制可与未来版本组合 |

v0.5 仍只实现前三个 Skill，避免在上游契约尚未完成真实验证前扩展自动化。

## v0.5 新增能力

### 1. 描述“现在是什么”

复杂已有项目可以按需建立“现状事实记录”，后续会话优先复用并增量勘误。

### 2. ADR 更明确

重要 ADR 增加：

- 不变量；
- 明确不做。

### 3. 开发任务更适合 AI 执行

任务增加：

- 不要顺手做；
- 开发中新发现（执行时追加）；
- 可选执行建议。

### 4. Review 需要裁决

未来 `feature-accept` 必须实现：

```text
review finding
→ 对照 HEAD 复核
→ 采纳 / 部分采纳 / 驳回
→ 回写正确层级
```

## Skill 之间的衔接

```text
$project-discovery
  → PROJECT.md
  → 规范性项目基线
  →（按需）现状事实记录

$feature-shape
  ← PROJECT.md
  ← 相关项目基线
  ←（按需）相关现状事实
  → 功能规格 + 功能依据清单

$feature-slice
  ← 已批准且可拆解的功能规格
  → 任务依赖图
  → 开发任务
  → 任务上下文清单
```

## 默认项目布局

```text
/
├── PROJECT.md
├── CONTEXT.md
├── AGENTS.md / CLAUDE.md
├── docs/
│   ├── requirements.md
│   ├── architecture.md
│   ├── adr/
│   ├── research/
│   └── current-state/     # 仅复杂已有项目按需
└── .scratch/
    └── <feature>/
```

不要为了工作流完整强制创建 `docs/current-state/`。

## 项目专用 Skill

项目稳定后，可生成项目专用 Skill，固化：

- 真实测试 / 构建 / lint 命令；
- 模块边界；
- 数据库 / 服务启动方式；
- 安全规则；
- 项目特定测试边界；
- 自动化不变量检查。

## 后续开发顺序建议

完成一次真实 `$feature-slice` 和第一张新会话开发任务验证后，再实现：

1. `feature-deliver`
2. `feature-accept`
3. `architecture-health`
4. 与现有 `handoff` / quota hook 的组合

`feature-accept` 优先加入“审查意见逐条裁决”。

## 中文术语

所有面向用户和 Agent 的中文说明遵循 `TERMINOLOGY.md`。
