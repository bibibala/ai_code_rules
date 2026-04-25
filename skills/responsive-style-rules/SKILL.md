---
name: responsive-style-rules
description: 编写前端 CSS/样式代码时强制执行响应式适配规范和间距规范。当 AI 编写任何涉及宽度、高度、间距（margin/padding）、布局的前端样式代码时触发，避免写死尺寸、避免用 margin/padding 直接处理组件间距。适用于前端 CSS、Tailwind、SCSS、styled-components、uni-app、React Native 等样式场景。
---

# 响应式样式规范 (Responsive Style Rules)

## 适用范围

- 仅适用于前端样式开发，包括 Web、H5、小程序、React Native、uni-app 等前端界面样式场景。
- 不适用于后端模板渲染规范、服务端输出格式、数据库字段设计或非前端任务。

## 核心原则

编写样式时必须遵守以下三大规范，任何违反都视为不合格代码。

---

## 规范零：优先使用 UI 组件库的响应式方案（最高优先级）

### 判断流程

在编写任何响应式布局或间距代码之前，**必须先判断项目是否使用了 UI 组件库**，并按以下优先级决策：

```
项目是否使用 UI 组件库？
  ├─ 是 → 组件库是否提供响应式 API / Layout 组件？
  │         ├─ 是 → ✅ 必须使用组件库方案，禁止手写 @media 查询
  │         └─ 否 → 使用规范一/二的通用 CSS 方案
  └─ 否 → 使用规范一/二的通用 CSS 方案
```

**手写 `@media` 媒体查询仅在以下情形允许：**
1. 项目没有引入任何 UI 组件库
2. 组件库明确不支持该场景的响应式（需注释说明原因）
3. 需要覆盖组件库样式的特殊定制需求

### 常见组件库的响应式方案

**Ant Design（antd）**
```jsx
import { Row, Col, Grid, Space, Flex } from 'antd';
const { useBreakpoint } = Grid;

// ✅ 用 Row/Col 的 响应式 span 做布局
<Row gutter={[16, 24]}>
  <Col xs={24} sm={12} md={8} lg={6}>内容</Col>
  <Col xs={24} sm={12} md={8} lg={6}>内容</Col>
</Row>

// ✅ 用 Space / Flex 处理间距
<Space direction="vertical" size="middle">
  <Card />
  <Card />
</Space>

<Flex gap="middle" wrap>
  <Button />
  <Button />
</Flex>

// ✅ 用 useBreakpoint Hook 处理逻辑分支
const screens = useBreakpoint();
const span = screens.lg ? 6 : screens.md ? 8 : screens.sm ? 12 : 24;
```

**Element Plus（Vue）**
```vue
<!-- ✅ 用 el-row / el-col 响应式布局 -->
<el-row :gutter="20">
  <el-col :xs="24" :sm="12" :md="8" :lg="6">内容</el-col>
</el-row>

<!-- ✅ 用 el-space 处理间距，不手写 margin -->
<el-space direction="vertical" :size="16">
  <el-card />
  <el-card />
</el-space>
```

**Naive UI（Vue）**
```vue
<n-grid :cols="{ xs: 1, sm: 2, md: 3, lg: 4 }" :x-gap="16" :y-gap="16">
  <n-gi><Card /></n-gi>
  <n-gi><Card /></n-gi>
</n-grid>

<n-space vertical :size="16">
  <n-card />
  <n-card />
</n-space>
```

**Vuetify（Vue）**
```vue
<v-row>
  <v-col cols="12" sm="6" md="4" lg="3">内容</v-col>
</v-row>
```

**MUI / Material UI（React）**
```jsx
import { Grid2, Stack, Box } from '@mui/material';
import { useMediaQuery, useTheme } from '@mui/material';

// ✅ 响应式 Grid
<Grid2 container spacing={2}>
  <Grid2 size={{ xs: 12, sm: 6, md: 4 }}>内容</Grid2>
</Grid2>

// ✅ Stack 处理间距
<Stack direction={{ xs: 'column', sm: 'row' }} spacing={2}>
  <Button />
  <Button />
</Stack>

// ✅ 断点逻辑用 Hook，不手写 @media
const theme = useTheme();
const isMobile = useMediaQuery(theme.breakpoints.down('sm'));
```

**Arco Design（React/Vue）**
```jsx
import { Grid, Space } from '@arco-design/web-react';
const { Row, Col } = Grid;

<Row gutter={[16, 16]}>
  <Col xs={24} sm={12} md={8}>内容</Col>
</Row>

<Space direction="vertical" size={16}>
  <Card />
  <Card />
</Space>
```

**uni-app（小程序/H5/App）**
```vue
<!-- ✅ 使用 rpx 单位自适应，或 uni-row/uni-col -->
<uni-row :gutter="10">
  <uni-col :span="12"><view>内容</view></uni-col>
  <uni-col :span="12"><view>内容</view></uni-col>
</uni-row>

<!-- ✅ 使用 uni-ui 的 Space 组件 -->
<uni-space direction="column" gap="16">
  <uni-card />
</uni-space>
```

### ❌ 有组件库时的禁止行为

```jsx
// ❌ 项目用了 antd 却手写媒体查询
<div style={{ display: 'flex' }}>
  <style>{`
    @media (max-width: 768px) { .box { flex-direction: column; } }
  `}</style>
</div>

// ✅ 改用 antd Flex 的 responsive 属性
<Flex vertical={{ xs: true, sm: false }}>...</Flex>
```

```css
/* ❌ 项目用了 Element Plus 却手写断点 */
@media (max-width: 768px) {
  .card-list { grid-template-columns: 1fr; }
}

/* ✅ 改用 el-row + el-col 的 xs/sm/md/lg span */
```

---

## 规范一：禁止写死宽高尺寸

### ❌ 禁止行为

```css
/* 禁止：写死像素宽高 */
.container { width: 375px; height: 812px; }
.card { width: 320px; }
.modal { width: 500px; height: 300px; }
.banner { height: 200px; }
```

### ✅ 正确做法

**宽度适配：**
```css
/* 使用百分比或视口单位 */
.container { width: 100%; max-width: 1200px; }
.card { width: 100%; }
.sidebar { width: clamp(200px, 25%, 320px); }

/* 使用 CSS Grid / Flexbox 自适应 */
.grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); }
```

**高度适配：**
```css
/* 使用 min-height 而非固定 height */
.hero { min-height: 60vh; }
.section { min-height: 400px; } /* 仅在必要时作为最小保底 */

/* 让内容撑开高度 */
.card { height: auto; }

/* 使用 aspect-ratio 保持比例 */
.thumbnail { width: 100%; aspect-ratio: 16 / 9; }
.avatar { width: 48px; aspect-ratio: 1; }
```

**断点响应式：**
```css
/* 移动优先 (Mobile First) */
.container {
  width: 100%;
  padding: 0 16px;
}
@media (min-width: 768px) {        /* tablet */
  .container { padding: 0 32px; }
}
@media (min-width: 1280px) {       /* desktop */
  .container { max-width: 1200px; margin: 0 auto; }
}
```

**Tailwind 写法：**
```html
<!-- ❌ 禁止 -->
<div class="w-[375px] h-[200px]">

<!-- ✅ 正确 -->
<div class="w-full max-w-5xl min-h-[200px]">
<div class="w-full md:w-1/2 lg:w-1/3">
<div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
```

---

## 规范二：禁止用 margin/padding 直接处理组件间距

### 核心问题

用 `margin` 直接撑开两个兄弟组件之间的距离，会导致：
- 布局逻辑分散，难以维护
- 删除/移动组件时破坏整体间距
- 组件本身不独立，与上下文强耦合

### ❌ 禁止行为

```css
/* 禁止：用 margin 硬撑两个组件之间的距离 */
.card { margin-bottom: 24px; }          /* 不知道下面是什么 */
.section-title { margin-top: 48px; }    /* 不知道上面是什么 */
.button { margin-left: 16px; }          /* 强行和左边拉开距离 */

/* 禁止：在子组件里用 margin 对抗父组件的 padding */
.list-item { margin: 0 -16px; }         /* 负 margin 打补丁 */
```

```jsx
// 禁止：直接在组件样式里写与外部关系相关的 margin
<Card style={{ marginBottom: '24px' }} />
<Button style={{ marginLeft: '16px' }} />
```

### ✅ 正确做法

**方案一：父容器用 gap 统一控制子元素间距（首选）**
```css
/* Flex 布局：用 gap */
.button-group {
  display: flex;
  align-items: center;
  gap: 12px;          /* 所有子元素之间统一间距 */
}

/* Grid 布局：用 gap */
.card-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;          /* 行列间距统一管理 */
}
```

**方案二：使用 Space / Stack 布局组件（组件库场景）**
```jsx
// 使用间距组件包裹，而非直接给子组件加 margin
<Space direction="vertical" size={16}>
  <CardA />
  <CardB />
  <CardC />
</Space>

// 或 Stack 模式
<Stack gap={24}>
  <Section />
  <Section />
</Stack>
```

**方案三：流式文本内容用 > * + * 选择器**
```css
/* 文章/内容区域的段落间距，使用相邻兄弟选择器 */
.article-content > * + * {
  margin-top: 1.5em;
}
/* 等价 Tailwind: space-y-6 */
```

**方案四：组件内部的 padding（组件自身内边距，不影响外部）**
```css
/* ✅ padding 是组件自己的内边距，这是合理的 */
.card {
  padding: 20px 24px;    /* 卡片内容与边框的距离，属于组件自身 */
}

/* ❌ margin 不能用于处理组件与外部其他组件的关系 */
.card {
  margin-bottom: 20px;   /* 这是在影响外部关系，不合规 */
}
```

**Tailwind 正确写法：**
```html
<!-- ❌ 禁止：在子组件上直接加间距 -->
<div>
  <Card class="mb-6" />
  <Card class="mb-6" />
</div>

<!-- ✅ 正确：父容器统一用 space-y 或 gap 控制 -->
<div class="flex flex-col gap-6">
  <Card />
  <Card />
</div>

<!-- ✅ 正确：Flex 横向排列 -->
<div class="flex items-center gap-3">
  <Button />
  <Button />
</div>
```

---

## 断点规范参考

| 断点名 | 宽度 | 适用场景 |
|--------|------|----------|
| `xs`   | < 480px  | 小屏手机 |
| `sm`   | ≥ 480px  | 手机横屏 / 大屏手机 |
| `md`   | ≥ 768px  | 平板竖屏 |
| `lg`   | ≥ 1024px | 平板横屏 / 笔记本 |
| `xl`   | ≥ 1280px | 桌面显示器 |
| `2xl`  | ≥ 1536px | 宽屏显示器 |

---

## 移动端特殊适配

```css
/* 安全区域适配（刘海屏 / 底部手势条）*/
.header {
  padding-top: env(safe-area-inset-top);
}
.footer {
  padding-bottom: calc(16px + env(safe-area-inset-bottom));
}

/* 图片自适应 */
img {
  max-width: 100%;
  height: auto;
  display: block;
}

/* 字体响应式 */
h1 { font-size: clamp(1.5rem, 4vw, 2.5rem); }
p  { font-size: clamp(0.875rem, 2vw, 1rem); }
```

---

## 代码审查清单

在生成任何样式代码前，必须自查：

- [ ] 项目是否用了 UI 组件库？→ 先查组件库有无 Row/Col、Grid、Space、Flex 等布局组件
- [ ] 是否在有组件库的情况下手写了 `@media` 查询？→ 改用组件库的响应式 API
- [ ] 是否有 `width: Xpx`（非 max-width / min-width）？→ 改为百分比或 clamp()
- [ ] 是否有 `height: Xpx`？→ 改为 `min-height` 或 `aspect-ratio`
- [ ] 是否用 `margin` 控制两个兄弟/相邻组件间的距离？→ 改用父容器 `gap` 或组件库 Space
- [ ] 是否移动端没有对应断点？→ 用组件库断点 prop，或补充 Mobile First @media
- [ ] 组件是否有 `margin-top/bottom` 依赖外部环境？→ 交由父容器的 `gap` 或 Space 组件控制

---

## 例外情形（允许写固定值的场景）

以下场景允许使用固定像素值：
1. **图标/头像尺寸**：`width: 24px; height: 24px;`（配合 `aspect-ratio: 1` 更佳）
2. **边框/圆角**：`border-radius: 8px;`
3. **最小可点击区域**：`min-width: 44px; min-height: 44px;`（无障碍要求）
4. **固定工具栏高度**：`height: 56px;`（需同时处理安全区域）
5. **组件自身 padding**：卡片/按钮自身的内边距（不影响外部关系）
