# 前端项目持久化上下文

## 安装与更新

进入需要使用该 skill 的项目目录后安装：

```bash
npx skills add taoweioo/project-context -y
```

后续更新：

```bash
npx skills update project-context -p
```

## 项目设想

本项目探索一种面向前端项目 AI 开发的持久化上下文机制：将复杂、特殊、容易改错且无法从当前代码直接得出的长期知识，沉淀为可按需加载的 `ctx-*` skill，减少遗漏约定和重复引入历史问题的风险。

本机制不维护 `docs/`，也不试图定义完整的 agent harness。它只维护 `ctx-*` skill 及其内部资源。

## 核心概念

### 分类级上下文

项目使用少量分类级 skill，而不是为每个页面或功能创建独立 skill：

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

每个固定分类最多对应一个顶层 skill；只有存在通过长期保留判断的内容时才创建。`ctx-other` 只承载已确认需要保留、但经过职责拆分和主要加载原因判断后仍不属于现有分类的内容。旧版 `ctx-<分类>-<主题>` 可以继续读取，并通过用户确认的结构调整逐类迁移。

### SKILL.md 是语义路由入口

分类级 `SKILL.md` 包含触发边界、负责范围、分类共同约束和内部 reference 索引。索引必须说明“什么任务或代码信号读取哪个文件”，不能只是文件名列表。

```md
## 按需读取

| 任务或代码信号 | 读取 |
| --- | --- |
| 登录、登出、session 或路由守卫 | [认证与会话](./references/auth-session.md) |
| Server Component、`"use client"` 或 hydration | [Server/Client 边界](./references/server-client-boundaries.md) |
```

Codex 只读取与当前任务匹配的 reference；任务跨越多个主题时，`SKILL.md` 还应说明组合读取条件。

### 内部 references

`references/` 承载分类内仅特定任务需要的长期知识，不是项目级文档库。

仅当知识具有明确读取条件、不读取会影响后续判断、属于当前固定分类并有可靠证据时，才写入其中。典型内容包括模块流程、状态机、复杂决策、跨文件关系、特定集成约束和字段语义。

每个 reference 必须从所属 `SKILL.md` 的语义路由直接到达；孤立文件、模糊的“需要时读取”和默认加载全部 reference 都不符合目标设计。

## 核心原则

- 每个固定分类最多一个顶层 `ctx-*` skill，不按页面、功能、文件或代码目录创建顶层 skill。
- `SKILL.md` 保留分类共同约束并负责路由；reference 保存分类内按需主题知识。
- 已有职责匹配的分类 skill 或 reference 优先更新，不创建重复位置。
- 已确认需要保留的内容必须给出分类意见；无法归入现有分类时使用 `ctx-other`，不因分类困难长期待确认。
- 待确认只用于证据不足、规则冲突、用户意图不明确或多个权威位置冲突，不能通过 `ctx-other` 绕过。
- 每条重要规则有代码、配置、项目指令或用户明确说明作为依据。
- 用户明确要求长期保留的规则记录来源、日期和状态，不由代码观察自动覆盖。
- 不沉淀一眼可见的实现、临时调试信息、通用教程、大段源码或未经确认的猜测。
- 普通代码任务结束后不自动检查或更新上下文；只有用户明确要求时才维护。
- 合并、移动、重命名或删除旧结构前，先输出计划并等待确认。

## 使用方式

```text
初始化这个前端项目的持久化上下文。

根据这次 git diff 更新持久化上下文。

把同分类的旧 ctx skill 合并到分类级 skill，并建立 reference 路由。

审计现有 ctx-* 的分类、语义路由和内部 references。
```

实际创建或修改 `ctx-*` skill 及其内部资源时，skill 会额外输出：

```text
project-context：已更新
```
