# AI CODE RULES

前端 AI 代码规则集合，用于约束 AI 生成代码的风格、结构与工程实践。

## 这套仓库怎么用

- `rules`：默认长期生效的硬约束，解决“平时就该怎么写、不该怎么写”。
- `skills`：按场景触发的方法或工作流，解决“遇到某类任务时该怎么推进”。
- 判断标准很简单：
  - 如果内容希望 AI 在大多数任务里都遵守，放 `rules`
  - 如果内容只在特定任务下启用，放 `skills`

## 目录说明

### Rules

- `rules/ai-coding-principles.md`：AI 编码通用原则（代码优先、保持简单、可读、可验证）
- `rules/ai-code-quality.md`：前端代码质量与错误处理规范
- `rules/ai-package-strategy.md`：前端依赖策略（统一使用 pnpm，复杂功能优先评估成熟 npm 包）
- `rules/ai-no-loop-api-calls.md`：禁止前端循环调用 API 拼接数据
- `rules/ai-select-use-list-api.md`：选项类组件必须使用 list/专用选项接口
- `rules/api-file-split.md`：前端 API 文件按页面或模块拆分
- `rules/web-development.md`：Vue 3 Web 开发约定

### Skills

- `skills/ai-andrej-karpathy-skills`：复杂功能实现工作流（分阶段推进、快速验证、强调交付质量时调用）
- `skills/ui-design`：UI 设计与视觉规范（做页面/组件/原型时调用）
- `skills/spec-workflow`：需求→设计→任务拆解工作流（做新功能/复杂改造时调用）
- `skills/responsive-style-rules`:编写 CSS/样式多端适配规范，避免写死gap或者宽度等

## 当前推荐的规则层级

### 一级：默认全局规则

- `rules/ai-coding-principles.md`
- `rules/ai-code-quality.md`

这两份解决“代码基本质量”和“输出基本方式”，适合作为底层常驻规则。

### 二级：前端结构和数据获取规则

- `rules/api-file-split.md`
- `rules/ai-no-loop-api-calls.md`
- `rules/ai-select-use-list-api.md`

这几份是最容易被 AI 写偏的地方，建议默认启用。

### 三级：项目/栈约束

- `rules/web-development.md`
- `rules/ai-package-strategy.md`

这两份更像“技术栈和工程习惯约束”，也建议在前端项目中一起启用。

## 使用建议

- 将 `rules` 目录作为 AI 编码助手的规则输入来源
- 将 `skills` 作为可按需调用的技能库（仅在匹配场景启用）
- 优先把“默认长期生效的约束”放进 `rules`，把“按场景触发的流程/方法/产物模板”放进 `skills`
- 新增规则时，优先使用统一模板，至少包含“适用范围、强制要求、违规示例、正确示例、输出前自检”
- 前后端一期开发时，仅在前端目录启用本仓库规则

## 如何减少 AI 不遵守规则

- 不要只写原则句，要写成“强制要求”。
- 不要只写应该做什么，也要写“违规示例”和“正确示例”。
- 对最重要的规则使用 `alwaysApply: true` 和更高 `priority`。
- 在总规则里重复引用关键子规则，例如在 `web-development.md` 中引用 `api-file-split.md`。
- 在规则末尾加“输出前自检”，让 AI 在生成后再检查一遍。

## 新增规则建议

- 优先新增“具体、可执行、可检查”的规则，不要新增太泛的价值观口号。
- 一条规则最好只解决一类高频跑偏问题。
- 如果一个问题只会出现在特定类型任务里，不要做成 `rule`，而应做成 `skill`。
- 如果某条规则经常不生效，通常不是“规则不够多”，而是“规则不够硬、不够具体、没有反例和自检”。
- 可直接参考 [`rules/RULE_TEMPLATE.md`](/RULE_TEMPLATE.md) 新建规则。

## 说明

- 本仓库规则以“前端场景”优先，不用于约束后端实现细节。
