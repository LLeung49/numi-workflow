# v0.6 → v0.6.1 升级说明

v0.6.1 是对 Gate B 未决事项处理的兼容性增强，不新增 Skill 或 Gate。

## 1. 不需要重跑已有 Research

已有 `spec.md`、`research.md`、固定测试样本继续有效。下一次 `$feature-shape` 复审时，用 v0.6.1 规则重新拆分未决项和计算 blocker 即可。

## 2. 重新检查混合未决项

如果一条问题同时包含以下任一差异，拆成多条：

- 问题类型不同；
- 解决责任不同；
- 当前阻塞状态不同；
- 激活条件不同；
- 一部分可保守处理，另一部分必须核实。

## 3. 给条件性 blocker 写激活条件

未来 route / 能力的事实未知，不自动阻塞当前 Feature。只有当前首版真正启用并依赖它时才成为当前 blocker。

## 4. 每次决定后重新计算 blocker

用户确认决策、Agent 核实事实、范围变化或 route 取舍后，都重新生成“当前真正 blocker”列表。不要沿用上一轮数量。

## 5. 推荐保留条件语义

推荐写成：

```text
触发条件 → 系统行为 → 当前结果影响
```

不要把可选 fallback 不可用扩大成无条件失败。

## 6. 重要：同步 Codex 实际加载的 Skill

更新 `numi-workflow` 源码仓库 **不会自动更新 Codex 当前已安装 / 已复制的 Skill**。

请把本仓库的：

```text
skills/project-discovery/
skills/feature-shape/
skills/feature-slice/
```

同步到你当前 Codex 实际读取的 Skill 目录。若使用项目级 Skill，通常就是项目里已经实际配置给 Codex 的 `.agents/skills/<skill-id>/`；如果你使用其它安装方式，以 Codex 当前真实加载路径为准。

同步后不要只看源码仓库，直接检查**实际加载路径**中的文件。至少应能搜到：

```text
feature-shape/SKILL.md:
- 条件性 blocker
- 每次新证据或人工决定后，都重新计算 blocker

feature-slice/SKILL.md:
- 不得隐式激活条件性 blocker
```

如果 Codex 仍报告“feature-shape 是旧版三类模板”，说明 Skill 没有同步成功。

## 7. 旧 Spec 不需要机械改版

只有当前仍打开的未决项需要采用新列：

- 阻塞条件
- 当前阻塞

已关闭的历史记录不必为了升级制造无意义 diff。
