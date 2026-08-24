---
name: project-context
description: 当用户明确要求初始化、维护或审计前端项目的持久化上下文，或询问 project-context 的工作方式时使用。负责维护 `.agents/skills/ctx-*` skill 及其内部资源，可根据代码、git diff、项目说明和历史问题处理长期项目知识。不用于普通功能开发、代码修改后的自动更新、后端规则提炼、修改 project-context 自身或维护非 `ctx-*` skill。
---

# 前端项目持久化上下文

## 目标

为前端项目建立和维护最小、准确且可按需加载的 `ctx-*` skill 集合。每个 skill 按职责边界保存后续修改所需的长期知识；复杂但同属该边界的知识可以放入 skill 内部 `references/`，仅在特定任务中读取。

## 适用场景

- **初始化**：分析前端项目，生成初始候选内容，再通过维护流程创建最小的 `ctx-*` 集合。
- **维护**：根据用户请求和可用证据新增、更新、删除或保持现有上下文。
- **审计**：只读检查 `ctx-*` 及其内部资源，不自动修改文件。
- **机制说明**：只读介绍 `project-context` 的职责和调用关系。

## 按场景读取

- 用户询问本机制的工作方式时，只读 [设计说明](references/design.md)。
- 用户要求初始化时，先读取 [初始化规则](references/initialization.md)，再按其指引进入 [维护流程](references/maintenance.md)。
- 用户要求维护时，读取 [维护流程](references/maintenance.md)。
- 用户要求审计时，读取 [审计规则](references/audit.md)。

只加载当前场景的直接入口。`selection.md`、`taxonomy.md`、`template.md`、`internal-references.md` 和 `structural-changes.md` 由对应流程按需读取。

## 只分析或规划

用户明确要求只分析、评估、起草、规划或不要写文件时，仍按对应场景完成范围识别和判断，但不得创建、修改、移动、重命名或删除任何文件。

## 边界

- `maintenance.md` 是 `.agents/skills/ctx-*/` 及其内部资源变更的唯一入口。
- `design.md` 在本 skill 运行时只读；本 skill 不修改自身规则文件。
- 本 skill 不自动响应普通代码或样式修改，也不在代码变更后自动更新上下文。
- 不修改 `docs/`、非 `ctx-*` skill、项目代码、配置或 Git 历史。
- 只将有证据的长期项目知识写入上下文；证据不足、规则冲突或职责不清时列入待确认。
- 不写入敏感值、临时调试信息或未经确认的猜测，并保留工作树中的用户已有改动。
