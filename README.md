# AI CODE RULES

前端 AI 代码规则集合，用于约束 AI 生成代码的风格、结构与工程实践。

本仓库中的所有 `rules` 与 `skills` 都只适用于前端开发场景，不适用于后端开发、数据库设计、服务端架构、中间件配置、运维脚本等非前端任务。

## 这套仓库怎么用

- `rules`：默认长期生效的硬约束，一旦启用就必须执行，不允许选择性遵守。
- `skills`：按场景触发的方法或工作流；一旦场景匹配并启用，其中的要求也必须执行，不允许只执行一部分。
- 所有规则与技能默认仅约束前端任务；如果当前任务是全栈或前后端联动，也只在前端目录和前端改动内生效。
- 判断标准很简单：
  - 如果内容希望 AI 在大多数任务里都遵守，放 `rules`
  - 如果内容只在特定任务下启用，放 `skills`

## 执行方式

- `rules` 中的内容都是强制规则，不是建议项，不允许 AI 选择性执行。
- `skills` 不是默认全局生效，但一旦任务场景匹配并启用，该 skill 内的要求同样按强制规则处理。
- 如果某条内容希望“只要启用就必须执行”，必须使用 `必须 / 禁止 / 仅允许` 这样的硬约束表达，不要写成“建议 / 优先 / 尽量”。

## 目录说明

### Rules

- `rules/ai-coding-principles.md`：AI 编码通用原则（代码优先、保持简单、可读、可验证）
- `rules/ai-code-quality.md`：前端代码质量与错误处理规范
- `rules/ai-code-aesthetic-order.md`：前端代码审美与结构秩序规则（密度、分层、函数重量、对称性、判断方向）
- `rules/ai-package-strategy.md`：前端依赖策略（统一使用 pnpm，复杂功能优先评估成熟 npm 包）
- `rules/ai-no-loop-api-calls.md`：禁止前端循环调用 API 拼接数据
- `rules/ai-select-use-list-api.md`：选项类组件必须使用 list/专用选项接口
- `rules/api-file-split.md`：前端 API 文件按页面或模块拆分
- `rules/web-development.md`：Vue 3 Web 开发约定
- `rules/ai-change-verification.md`：AI 改动后的验证与回归检查规范

### Skills

- `skills/ai-andrej-karpathy-skills`：复杂功能实现工作流（分阶段推进、快速验证、强调交付质量时调用）
- `skills/ui-design`：UI 设计与视觉规范（做页面/组件/原型时调用）
- `skills/ibm-carbon-design`：IBM Carbon / 企业级秩序感 UI 规范（用户明确要求 Carbon/IBM 风格时调用）
- `skills/spec-workflow`：需求→设计→任务拆解工作流（做新功能/复杂改造时调用）
- `skills/responsive-style-rules`:编写 CSS/样式多端适配规范，避免写死gap或者宽度等
- `skills/debug-workflow`：Bug 排查与修复工作流（定位问题、收集证据、最小修复、验证回归时调用）

## 当前推荐的规则层级

### 一级：默认全局规则

- `rules/ai-coding-principles.md`
- `rules/ai-code-quality.md`
- `rules/ai-code-aesthetic-order.md`

这三份解决“代码基本质量”“输出基本方式”和“代码结构质感”，适合作为底层常驻规则。

### 二级：前端结构和数据获取规则

- `rules/api-file-split.md`
- `rules/ai-no-loop-api-calls.md`
- `rules/ai-select-use-list-api.md`

这几份是最容易被 AI 写偏的地方，建议默认启用。

### 三级：项目/栈约束

- `rules/web-development.md`
- `rules/ai-package-strategy.md`
- `rules/ai-change-verification.md`

这三份更像“技术栈和工程习惯约束”，也建议在前端项目中一起启用。

注意：

- `rules/web-development.md` 是 Vue 3 Web 项目的条件规则，不是所有前端项目都默认启用。
- 若项目本身是 TypeScript，则应继续遵循项目现有语言栈，不应因为该规则而强行改回纯 JavaScript。

## 使用建议

- 将 `rules` 目录作为 AI 编码助手的规则输入来源
- 将 `skills` 作为可按需调用的技能库（仅在匹配场景启用）
- 给 AI 接入本仓库时，应同时明确声明“本仓库所有规则和技能仅适用于前端任务”
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
- 可直接参考 [`RULE_TEMPLATE.md`](/Users/nightwish/practice/ai_code_rules/RULE_TEMPLATE.md) 新建规则。

## 说明

- 本仓库中的所有规则与技能都以“前端场景”作为唯一适用范围，不用于约束后端实现细节。
- 如果未来需要后端规范，应单独建立后端规则仓库或后端专用目录，不建议继续混放在这里。
