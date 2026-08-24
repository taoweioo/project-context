# project-context 设计说明

本文件定义 `project-context` 的目标设计，由作者维护。它用于约束规则文件的职责和调用关系，也可在用户询问工作方式时只读参考。本文件不参与初始化、维护或审计等具体流程。

## 用户场景

`project-context` 围绕初始化、维护和审计三类用户请求设计。初始化和维护最终都通过 `maintenance.md` 落地变更；审计保持只读。用户只要求分析或规划时，仍进入对应场景，但不执行文件修改。

## 场景流程

### 初始化

初始化请求的处理路径如下：

```text
初始化请求
→ initialization.md：检查项目并生成初始候选内容
→ maintenance.md：接收候选内容并进入统一维护流程
  → selection.md：决定每项候选内容的最终状态与去向
  → 执行选择结果
  → 汇总初始化结果
```

用户只要求分析或规划时，沿用相同的判断路径，但不创建或修改文件。

### 维护

维护请求的处理路径如下：

```text
维护请求
→ maintenance.md：所有 ctx-* 变更的唯一入口

1. 确定维护范围和候选内容
   → 读取当前请求和当前可见会话中的相关说明
   → 读取用户指定的 commit、git diff、代码和当前工作树
   → 对照相关 ctx-* skill，生成带证据的候选内容

2. 决定候选内容的最终状态
   → selection.md：决定新增、更新、删除、保持不变、待确认或跳过，以及内容去向

3. 执行选择结果
   ├─ baseline 不存在 → taxonomy.md 固定名称 → template.md → 创建 ctx-project-baseline/SKILL.md
   ├─ 普通内容变化 → 对应的 ctx-*/SKILL.md 或内部 resource
   ├─ 新建 skill → taxonomy.md → template.md → 新的 ctx-*/SKILL.md
   ├─ 内容复杂或用户明确维护内部 reference
   │  → internal-references.md：按需判断是否使用内部 reference
   └─ 需要改变文件结构
      → structural-changes.md：输出计划 → 用户确认 → 执行

4. 输出维护结果
   → maintenance.md：汇总实际变化、证据、待确认、跳过和无需更新的内容
```

用户只要求分析或规划时，沿用相同的判断路径，但不创建、修改或调整文件。

### 审计

审计请求的处理路径如下：

```text
审计请求
→ audit.md：只读检查 ctx-* skill 及其内部资源

1. 确定审计范围
   → 读取用户指定的 ctx-* 和当前工作树
   → 排除 docs/、非 ctx-* skill 和无效目标

2. 读取目标与证据
   → 枚举目标 skill 的全部内部文件并核对引用关系
   → 读取目标 SKILL.md、明确引用的 resource 和检查所需的孤立文件
   → 按需读取代码、配置、项目指令和用户说明

3. 检查现有上下文
   ├─ selection.md：判断内容是否仍应保留及其归属是否正确
   ├─ taxonomy.md：检查分类和命名
   ├─ template.md：检查 skill 结构和内容分工
   └─ internal-references.md：检查内部 reference 的准入、路径和读取条件

4. 输出审计报告
   → audit.md：按问题级别汇总结论、证据和待确认项
   → 不修改任何文件
```

用户根据审计报告明确要求修复时，产生新的维护请求并进入 `maintenance.md`；审计本身不自动触发维护。

## 目标文件结构

以下结构按职责拆分文件。

```text
SKILL.md：识别机制说明、初始化、维护和审计请求，只加载对应的直接入口
references/
├─ design.md：记录作者维护的目标设计，运行时仅供说明
├─ initialization.md：执行初始化特有的项目检查并生成候选内容
├─ maintenance.md：作为所有变更的唯一入口，编排选择、执行和结果输出
├─ selection.md：决定候选内容的最终状态与去向
├─ taxonomy.md：定义新建 skill 的分类和名称，并供审计核对
├─ template.md：确定 ctx-* skill 的结构和内容分工
├─ internal-references.md：定义内部 reference 的准入与组织，并供审计核对
├─ structural-changes.md：按需处理文件结构变化的计划、确认和执行
└─ audit.md：执行只读检查并输出审计报告
```

不增加通用执行文件。普通内容的写入由 `maintenance.md` 执行；只有具备独立加载原因的复杂或高风险规则才保留为按需文件。

## 设计约束

- `maintenance.md` 是所有 `ctx-*` skill 及其内部资源变更的唯一入口。
- `initialization.md` 只负责初始化特有的项目检查和候选内容生成，不直接修改 `ctx-*`。
- `audit.md` 只负责检查和报告，不直接修改文件或自动进入维护。
- 具体规则只能由其拥有最终判断权的文件定义，其他文件只负责调用或执行其结果。
- `selection.md` 是所有候选内容的唯一选择入口；`internal-references.md` 不判断内容是否应沉淀。
- `taxonomy.md` 在新建 skill 时确定分类和名称，审计时只读核对现有分类和名称。
- `template.md` 在创建或调整 skill 结构时定义内容分工，审计时只读核对现有结构。
- `structural-changes.md` 只提供结构调整的计划、确认和执行约束，由 `maintenance.md` 按需调用，不作为独立入口。
