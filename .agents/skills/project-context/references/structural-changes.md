# ctx skill 结构调整规则

本规则只由 [维护流程](./maintenance.md) 在删除、拆分、合并、重命名、移动或整理 `ctx-*` skill 及其内部文件时按需调用。它负责安全执行文件结构变化，不决定内容是否保留或归属哪里。

## 范围

只调整直接位于 `.agents/skills/` 下的 `ctx-*` 目录及其内部文件。不调整 `project-context`、非 `ctx-*` skill、项目代码、配置、Git 历史或 `docs/`。已存在的 `ctx-project-baseline` 不得删除、重命名或移动；首次创建由维护流程直接执行。

## 计划与确认

读取全部受影响的 skill、内部 resource、指向它们的链接和必要证据。涉及内容保留、迁移或删除时，先取得 [选择规则](./selection.md) 的结果。结构调整计划至少列出：

- 目标、原因和期望结构；
- 受影响的 skill、内部 resource 与链接；
- 每项内容的选择结果和目标位置；
- 文件创建、移动、重命名或删除操作；
- 重名、重复、冲突和证据缺口；
- 风险和完成检查。

未获得用户明确确认前，不修改任何受影响文件。确认后重新读取目标和链接，只执行已确认计划。

## 旧 skill 合并到分类级 skill

将旧版 `ctx-<分类>-<主题>` 合并到分类级 skill 时，必须先排除规范入口 `ctx-project-baseline`。`project` 的目标固定为 `ctx-project-baseline`，其他分类的目标为 `ctx-<分类>`。

1. 枚举同分类的全部旧 skill、内部 resource、相互链接和用户已有改动；不得把 `ctx-project-baseline` 计入旧 skill。
2. 使用 `selection.md` 为每项内容确定保留状态和唯一分类；不把整份旧文件不加判断地搬运。
3. 按 [结构规则](./template.md) 把分类共同约束放入目标 `SKILL.md`，按 [内部 reference 规则](./internal-references.md) 把主题知识合并或转换为 references。
4. 为每个保留的 reference 在目标 `SKILL.md` 增加精确读取条件；需要联合理解时增加组合读取条件。
5. 合并重复主题，解决文件名和权威结论冲突；证据不足或用户规则冲突时停止相关项并列入待确认。
6. 更新所有受影响链接。确认旧 skill 的保留内容和链接均已处理后，才删除旧目录。
7. 验证目标 skill 的 frontmatter、完整路由、相对路径，以及工作树中不存在意外修改；目标为 `ctx-other` 时额外验证 description 覆盖全部稳定主题信号。

可以一次迁移一个分类，不要求所有分类同时迁移。迁移期间不得在新旧位置保留同一内容的两个权威副本。

## 其他执行约束

- 首次创建分类级 skill 时按 [分类规则](./taxonomy.md) 和 `template.md` 创建，不重新判断内容价值。
- 迁移或拆分 reference 时保持准入价值和语义路由，不跨分类复制。
- 重命名或移动时更新所有相对链接、frontmatter `name` 和路由路径。
- 删除 skill 或内部 resource 前，确认计划已说明其中内容和链接的处理结果。
- 不因结构调整自动改写用户明确规则；其状态和去向遵循 `selection.md` 的结果。

执行后将实际变更、链接更新和验证结果返回 [维护流程](./maintenance.md)，由维护流程统一输出。
