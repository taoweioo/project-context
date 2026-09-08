# project-context 设计说明

本文件定义 `project-context` 的目标设计，由作者维护。它用于约束规则文件的职责和调用关系，也可在用户询问工作方式时只读参考。本文件不参与初始化、维护或审计等具体流程。

## 上下文模型

项目使用少量分类级 `ctx-*` skill 保存前端长期知识。每个固定分类最多对应一个顶层 skill；该 skill 的 `SKILL.md` 是领域入口和语义路由器，分类内仅特定任务需要的知识放入 `references/` 并按需读取。

```text
.agents/skills/
├── ctx-project-baseline/
├── ctx-architecture/
├── ctx-modules/
├── ctx-capabilities/
├── ctx-contracts/
├── ctx-integrations/
├── ctx-policies/
└── ctx-other/
```

没有通过长期保留判断的分类不创建空 skill。`ctx-other` 只兜底已经确认需要保留、但经过职责拆分和主要加载原因判断后仍不属于现有分类的内容；分类困难不作为长期待确认状态。旧版 `ctx-<分类>-<主题>` 仍可读取和普通维护，但不再新建；它们通过用户确认的结构调整逐类迁移。

分类目录保持同级，但架构知识具有稳定的语义层级：`ctx-project-baseline` 保存项目定位、工程事实和长期约束，作为上游依据；`ctx-architecture` 保存当前架构、目标架构、演进路径和决策约束，作为这些依据在跨模块运行时设计上的展开；其他分类再保存局部实现知识。层级通过分类职责、分类级入口关系和证据追溯表达，不通过目录嵌套或跨分类复制表达。

## 用户场景

`project-context` 围绕初始化、维护和审计三类用户请求设计。初始化和维护最终都通过 `maintenance.md` 落地变更；审计保持只读。用户只要求分析或规划时，仍进入对应判断路径，但不执行文件修改。

## 场景流程

### 初始化

```text
初始化请求
→ initialization.md：检查项目并生成有证据的候选内容，不按页面生成 skill
→ maintenance.md：接收候选内容并进入统一维护流程
  → selection.md：决定保留状态、固定分类和目标位置
  → taxonomy.md：确定分类级固定名称
  → template.md / internal-references.md：建立入口、语义路由和按需内容
  → 执行选择结果、完成检查并汇总
```

用户只要求分析或规划时，沿用相同判断路径，但不创建或修改文件。

### 维护

```text
维护请求
→ maintenance.md：所有 ctx-* 变更的唯一入口

1. 确定维护范围和候选内容
   → 读取请求、指定证据、当前工作树和相关 ctx-*
   → 按可独立证实的知识边界生成候选内容

2. 决定最终状态和去向
   → selection.md：保留状态、固定分类、目标 skill 和目标文件
   → taxonomy.md：分类级固定路径；不创建主题级顶层 skill

3. 执行选择结果
   ├─ 分类 skill 不存在 → template.md → 创建最小 SKILL.md
   ├─ 分类共同知识 → 对应 SKILL.md
   ├─ 特定任务知识 → internal-references.md → reference + 语义路由
   ├─ 普通旧内容更新 → 保持原权威位置，避免新旧复制
   └─ 合并、移动、删除等结构变化
      → structural-changes.md：输出计划 → 用户确认 → 执行

4. 完成检查并输出维护结果
   → maintenance.md：核对变更范围、入口、路由、证据和权威位置
   → 汇总实际变更、检查结果和未执行项
```

### 审计

```text
审计请求
→ audit.md：只读检查 ctx-* skill 及其内部资源

1. 枚举目标、工作树、内部文件和引用关系
2. selection.md：核对保留价值和唯一归属
3. taxonomy.md：核对分类级名称并识别旧结构迁移候选
4. template.md / internal-references.md：核对入口、语义路由、准入和链接
5. audit.md：按问题级别输出报告，不修改文件
```

用户根据审计报告明确要求修复时，产生新的维护请求并进入 `maintenance.md`；审计本身不自动触发维护。

## project-context 文件结构

```text
SKILL.md：识别机制说明、初始化、维护和审计请求，只加载对应入口
references/
├── design.md：记录作者维护的目标设计，运行时仅供说明
├── initialization.md：执行初始化特有检查并生成候选内容
├── maintenance.md：所有变更的唯一入口
├── selection.md：决定保留状态、分类和内容去向
├── taxonomy.md：定义固定分类、分类级名称和旧结构兼容
├── template.md：定义分类级 SKILL.md、语义路由和内容分工
├── internal-references.md：定义内部 reference 的准入与路由
├── structural-changes.md：处理受控结构迁移
└── audit.md：执行只读检查并输出报告
```

不增加通用执行文件。只有具备独立读取原因的特定主题知识才保留为按需文件。

## 规则归属

具体规则由下表中的文件定义。流程文件保留调用条件和执行提醒，审计文件保留检查项，不另设判断标准。修改机制时先更新规则所属文件，再核对调用处。

| 规则 | 定义位置 |
| --- | --- |
| 保留价值、项目基础保留边界、最终状态和权威位置优先级 | [selection.md](./selection.md) |
| 分类职责、规范名称、架构语义层级和旧结构识别 | [taxonomy.md](./taxonomy.md) |
| 入口结构、路由、架构状态表达和 `ctx-other` 触发条件 | [template.md](./template.md) |
| reference 准入和分类内主题拆分 | [internal-references.md](./internal-references.md) |
| 结构调整的计划、确认和迁移操作 | [structural-changes.md](./structural-changes.md) |
| 变更执行、完成检查和结果汇总 | [maintenance.md](./maintenance.md) |
| 初始化候选生成；只读审计及问题分级 | [initialization.md](./initialization.md)、[audit.md](./audit.md) |

`initialization.md` 不直接修改 `ctx-*`，`audit.md` 只检查和报告。所有变更仍通过 `maintenance.md` 执行。
