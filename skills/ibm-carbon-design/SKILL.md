---
name: 'ibm-carbon-design'
description: 'IBM Carbon/企业级风格的 UI 设计与实现规范。用户要求 IBM/Carbon/企业官网/管理后台风格，或需要“黑白+单一蓝色强调、8px 网格、0 圆角、token 化”时调用。'
---

# IBM Carbon Design Rules

## When to use this skill

Use this skill when the UI needs to match **IBM Carbon / 企业级权威风格**，例如：

- 用户明确说 “IBM / Carbon / Carbon Design System / IBM 风格”
- 企业官网、产品介绍页、B2B 管理后台希望呈现“工程化、克制、秩序感”
- 需要“白底 + 近黑文字 + 单一蓝色强调”的强规范视觉

Scope boundary:

- This skill is frontend-only.
- It applies to frontend pages, components, dashboards, websites, and design implementation work.

Do NOT use for：

- 需要强烈创意、夸张视觉、渐变与装饰为主的营销设计（优先使用 ui-design）
- 没有 UI 交付的纯后端任务

---

## Visual Theme & Tokens

- 基底：白色背景 `#ffffff`，主文字近黑 `#161616`
- 唯一强调色：IBM Blue 60 `#0f62fe`（按钮、链接、焦点、激活态）
- 以语义 token 驱动样式：所有交互/状态颜色都应映射为 CSS 变量（示例命名使用 `--cds-*` 前缀）
- 通过背景分层体现层级：优先使用灰阶层（如 Gray 10 `#f4f4f4`）而不是大量阴影

---

## Color Palette (Quick)

- Background：`#ffffff`
- Text primary：`#161616`
- Accent / Primary CTA：`#0f62fe`
- Surface / Layer 01：`#f4f4f4`
- Border subtle：`#c6c6c6`
- Link hover：`#0043ce`
- Active/pressed：`#002d9c`
- Error：`#da1e28`
- Success：`#24a148`
- Warning：`#f1c21b`

---

## Typography

- 字体家族：
  - Primary：IBM Plex Sans（展示/正文）
  - Mono：IBM Plex Mono（代码、数据、技术标签）
- 权重体系：300（展示）、400（正文）、600（强调/标签），避免 700
- 微字距：
  - 14px：letter-spacing 0.16px
  - 12px：letter-spacing 0.32px
  - 展示字号不加 tracking

---

## Layout & Spacing

- 间距基准：8px（允许 2px/4px 的微调）
- 网格：16 列；内容宽度与断点遵循系统化规则
- 垂直节奏：常用 48px 作为段落/区块过渡的默认节奏

---

## Component Rules

### Buttons

- 主按钮：`#0f62fe` 背景 + 白字，0px 圆角
- 默认高度：48px（紧凑 40px，大号 64px）
- 状态：
  - Hover：更深蓝（如 `#0353e9`）
  - Active：`#002d9c`
  - Focus：2px 蓝色内描边 + 1px 白色内圈（在深色背景上增强可见性）

### Inputs

- 形态：底边框输入（不要四边框）
- 默认：底边 2px 透明；Focus 底边变为 `#0f62fe`；Error 底边 `#da1e28`
- 圆角：0px（与按钮/卡片一致）

### Cards / Tiles

- 扁平为主：通常无阴影、弱边框或纯背景分层
- 可点击卡片 Hover：背景从 `#f4f4f4` 过渡到 `#e8e8e8`

### Navigation

- 深色主导航：`#161616` 背景，48px 高度
- 链接默认灰、hover 变白，激活态使用底边指示线

---

## Do / Don’t (Hard Rules)

### Do

- 使用 0px 圆角作为默认交互语言（按钮/输入/卡片/瓦片）
- 保持“单一蓝色强调”，不要引入第二强调色
- 所有颜色与状态通过 token/变量统一管理
- 用灰阶背景分层表达层级，阴影只用于真正悬浮元素（下拉、tooltip、modal）

### Don’t

- 不要给卡片/瓦片加大阴影作为默认层级手段
- 不要使用渐变背景作为主要视觉语言
- 不要随意偏离 8px 网格（除 2/4px 的微调）

---

## Implementation Checklist (Before Submitting UI)

- 色彩：是否保持“黑白+单一蓝色强调”，没有额外强调色
- 圆角：按钮/输入/卡片是否为 0px（tag/label 允许例外）
- 表单：是否为底边框输入而非四边框
- 间距：是否以 8px 为基准，主节奏是否一致（常见 48px）
- token：关键交互颜色是否由变量驱动，而不是散落硬编码
