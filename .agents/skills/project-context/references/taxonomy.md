# 前端持久化上下文分类规则

本规则在候选内容需要确定分类、创建分类级 `ctx-*` skill，或审计现有分类和名称时使用。候选内容是否应保留及其最终状态由 [选择规则](./selection.md) 决定。

## 分类级 skill

按知识的职责边界和加载原因分类，而不是按代码目录、页面、功能名或单个文件分类。每个固定分类在项目中最多对应一个顶层 skill；分类内仅特定任务需要的知识通过该 skill 的内部 `references/` 按需加载。

目标结构如下：

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

只有分类中存在通过 [选择规则](./selection.md) 的长期知识时，才创建对应 skill；不要预先创建空分类。项目基础固定使用 `ctx-project-baseline`，其他分类使用 `ctx-<分类标识>`。

## 架构语义层级

所有分类在目录中保持同级，不建立嵌套 skill；其中与架构有关的知识按以下语义层级组织：

```text
ctx-project-baseline
项目定位、工程事实和长期约束
        ↓ 约束并解释
ctx-architecture
当前架构、目标架构、演进路径和决策约束
        ↓ 具体化
ctx-modules / ctx-capabilities / ctx-contracts / ctx-integrations / ctx-policies
```

`ctx-project-baseline` 是架构判断的上游依据，回答项目是什么、工程环境如何以及哪些长期前提限制架构选择；`ctx-architecture` 是这些依据在跨模块运行时设计上的展开，回答系统现在如何组织、准备往哪里演进以及迁移期间遵循什么原则。其他分类保存架构在局部流程、复用能力、契约、集成和产品规则上的具体化结果。

该层级只规定职责、阅读关系和可追溯性，不改变“一类一个顶层 skill”的结构，也不允许跨分类复制同一事实。项目事实或工程约束归入 `project`；跨模块系统形态、目标设计和演进决策归入 `architecture`；具体实现知识继续按主要加载原因归入其他分类。

## 固定分类

### 项目基础（`project`）

存放整个前端项目的工程事实和基础约定，与具体页面、产品规则和跨模块运行时设计无关。

其中会限制架构选择的项目定位、技术栈、构建方式和工程约定构成 `architecture` 的上游基线；这里只记录约束本身，不重复由约束推导出的架构决策。

适合记录：技术栈、包管理工具、常用命令、前端源码根目录、目录约定、环境变量入口、构建配置、Lint、格式化、测试体系、全局样式体系、设计令牌和组件库使用约定。

不放入：路由运行时策略、全局缓存机制、跨模块状态流或第三方服务特有约束。

固定路径和名称：`.agents/skills/ctx-project-baseline/SKILL.md`、`ctx-project-baseline`。

### 前端架构（`architecture`）

存放影响多个模块的前端运行时边界、架构决策、跨模块协调机制和有证据的架构演进方向。

适合记录：当前与目标架构、阶段性迁移路径、保留或停止扩展的旧模式、路由组织、服务端与客户端边界、渲染策略、数据请求与缓存、全局状态、事件机制、错误边界、国际化运行时策略、登录态运行时边界和跨模块数据流。

不放入：未产生架构影响的项目工程事实、某个第三方 SDK 的配置细节、具体接口字段语义或产品权限规则。不得把目标设计或迁移计划写成已经存在的当前事实。

固定路径和名称：`.agents/skills/ctx-architecture/SKILL.md`、`ctx-architecture`。

### 页面与局部流程（`modules`）

存放单个页面、业务区域或用户流程的上下文。

适合记录：列表、筛选、详情、图表、弹窗、表单、页面级数据流和页面级交互规则。不同模块或流程使用独立 reference 和读取条件，不为每个模块创建顶层 skill。

不放入：被多个模块复用的能力、跨模块数据契约或跨模块产品规则。

固定路径和名称：`.agents/skills/ctx-modules/SKILL.md`、`ctx-modules`。

### 复用能力（`capabilities`）

存放被多个模块复用、且具有独立职责边界的前端能力或复杂交互组件。

适合记录：文件上传、下载、导出、收藏、分享、跨模块筛选器、复杂表格、AI 任务和识别任务。

如果能力只服务一个模块，应归入 `modules`；不放入全局样式、第三方服务配置或页面局部实现。

固定路径和名称：`.agents/skills/ctx-capabilities/SKILL.md`、`ctx-capabilities`。

### 数据契约（`contracts`）

存放跨模块共享且容易改错的数据语义、字段映射和稳定标识符。

适合记录：接口参数映射、响应字段语义、枚举、缓存键、URL 参数、浏览器存储键、Cookie 名称、事件名称、事件属性和字段迁移规则。

如果契约只属于一个模块且没有复用价值，保留在对应模块 reference 中；不放入第三方鉴权、缓存运行时策略或产品规则。

固定路径和名称：`.agents/skills/ctx-contracts/SKILL.md`、`ctx-contracts`。

### 外部集成（`integrations`）

存放第三方前端服务、SDK、平台或组件库的特有行为和接入约束。

适合记录：支付、埋点平台、错误监控、地图、广告平台、文件存储、AI 服务、身份提供方和第三方组件库的集成约束。

不放入：跨提供方共享的事件字段、项目内部数据契约或通用前端架构策略。

固定路径和名称：`.agents/skills/ctx-integrations/SKILL.md`、`ctx-integrations`。

### 产品规则（`policies`）

存放前端必须遵守的跨模块产品规则和操作限制，不记录后端实现。

适合记录：权限展示、套餐权益、使用额度、前端降级、业务状态、操作入口限制和可见性规则。规则必须有项目说明、明确配置或多处一致实现作为依据，不能仅凭单个界面分支推断。

不放入：登录态的前端运行时边界、身份提供方 SDK 配置或具体接口字段语义。

固定路径和名称：`.agents/skills/ctx-policies/SKILL.md`、`ctx-policies`。

### 其他（`other`）

存放已经通过长期保留判断、但在拆分职责并判断主要加载原因后仍不属于任何现有分类的前端项目知识。

`other` 是分类体系的最终兜底，不是证据不足、规则冲突、临时内容或省略分类判断的默认去向。内容只要能归入 `project`、`architecture`、`modules`、`capabilities`、`contracts`、`integrations` 或 `policies`，就不得写入 `other`。

由于 `other` 内部主题没有共同加载原因，其 `SKILL.md` description 必须按 [结构规则](./template.md) 覆盖当前全部 references 的稳定主题信号，并随 reference 变化同步维护。

固定路径和名称：`.agents/skills/ctx-other/SKILL.md`、`ctx-other`。

## 分类判断顺序

发现候选知识时，按以下顺序判断唯一分类：

1. 项目工程事实或基础约定 → `project`。
2. 跨模块运行时边界或架构决策 → `architecture`。
3. 第三方服务、SDK 或平台特有约束 → `integrations`。
4. 跨模块共享的数据语义、字段映射或稳定标识符 → `contracts`。
5. 跨模块必须遵守的产品规则或操作限制 → `policies`。
6. 被多个模块复用的前端能力或复杂交互组件 → `capabilities`。
7. 单个页面、业务区域或用户流程 → `modules`。
8. 同时涉及多个分类 → 先按职责拆分；无法合理拆分时，选择主要加载原因对应的分类。
9. 已确认需要长期保留，但经过以上判断仍不属于任何现有分类 → `other`。

分类困难本身不进入待确认。待确认只处理证据不足、规则冲突、用户意图不明确或多个权威位置冲突；相关状态由 [选择规则](./selection.md) 决定。

## Reference 主题命名

分类内的按需知识使用以下路径：

```text
.agents/skills/ctx-<分类标识>/references/<读取主题>.md
```

`project` 使用 `ctx-project-baseline/references/<读取主题>.md`。

- `<读取主题>` 使用小写英文和连字符，表达稳定的知识对象或任务边界，例如 `auth-session.md`、`ad-library-export.md`、`subscription-entitlements.md`。
- 不使用 `pages`、`hooks`、`utils`、`components`、`workflow`、`notes` 等宽泛目录或资料类型名称。
- 不使用 `fix`、`new`、`update`、`issue` 等临时词。
- 同一主题不能在多个分类中重复建立；交叉主题按职责拆分，并由各分类 `SKILL.md` 分别路由。

## 旧结构兼容

旧版项目可能存在 `.agents/skills/ctx-<分类标识>-<主题>/SKILL.md`。旧结构识别必须同时满足：分类标识属于本文件定义的固定分类、主题非空，并且目录名不等于 `ctx-project-baseline`。

- 维护和审计时仍将其视为有效范围并读取，不因采用新规则自动移动或删除。
- 不再按旧结构新建顶层 skill，也不向不匹配当前归属的旧 skill 继续扩张内容。
- `ctx-project-baseline` 是 `project` 的规范入口，必须从旧结构识别中排除；其他 `ctx-project-<主题>` 的迁移目标固定为 `ctx-project-baseline`。
- 非 `project` 旧 skill 的迁移目标为 `ctx-<分类标识>`。
- 同分类的旧 skill 是合并到分类级 skill 的结构调整候选；实际迁移必须进入 [结构调整规则](./structural-changes.md) 并获得用户确认。
- 迁移完成前，避免在新旧位置复制同一权威内容；无法确定新旧权威位置时列入待确认。

固定英文分类标识不得通过普通项目维护新增、修改或复用。未覆盖但已确认需要保留的主题归入 `other`；修改分类体系只由 `project-context` 作者维护。

## 禁止的分类方式

- 不按单个页面、功能、文件或代码目录创建顶层 skill。
- 不因为存在 `hooks`、`components`、`utils`、`layouts`、`constants`、`routes` 或 `stores` 目录而创建同名分类。
- 不把权限、缓存、接口契约等跨模块规则塞进第一个使用它的页面 reference。
- 不让一个分类级 skill 承担其他固定分类的知识。
- 不把尚未确认、可归入现有分类或只是暂时难以判断的内容堆入 `other`。
