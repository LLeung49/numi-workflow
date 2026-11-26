# 切片开发与交付标杆案例 (Reference Case)

> 💡 **标杆导读**：本案例展示了单个端到端切片（以资金账本切片为例）在 `numi-slice-dev` 驱动下的完整落地全生命周期：从 H0 依赖物理核验、SpecKit 装配、强制软停等、带门禁编码，到最终输出 6 证据 DoD 验收报告与对话级变更总结。语言采用中立的多语言工程风格，展示标准工程纪律。

---

## 阶段一：H0 开工纪律与依赖核验（实录）

```bash
# 1. 物理核验前序切片 (Slice-00 公共契约) 是否真实存在于磁盘且已合入主干
$ ls -l src/common/contracts/
-rw-r--r-- 1 dev staff 1240 Oct 30 10:00 events.py
-rw-r--r-- 1 dev staff 1890 Oct 30 10:00 types.py

# 2. H0-D 分支与工作区物理隔离核验：确认主干干净，从最新 main 切出专属切片分支（非主干直改、非套娃分支）
$ git status --porcelain
$ git switch main && git switch -c feat/slice-01-account-ledger main
Switched to a new branch 'feat/slice-01-account-ledger'
```
- **H0 核验结论**：前序切片 `Slice-00` 公共契约已合入 `main` 且物理存在，上游强约束核验无冲突，已基于干净 `main` 基线切出独立分支 `feat/slice-01-account-ledger`（若是并行组卡片，则通过 `git worktree add .worktrees/slice-01 -b feat/slice-01-account-ledger main` 隔离独立工作目录），允许开工。

---

## 阶段二至四：SpecKit 装配与消歧

1. **`/speckit-specify` 需求与验收断言提取**：
   - 提取原料卡第 1 节（端到端复式记账）、第 5 节（主路径：转入/划转/冻结）、第 8 节（借贷守恒断言）与第 9 节（不做清单：不做外币汇兑与杠杆计算）。
2. **`/speckit-clarify` 消歧核验**：
   - 检查原料卡第 5.2 节关于“资金不足扣减”时的行为：已明确为抛出 `InsufficientFundsException` 并回滚事件，无未决歧义，消歧通过。
3. **`/speckit-plan` 实施方案与 Harness 组装**：
   - 提取原料卡第 6 节（账本服务接口 `AccountLedger`）、第 7 节（SQLite 表结构与平衡公式）、第 10 节（专属测试 Fixture `tests/fixtures/ledger_cases.json`）。
   - 承接外层 Harness 全局门禁要求（代码规范、类型检查、不变量回归）。

---

## 阶段五：Tasks 拆解与 🛑 强制软停等（实录）

Agent 在对话中输出软停等信息并停止调用工具：

```markdown
> 🛑 **【强制软停等】切片任务清单与交付物对照核验**
> 
> 本切片已完成需求规格与实施计划拆解，生成以下执行任务（Harness 先行）：
> 1. `[Task 1]`：创建 SQLite 资金明细迁移脚本与表结构（对齐原料卡第 7.1 节）；
> 2. `[Task 2]`：构建需求内专属 Inner Harness：准备 `ledger_cases.json` 测试用例与守恒单测骨架（对齐原料卡第 10 节，Harness 先行）；
> 3. `[Task 3]`：实现 `AccountLedger` 复式记账核心领域逻辑与双向事件记录（对齐原料卡第 5.1 节与第 6 节）；
> 4. `[Task 4]`：实现异常分支防御与余额不足拦截抛错（对齐原料卡第 5.2 节）；
> 5. `[Task 5]`：执行外层适用质量门禁与历史回归测试。
> 
> 📋 **原料卡第 11 节交付物白名单三态核验结论：**
> - **比对状态**：🟢 齐（全部白名单文件 1:1 闭环覆盖）
> - **文件覆盖对照**：
>   - [x] `src/ledger/models.py` $\leftrightarrow$ 对应 Task 1
>   - [x] `tests/unit/test_ledger.py` $\leftrightarrow$ 对应 Task 2 (Inner Harness)
>   - [x] `tests/fixtures/ledger_cases.json` $\leftrightarrow$ 对应 Task 2 (Inner Harness)
>   - [x] `src/ledger/service.py` $\leftrightarrow$ 对应 Task 3 & 4
> 
> 🚫 **不做清单（Anti-Scope）核验**：未触碰第 9 节排除的多币种汇率转换与外联银行 API。
> 
> ❓ **请审核任务清单。若无异议请回复“继续”，我将开始执行 /speckit-analyze 预检与编码实施。**
```

*(人类在对话中审核通过，输入“继续”)*

---

## 阶段六：执行 `/speckit-analyze` 预检与 `/speckit-implement` 编码实施（实录）

1. **执行 `/speckit-analyze` 静态交叉预检**：
   - 核查 `spec.md` 与 `tasks.md`：需求断言 100% 映射到 Task 2/3/4，无遗漏悬空；
   - 核查 `tasks.md` 与原料卡第 11 节白名单：交付文件 100% 吻合，第 9 节不做清单 0 触碰；
   - 核查契约与数据模型：字段与方法签名无漂移；
   - 预检结论：0 阻断性异常，静态一致性通过。

2. **调用 `/speckit-implement` 逐任务带门禁编码落地**：
   - 写前核验依赖库方法签名；
   - 严格遵循只读受保护路径，不修改 `src/common/contracts/`；
   - 严禁引入多余公共类型与无用抽象层；
   - 依次编码落地 Task 1 至 Task 4，伴随跑测，局部单测即时通过。

---

## 阶段七：6 证据 DoD 验收报告（落盘交付物）

文件路径：`docs/reports/slice-01-acceptance-report.md`

```markdown
# SLICE-01 交付验收报告 (DoD Verification)

- **切片名称**：资金账本与复式记账核心切片
- **Git Commit**：`a4f91c2`
- **验收结论**：🟢 **PASS**

### 1. 外层适用门禁证据 (Outer Harness Gates)
```text
$ uv run ruff check .
All checks passed!
$ uv run mypy src/
Success: no issues found in 6 source files
```

### 2. Inner Harness 专属测试证据
```text
$ uv run pytest tests/unit/test_ledger.py -v
tests/unit/test_ledger.py::test_happy_path_transfer PASSED [ 33%]
tests/unit/test_ledger.py::test_insufficient_funds_rollback PASSED [ 66%]
tests/unit/test_ledger.py::test_balance_conservation PASSED [100%]
3 passed in 0.42s
```

### 3. 交付物物理存在证据 (对齐第 11 节)
- `src/ledger/models.py`: 48 lines (4.2 KB) - 存在且非空
- `src/ledger/service.py`: 120 lines (9.8 KB) - 存在且非空
- `tests/unit/test_ledger.py`: 75 lines (6.1 KB) - 存在且非空
- `tests/fixtures/ledger_cases.json`: 32 lines (1.5 KB) - 存在且非空

### 4. 前序切片全量回归证据
```text
$ uv run pytest tests/ -k "not test_ledger" -v
tests/contracts/test_public_contracts.py PASSED [100%]
1 passed in 0.15s
```

### 5. 全局系统不变量核对
- `INV-01 借贷资金守恒`：自查通过，每笔划转必产生一对借贷记账凭证，差额严格为 0。
- `INV-02 状态外置与持久化`：自查通过，状态均持久化至 SQLite，无内存单例缓存。

### 6. 剩余人工核验项清单
- [ ] 本地已完成 SQLite 与 Mock 验证，等待真实银行网关凭证接入实测联调。
```

---

## 对话级 Review 变更总结（直接呈现在对话中）

```markdown
### 📝 切片变更总结 (Review Guide)

#### 1. 改动点 (Changes)
- **新增**：`src/ledger/models.py`（账本明细表与领域事件）
- **新增**：`src/ledger/service.py`（实现双侧复式记账业务闭环）
- **新增**：`tests/unit/test_ledger.py`（原料卡第 10 节 Inner Harness 测试）
- **新增**：`tests/fixtures/ledger_cases.json`（测试数据集）
- **修改**：`src/common/registry.py#L12`（注册账本实现实例，高亮前序文件触碰）

#### 2. 重点 Review 清单 (High-Risk Review Points)
1. **资金守恒校验**：[`src/ledger/service.py#L52-L68`](file:///path) 强校验每笔分录 `sum(debit) == sum(credit)`，若不平衡抛出硬中断。
2. **事务原子性**：[`src/ledger/service.py#L80-L95`](file:///path) 在数据库事务上下文内执行，确保异常时完全回滚。
3. **类型收窄处理**：[`src/ledger/models.py#L30`](file:///path) 针对 `Decimal` 金额精度采用强类型注解。

#### 3. 如何验证 (How to Verify)
```bash
# 1. 运行外层全量门禁
uv run ruff check . && uv run mypy src/
# 2. 运行本切片专属 Inner Harness 测试
uv run pytest tests/unit/test_ledger.py -v
# 3. 运行历史全量回归测试
uv run pytest tests/ -v
```
**剩余人工核验项**：
- [ ] 真实银行网关实机联调（当前处于 Mock 状态）。

#### 4. 拓扑交接与分支归并指引 (Merge & Next Slice)
```bash
# 人类审查通过后，提交当前切片并以 --no-ff 合入主干，再开启下一张卡：
git add src/ledger/ src/common/registry.py tests/ docs/reports/slice-01-acceptance-report.md specs/slice-01-account-ledger/
git commit -m "feat(slice-01): implement double-entry account ledger slice"
git switch main && git merge --no-ff feat/slice-01-account-ledger -m "merge feat/slice-01: account ledger"
```
```
