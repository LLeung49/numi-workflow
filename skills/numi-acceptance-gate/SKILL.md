---
name: numi-acceptance-gate
description: "Use when defining acceptance test designs (Mode A: 制定验收方案, status NOT_RUN), or executing actual verification commands, inspecting git diffs, and generating honest test reports (Mode B: 执行实测验收报告) for a delivered feature slice or system release."
---

# 验收方案与测试执行门禁技能 (Acceptance Gate)

本技能负责把好代码交付的最后一关（主要用于阶段里程碑/系统级大验收，或为未走 SpecKit 流程的手写代码出具独立验收报告；在标准 `numi-slice-dev` 单切片流水线中，其第 7 步已内置 6 证据 DoD 闭环，日常切片无需重复调用本技能）。

**测试负责判断结果对不对。完成必须提供构建、测试、静态检查和交付物证据；禁止通过删测试、放宽断言、关闭检查让结果变绿。**

为了防止 AI 产生“把预期结果当成执行事实、未跑测试先判通过”的幻觉，严格区分为两种工作模式：

- **模式 A：制定验收方案 (Acceptance Plan)**
  - **触发时机**：代码编写前（需求拆解或切片设计阶段）；
  - **职责**：规划要测哪些场景、期望断言什么数值、计划执行什么命令；
  - **原则**：**所有测试状态一律保持 `⚪ NOT_RUN`，通过率为 `N/A (NOT_RUN)`，严禁提前判定通过！**
  - **输出**：`docs/reports/<target>-acceptance-plan.md`。

- **模式 B：执行实测验收报告 (Acceptance Verdict Report)**
  - **触发时机**：代码编写完成并提交 Git 之后；
  - **职责**：核查 Git Diff（确保测试用例未被篡改）、在终端实际执行测试命令、抓取真实退出码与执行日志；
  - **原则**：**实事求是。跑了就是跑了，没跑就写 `NOT RUN`，部分通过写 `PARTIAL`，没有真实终端日志绝不宣称通过！**
  - **输出**：`docs/reports/<target>-acceptance-report.md`。

---

## 一、 核心验收原则（实事求是、重运行证据）

1. **方案制定与执行实测物理分开**：
   - 方案阶段只管“定测试计划与断言标准”，不假装测试已跑；
   - 验收阶段只看“实际命令输出与退出码”，不放宽既有断言。
2. **严禁 AI 作弊与自嗨**：
   - 严禁测试报错时修改测试断言期望值让测试变绿；
   - 严禁擅自给失败测试加 `@skip`、注释或删除测试用例；
   - 严禁为了通过测试擅自修改公共契约接口。
3. **区分“本地测试通过”与“外部真实准入”**：
   - 本地 SQLite/Mock 下跑通，如实记录本地通过；
   - 外部未打通的依赖（如真实券商账号、外部收费行情）坦诚标为 `NOT VERIFIED`，绝不虚夸宣称具备实盘资格。

---

## 二、 工作执行流程

### 阶段一：前置资产读取（硬性门禁）
动笔前必须调用 `read_file` 完整读取以下文件：
1. `references/writing-guide.md` —— 掌握模式 A/B 切换、实事求是报告风格与防作弊审查；
2. `references/reference-case.md` —— **研读标杆案例，学习一份实事求是、不弄虚作假、以真实日志为证据的验收报告**；
3. `assets/document-template.md` —— 获取方案模板 A 与报告模板 B。

### 阶段二：开工就绪度反问与模式判定（Readiness & Mode Gate）
动笔前必须反问自检，严格杜绝“未跑测试先判通过”与“凭空编造断言”：

#### 1. 模式就绪度核验：
- **检查代码是否已编写并提交 Git**：
  - 代码尚未编写/未完成 $\rightarrow$ **进入模式 A（制定验收方案）**，状态全部统一为 `⚪ NOT_RUN`，通过率一律标注 `N/A (NOT_RUN)`；
  - 代码已提交 Git $\rightarrow$ **进入模式 B（执行实测验收）**，必须检查 Git Diff 并运行真实终端命令。

#### 2. 各模式开工必备条件反问：
- **模式 A（方案制定）反问**：
  - 需求场景（SC-xx）和原料卡第 8 节的期望数值明确了吗？若断言数值模糊，暂停并向人类提问，严禁编造假断言。
- **模式 B（实测验收）反问**：
  - 本地 Git 分支干净吗？受保护路径有无被非法改动？（必须用 `git diff` 核查）
  - 外层适用质量门禁和切片测试命令的真实退出码与关键日志输出了吗？（**没有终端日志，绝对禁止给出 PASS 结论**）

### 阶段三 A：模式 A 执行（制定方案）
1. 读取切片原料卡与需求文档中的验收场景（SC-xx）；
2. 规划单元、集成与端到端测试用例，明确输入、触发与断言数值；
3. 输出 `docs/reports/<target>-acceptance-plan.md`（状态统一为 `⚪ NOT_RUN`）。

### 阶段三 B：模式 B 执行（实测报告）
1. 运行 `git diff` 核查受保护路径，确认无非法改动测试断言；
2. 在终端实际执行 `docs/AiProgrammingGuide.md` 规定的适用门禁命令，记录命令的标准输出、退出码与耗时（未配置项如实标注 NOT_CONFIGURED）；
3. 输出 `docs/reports/<target>-acceptance-report.md`，给出客观真实的实测结论。

---

## 三、 交付检查清单

- [ ] **资产读取门禁**：动笔前是否已实际调用 `read_file` 读取指南、**标杆案例 (`reference-case.md`)** 与模板？
- [ ] **模式准确无误**：代码未跑时是否坚决使用了模式 A？有无虚构测试通过率？
- [ ] **防作弊核查（模式 B）**：是否核验了 Git diff，确保受保护断言未被篡改？
- [ ] **真实日志证据（模式 B）**：报告中的结果是否均来自终端真实命令输出与退出码？
- [ ] **未决事项诚实披露**：未执行的用例是否如实写 `NOT RUN`？外部未通的是否写 `NOT VERIFIED`？
