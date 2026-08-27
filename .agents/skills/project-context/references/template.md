# ctx skill 结构

本规则在新建或调整 `ctx-*` skill 结构时确定内容分工，并在审计时只读核对现有结构。候选内容是否应保留及其分类由 [选择规则](./selection.md) 决定。

每个固定分类最多对应一个顶层 skill。分类级 `SKILL.md` 是必需入口，负责定义触发边界、保存分类共同约束，并把具体任务路由到内部 references。

## 组成部分与分工

| 内容 | 位置 | 适用条件 |
| --- | --- | --- |
| 分类 skill 的触发条件 | `SKILL.md` frontmatter 的 `description` | 通常概括该分类覆盖的任务和必要排除范围；`ctx-other` 还必须覆盖当前 references 的稳定主题信号。 |
| 分类职责、共同约束和高价值入口 | `SKILL.md` 正文 | 每次使用该分类都需要，且能够用简短、独立条目表达。 |
| reference 语义路由 | `SKILL.md` 正文 | 列出每个 reference 的任务或代码信号、相对路径和组合读取条件。 |
| 特定主题或子任务知识 | `references/` | 只在路由条件匹配时读取，不应默认全部加载。 |
| 可重复且需要确定结果的操作 | `scripts/` | 重复执行或确定性操作能够用脚本提高可靠性。 |
| 用于生成交付物的材料 | `assets/` | 会被复制、嵌入或改造成输出，不作为 agent 指令加载。 |

`SKILL.md` 不重复 reference 的详细内容。reference 也不重复分类职责或共同约束。

## 分类级 SKILL.md 模板

`SKILL.md` 至少包含 frontmatter、一级标题和 `负责范围`。存在分类共同约束时增加 `共同约束` 和对应的 `证据来源`；存在 reference 时必须增加 `按需读取`。没有内容的章节不保留。

```md
---
name: ctx-architecture
description: 当修改项目的跨模块前端运行时边界、路由、渲染、数据请求或全局状态时使用；不适用于页面局部交互、第三方 SDK 配置或产品权限规则。
---

# 前端架构上下文

## 负责范围

记录跨模块的前端运行时边界、架构决策和有证据的演进方向。

只读取与当前任务匹配的 reference，不默认读取全部文件。

## 共同约束

- 写明每次进入该分类都必须遵守的少量长期约束。

## 按需读取

| 任务或代码信号 | 读取 |
| --- | --- |
| 登录、登出、session、路由守卫或受保护页面 | [认证与会话](./references/auth-session.md) |
| `"use client"`、Server Component 或 hydration 边界 | [Server/Client 边界](./references/server-client-boundaries.md) |

涉及受保护的 Server Component 时，同时读取以上两份 reference。

## 证据来源

- 共同约束：项目指令 `AGENTS.md`
```

只有确有必要时才增加 `不负责范围`、`关键入口与对象`、`用户明确规则`、`相关分类` 和 `待确认事项`；不保留空标题。

## 架构层级特例

`ctx-project-baseline` 和 `ctx-architecture` 在目录中保持同级，但生成出的入口必须能看出 [分类规则](./taxonomy.md) 定义的上游与下游关系：

- 两个 skill 同时存在时，`ctx-project-baseline/SKILL.md` 的 `相关分类` 指向 `../ctx-architecture/SKILL.md`，说明跨模块运行时设计和架构演进由后者负责。
- 两个 skill 同时存在时，`ctx-architecture/SKILL.md` 的 `相关分类` 指向 `../ctx-project-baseline/SKILL.md`，说明项目事实和长期工程约束是其上游依据。
- 跨 skill 只链接分类级 `SKILL.md`，不直接依赖对方的内部 reference，也不复制对方的事实或约束。
- 只有一个 skill 存在时不为补齐层级而创建空分类；以后创建另一个 skill 时再同步建立关系。

`ctx-architecture` 中有目标设计或迁移证据时，必须让读者能够区分以下信息：

1. 当前架构：代码和配置能够证明的现状；
2. 目标架构：用户说明、项目指令或已确认方案定义的预期状态；
3. 演进路径：从当前状态到目标状态的阶段、兼容方式或停止扩展范围；
4. 决策约束：迁移期间不能破坏的边界和架构选择依据。

这些信息影响所有架构任务时放入 `SKILL.md` 的 `架构走向`；仅影响特定主题时放入对应 reference，并从 `按需读取` 路由。没有可靠目标或迁移证据时只记录当前架构，不创建空章节、不推测目标，也不把计划状态表述成当前事实。

## ctx-other 触发特例

`ctx-other` 没有统一领域触发范围，不能只使用“其他前端知识”或“无法分类的内容”等宽泛 description。其 frontmatter `description` 必须同时包含：

- `other` 的兜底边界和必要排除范围；
- 当前每个 reference 的稳定任务、领域词或代码信号的简短并集，不写文件名或临时实现细节。

新增、删除、重命名 reference，或 reference 的读取条件发生实质变化时，必须同步更新 description 和“按需读取”。若当前主题信号无法在保持准确的前提下简短表达，应重新分类或将分类体系扩展列为待确认，不能退化成无边界的宽泛 description。

## 路由要求

- 每个 reference 必须能从 `SKILL.md` 的 `按需读取` 直接到达。
- 路由项必须说明可识别的任务、领域词或代码信号，不能只有文件名和内容标题。
- 多份 reference 经常需要联合理解时，在 `SKILL.md` 中写明组合读取条件，不复制其内容。
- 新增、重命名、移动或删除 reference 时，同步更新路由；不存在的路径和孤立文件都属于结构问题。
- reference 很多时可按领域分组路由，但仍由一个 `按需读取` 章节提供完整入口。
