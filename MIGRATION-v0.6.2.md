# v0.6.1 → v0.6.2 升级说明

本版以远端 `LLeung49/numi-workflow` 的 v0.6.1 `main@697662739b6eddf6efe3a6676aa81cdceaa9ddd2` 为基线生成。

v0.6.2 是 `$feature-shape` 执行策略增强，不新增 Skill 或 Gate。

## 1. 默认从“分类后停下”改为“事实闭环”

升级后，`$feature-shape` 发现当前 `F-*` blocker 时，只要 Agent 仍能在当前授权、范围和合理成本内核实，就继续定向取证、回写并重新计算 blocker。

正常 Gate B 不应把这类尚未完成的 Agent 作业作为用户待办。

## 2. 只有显式要求时使用诊断复审模式

如果用户明确说“不要新增外部调研”“只重新分类”“做 smoke test”，可以暂停事实闭环，但必须在输出中标记：

```text
运行模式：诊断复审；本轮故意不执行事实闭环。
```

该状态不能进入 `$feature-slice`。

## 3. 事实确实无法继续时记录客观限制

因权限、网络、认证、硬件、数据来源、未授权写操作 / 付费能力或明显范围越界而无法继续时，记录“Agent 闭环受限事项”。不要让用户决定第三方接口事实；用户最多确认是否接受由此产生的功能边界。

## 4. 不要为待选方案穷举所有事实分支

如果一个 `D/A/R` 决策会决定某些 route 是否进入首版，先关闭主路径事实，对候选分支只补足形成可靠推荐所需的低成本证据。用户选定后，再激活对应条件性 blocker。

## 5. 新建未决事项 ID 与状态分离

新建 ID 使用 `F/D/A/R/I`。`a/b/c` 只表示同一问题家族的子项。不要新建 `N-*` 来表示非 blocker；状态变化只改状态列。旧历史 ID 不要求机械重写。

## 6. 已有 Feature 如何继续

不需要重跑 `$project-discovery` 或已有 Research。对仍打开的 Feature 直接调用最新 `$feature-shape`：

- 先重新计算当前 blocker；
- 自动闭环可核实的 Agent-owned 当前事实；
- 对条件性事实保持未激活；
- 最后把真正需要人的决定带到 Gate B；
- 用户决定若激活新的条件性事实，再回到事实闭环循环，关闭后才判断是否可进入 `$feature-slice`。

## 7. 同步 Codex 实际 Skill 并自检

把本仓库 `skills/` 同步到 Codex 实际加载目录后，检查：

```text
feature-shape/SKILL.md:
- 事实闭环循环
- 诊断复审模式
- Agent 闭环受限事项
- 不要用 `N-*`

feature-slice/SKILL.md:
- 不得隐式激活条件性 blocker
- 事实闭环循环

feature-shape/agents/openai.yaml:
- 默认执行事实闭环循环

feature-slice/agents/openai.yaml:
- 诊断复审模式
```

如果这些字符串在源码仓库存在、但 Codex 实际 Skill 路径不存在，说明只更新了源码，没有更新安装。
