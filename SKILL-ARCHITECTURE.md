# Skill 架构 v0.6.1

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

工作流负责阶段、评审、工作产物、事实回写、未决事项责任分配和证据要求。

## 当前通用 Skill

| Skill ID | 中文名称 | 主要产物 |
|---|---|---|
| `project-discovery` | 项目基线梳理 | 项目资料索引 + 项目基线 + 按需现状事实记录 |
| `feature-shape` | 功能定义 | 功能规格 + 功能依据清单 + 按需调研证据 + 未决事项 / 决策归纳 |
| `feature-slice` | 任务拆解 | 任务依赖图 + 开发任务 + 任务上下文清单 |
| `project-bootstrap` | 工程初始化 | 后续实现 |
| `feature-deliver` | 单任务开发 | 后续实现 |
| `feature-accept` | 功能验收 | 后续实现 |
| `architecture-health` | 架构健康检查 | 后续实现 |
| `handoff` | 开发续接 | 已有独立 quota/handoff 机制可与未来版本组合 |

v0.6.1 仍只实现前三个 Skill。

## v0.6.1 能力：未决事项、条件性 blocker 与决策归纳

`feature-shape` 不再只把问题分成“待确认 / 待调研 / 实现细节”，而是进一步明确：

```text
这个问题是什么类型？
        ↓
谁有能力把它解决？
        ↓
客观事实 → Agent 继续核实
功能 / 架构 / 风险选择 → Agent 先推荐，再由人确认
实现细节 → 延后到开发任务
        ↓
所有 blocker 关闭
        ↓
Gate B 可进入任务拆解
```

Research、责任归类和决策归纳都属于 `feature-shape` 内部能力，不新增新 Skill 或新 Gate。v0.6.1 进一步要求：混合未决项拆分、条件性 blocker 写明激活条件、每次证据/决定后重新计算当前 blocker。


## 源码 Skill 与 Codex 已加载 Skill 不是同一状态

`numi-workflow/skills/*` 是工作流源码。只有把对应 Skill 同步 / 安装到 Codex 实际读取的 Skill 目录后，新规则才会生效。

因此升级验收必须同时检查：

1. 工作流源码仓库已经是 v0.6.1；
2. Codex 实际加载的 `feature-shape/SKILL.md` 能找到“条件性 blocker”“重新计算 blocker”等 v0.6.1 规则；
3. `feature-slice` 能找到“不得隐式激活条件性 blocker”的规则。

如果 Codex 报告自己仍在使用旧三分类模板，说明源码升级完成但 Skill 安装没有同步。

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
  →（按需）调研记录 / 固定测试样本
  → 未决事项表
  → Gate B 待确认决策（含 Agent 推荐）

$feature-slice
  ← 已批准且“任务拆解准备状态 = 可开始”的功能规格
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
        ├── spec.md
        ├── research.md    # 按需
        └── issues/
```

不要为了工作流完整强制创建 research / current-state 文件。

## 项目专用 Skill

项目稳定后，可生成项目专用 Skill，固化真实测试 / 构建命令、模块边界、安全规则、项目特定测试边界和自动化不变量检查。

## 后续开发顺序建议

完成一次真实 `$feature-slice` 和第一张新会话开发任务验证后，再实现：

1. `feature-deliver`
2. `feature-accept`
3. `architecture-health`
4. 与现有 `handoff` / quota hook 的组合

`feature-accept` 优先加入“审查意见逐条裁决”。

## 中文术语

所有面向用户和 Agent 的中文说明遵循 `TERMINOLOGY.md`。
