# 工作流能力注册表设计（Workflow Capability Registry Design）

-   版本：v0.7-draft
-   状态：设计评审中
-   目标：定义 numi-workflow 如何描述、发现和管理可用能力。

------------------------------------------------------------------------

# 1. 文档目的

能力注册表用于解决：

> numi 如何知道当前环境具备哪些能力，以及这些能力由谁提供。

它不是简单的 Skill 列表，而是连接：

    Workflow

    ↓

    Capability

    ↓

    Provider

    ↓

    Execution Backend

的中间层。

------------------------------------------------------------------------

# 2. 设计原则

## 2.1 能力与实现分离

Workflow 不应该绑定具体工具。

错误：

    feature-deliver -> Codex

正确：

    feature-deliver

    ↓

    implementation capability

    ↓

    Codex / Claude Code / 其它 Agent

------------------------------------------------------------------------

## 2.2 面向能力设计

能力描述：

-   需要什么；
-   输入是什么；
-   输出是什么；
-   约束是什么。

Provider 描述：

-   谁可以提供该能力；
-   当前是否可用；
-   如何调用。

------------------------------------------------------------------------

# 3. 能力分类

## Own

numi 自主拥有。

例如：

-   项目基线；
-   功能定义；
-   决策管理；
-   交付治理。

------------------------------------------------------------------------

## Adapt

吸收外部成熟方法并适配。

例如：

-   Spec 驱动开发；
-   领域建模；
-   任务拆解方法。

------------------------------------------------------------------------

## Reuse

直接复用成熟能力。

例如：

-   TDD；
-   Debug；
-   Code Review；
-   测试工具。

------------------------------------------------------------------------

## Delegate

责任属于 numi 流程，但执行交给外部系统。

例如：

-   编码；
-   大规模测试运行；
-   构建发布。

------------------------------------------------------------------------

# 4. 注册表结构

建议运行时配置：

    .numi/

    └── capability-registry.yaml

设计示例：

``` yaml
version: "1.0"

capabilities:

  feature_definition:

    ownership: numi
    category: own

    providers:

      - id: feature-shape
        type: skill


    input:
      - project_baseline
      - user_goal


    output:
      - feature_spec
      - decisions


  implementation:

    ownership: external
    category: delegate

    providers:

      - id: codex
        type: agent

      - id: claude-code
        type: agent
```

------------------------------------------------------------------------

# 5. Provider 属性

每个 Provider 应描述：

``` yaml
provider:

  id:

  type:

  capabilities:

  availability:

  constraints:

  version:
```

------------------------------------------------------------------------

# 6. feature-deliver 使用方式

feature-deliver 不直接调用工具。

流程：

    读取任务要求

    ↓

    分析需要能力

    ↓

    查询 capability registry

    ↓

    选择 provider

    ↓

    执行任务

    ↓

    收集证据

------------------------------------------------------------------------

# 7. 能力发现

未来支持：

## 静态声明

用户或插件声明：

    我提供 implementation capability

------------------------------------------------------------------------

## 自动发现

例如：

检测：

-   已安装 Skill；
-   MCP；
-   Agent backend；
-   工具权限。

------------------------------------------------------------------------

## 能力验证

不能只声明：

需要验证：

-   是否可调用；
-   是否满足版本要求；
-   是否满足项目约束。

------------------------------------------------------------------------

# 8. 与 Skill 的关系

Skill：

> 能力的一种实现形式。

例如：

    feature_definition capability

            |

            +-- feature-shape skill

Agent：

> 可以提供多个能力的执行主体。

例如：

    Codex

     capabilities:

     - implementation
     - testing
     - debugging

------------------------------------------------------------------------

# 9. 当前不实现范围

v0.7 不实现：

-   完整 Agent 市场；
-   自动发现所有工具；
-   自动动态调度所有 Agent。

当前目标：

建立稳定能力模型，为 feature-deliver 提供基础。

------------------------------------------------------------------------

# 10. 后续演进

## v0.7

-   capability schema
-   feature-deliver 查询接口

## v0.8

-   provider 自动发现
-   能力验证

## v1.x

-   Adaptive Autonomy
-   多 Agent 协同
-   动态工作流调整

------------------------------------------------------------------------

# 总结

Capability Registry 的目标：

> 让 numi 面向能力设计，而不是面向工具设计。

它保证未来 Agent 能力快速变化时，numi 的核心治理能力仍保持稳定。
