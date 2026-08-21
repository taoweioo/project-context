---
name: project-context
description: 当用户明确要求初始化、分析、规划、记录、沉淀、补充、完善、修正、更新、整理、删除、拆分、合并、重命名、移动或审计前端项目的持久化上下文时使用。负责维护 `.agents/skills/ctx-*` skill 及其内部资源；适用于根据代码、git diff、业务说明或历史问题沉淀和检查长期项目知识。不用于普通功能开发、代码修改后的自动检查或维护非 `ctx-*` skill。
---

# 前端项目持久化上下文

## 目标

为前端项目建立和维护最小、准确且可按需加载的 `ctx-*` skill 集合。每个 skill 按职责边界保存后续修改所需的长期知识；复杂但同属该边界的知识可作为 skill 内部 `references/` 资源按需读取。

## 适用边界

本 skill 只处理三类用户明确请求：

- **初始化**：分析前端项目，创建初始 `ctx-*` skill 及必要的内部资源。
- **手动维护**：记录、补充、完善、修正或更新已有 `ctx-*` skill，必要时创建新的上下文单元；用户明确要求且确认后可进行结构调整，固定的 `ctx-project-baseline` 仅允许内容维护。
- **审计**：只读检查 `ctx-*` skill 及其内部资源是否过期、重复、误触发、边界不清或存在误导。

本 skill 不处理普通功能开发、代码修改后的自动更新、后端规则提炼、非 `ctx-*` skill、`docs/` 中的任何文件、局部实现细节、临时调试信息或未经确认的猜测。

## 只分析或规划，不写文件

用户明确要求只分析、评估、起草、规划或不要写文件时，可以读取完成判断所需的项目资料、`ctx-*` skill、其内部资源、git diff 和规则引用，并输出建议或结构调整计划；不得创建、修改、移动、重命名、拆分、合并或删除任何 `ctx-*` skill 及其内部资源。

## 所有权边界

- `project-context` 是入口与管理 skill，本身不使用 `ctx-` 前缀。
- 本 skill 只创建和维护 `.agents/skills/ctx-*/` 中属于 `ctx-*` skill 的文件和目录。
- `SKILL.md` 是完整 skill 的必需入口；`references/` 是可选内部资源目录之一，不是完整 skill 结构的定义。
- 其他 `.agents/skills/` 与 `docs/` 可以在必要时只读参考，但不在本机制的维护范围内。

## 按任务读取规则

实际创建或修改 `ctx-*` skill 及其内部资源时，在最终结果中额外输出：`project-context：已更新`。

### 初始化

每次初始化必须读取：

- `references/selection.md`：判断候选知识是否值得提取。
- `references/taxonomy.md`：确定职责边界、目标目录和命名。
- `references/template.md`：生成 `SKILL.md` 并保持内容分工。
- `references/references.md`：判断是否需要 skill 内部参考资料。
- `references/initialization.md`：执行前置检查、分析和生成流程。

### 手动维护

每次手动维护先读取 `references/maintenance.md`。

仅当新建或明显扩展 skill 内部参考资料时，额外读取 `references/references.md`；仅当没有唯一既有归属且可能新建上下文单元时，额外读取 `selection.md`、`taxonomy.md` 和 `template.md`；涉及删除、拆分、合并、重命名、移动或整理 `ctx-*` skill 或其内部资源时，额外读取 `reorganization.md`，先输出计划并等待确认。

### 审计

每次审计必须读取 `audit.md`、`taxonomy.md`、`template.md` 和 `references.md`。审计范围是直接位于 `.agents/skills/` 下的 `ctx-*` skill 及其内部资源，不得修改文件。

## 通用原则

- 用户的明确请求只授权维护 `ctx-*` skill 及其内部资源。
- 用户明确要求记录时，用户对长期保留价值的判断优先；具体处理遵循 `maintenance.md`。
- 是否新建上下文单元必须遵循 `selection.md`；是否新建或扩展内部 reference 必须遵循 `references.md`。
- 已有 skill 职责匹配时优先更新，不新建重复文件。
- 不创建空文件、通用教程、大段源码摘录或重复的权威说明。
- 所有重要结论都注明证据来源；证据不足、规则冲突或职责边界不清时列入待确认，不写成稳定规则。
