---
name: responsive-style-rules
description: 编写前端 CSS/样式代码时强制执行响应式适配规范和间距规范。当 AI 编写任何涉及宽度、高度、间距（margin/padding）、布局的前端样式代码时触发，避免写死尺寸、避免用 margin/padding 直接处理组件间距。适用于前端 CSS、Tailwind、SCSS、styled-components、uni-app 等样式场景。
---

# 响应式样式规范 (Responsive Style Rules)

## 适用范围

- 仅适用于前端样式开发，包括 Web、H5、小程序、uni-app 等前端界面样式场景。
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

**Vuetify 3（Vue 3）**
```vue
<script setup>
import { useDisplay } from 'vuetify'

// ✅ 用 useDisplay Hook 处理逻辑分支，禁止手写 @media
const { mobile, smAndDown, mdAndUp } = useDisplay()
</script>

<template>
  <!-- ✅ 用 v-row / v-col 响应式布局 -->
  <v-row>
    <v-col cols="12" sm="6" md="4" lg="3">内容</v-col>
    <v-col cols="12" sm="6" md="4" lg="3">内容</v-col>
  </v-row>

  <!-- ✅ 用 v-container 控制最大宽度 -->
  <v-container>
    <v-row>...</v-row>
  </v-container>

  <!-- ✅ 响应式显隐用 display class，不手写媒体查询 -->
  <div class="d-none d-sm-flex">仅平板及以上显示</div>
  <div class="d-flex d-sm-none">仅手机显示</div>

  <!-- ✅ 用 v-spacer 处理 Flex 间距 -->
  <div class="d-flex align-center ga-3">
    <v-btn />
    <v-btn />
  </div>
</template>
```

**Quasar（Vue 3）**
```vue
<script setup>
import { useQuasar } from 'quasar'

// ✅ 用 $q.screen 处理断点逻辑，禁止手写 @media
const $q = useQuasar()
const cols = computed(() => $q.screen.lt.md ? 12 : $q.screen.lt.lg ? 6 : 4)
</script>

<template>
  <!-- ✅ 用 QGrid / col-* 响应式布局 -->
  <div class="row q-col-gutter-md">
    <div class="col-12 col-sm-6 col-md-4 col-lg-3">内容</div>
    <div class="col-12 col-sm-6 col-md-4 col-lg-3">内容</div>
  </div>

  <!-- ✅ 用 q-gutter-* 处理子元素间距，不手写 margin -->
  <div class="row q-gutter-md">
    <q-card />
    <q-card />
  </div>

  <!-- ✅ 响应式显隐用内置 class -->
  <div class="lt-md">仅手机/平板显示</div>
  <div class="gt-sm">仅桌面显示</div>

  <!-- ✅ Flex 间距用 q-gutter 或 gap class -->
  <div class="row items-center q-gutter-sm">
    <q-btn />
    <q-btn />
  </div>
</template>
```

### ❌ 有组件库时的禁止行为

```vue
<!-- ❌ 项目用了 Vuetify 3 却手写媒体查询 -->
<style scoped>
@media (max-width: 768px) {
  .card-list { flex-direction: column; }
}
</style>

<!-- ✅ 改用 v-row + v-col 的断点 prop -->
<v-row>
  <v-col cols="12" sm="6">...</v-col>
</v-row>
```

```vue
<!-- ❌ 项目用了 Quasar 却手写媒体查询 -->
<style scoped>
@media (max-width: 1024px) {
  .grid { grid-template-columns: 1fr 1fr; }
}
</style>

<!-- ✅ 改用 Quasar col-* 响应式 class -->
<div class="row q-col-gutter-md">
  <div class="col-12 col-md-6">...</div>
</div>
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

**断点响应式（无组件库时）：**
```css
/* 移动优先 (Mobile First) */
.container {
  width: 100%;
  padding: 0 16px;
}
@media (min-width: 768px) {
  .container { padding: 0 32px; }
}
@media (min-width: 1280px) {
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
.card { margin-bottom: 24px; }
.section-title { margin-top: 48px; }
.button { margin-left: 16px; }

/* 禁止：负 margin 打补丁 */
.list-item { margin: 0 -16px; }
```

```vue
<!-- 禁止：直接在组件上写与外部关系相关的 margin -->
<q-card style="margin-bottom: 24px" />
<v-btn style="margin-left: 16px" />
```

### ✅ 正确做法

**方案一：父容器用 gap 统一控制子元素间距（首选）**
```css
/* Flex 布局：用 gap */
.button-group {
  display: flex;
  align-items: center;
  gap: 12px;
}

/* Grid 布局：用 gap */
.card-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;
}
```

**方案二：Vuetify 3 用 gutter / ga-* class**
```vue
<!-- ✅ v-row 的 gutter 控制列间距 -->
<v-row>
  <v-col cols="6"><v-card /></v-col>
  <v-col cols="6"><v-card /></v-col>
</v-row>

<!-- ✅ Flex 容器用 ga-* (gap) class -->
<div class="d-flex align-center ga-3">
  <v-btn>按钮A</v-btn>
  <v-btn>按钮B</v-btn>
</div>

<!-- ✅ 垂直堆叠用 ga-* -->
<div class="d-flex flex-column ga-4">
  <v-card />
  <v-card />
</div>
```

**方案三：Quasar 用 q-gutter-* / q-col-gutter-***
```vue
<!-- ✅ 子元素间距用 q-gutter-* -->
<div class="row q-gutter-md">
  <q-card />
  <q-card />
</div>

<!-- ✅ Grid 列间距用 q-col-gutter-* -->
<div class="row q-col-gutter-lg">
  <div class="col-12 col-md-6"><q-card /></div>
  <div class="col-12 col-md-6"><q-card /></div>
</div>

<!-- ✅ 垂直堆叠 -->
<div class="column q-gutter-y-md">
  <q-card />
  <q-card />
</div>
```

**方案四：流式文本内容用相邻兄弟选择器**
```css
/* 文章/内容区域的段落间距 */
.article-content > * + * {
  margin-top: 1.5em;
}
```

**方案五：组件内部 padding（组件自身内边距，合理使用）**
```css
/* ✅ padding 是组件自己的内边距，这是合理的 */
.card {
  padding: 20px 24px;
}

/* ❌ margin 不能用于处理组件与外部其他组件的关系 */
.card {
  margin-bottom: 20px;
}
```

---

## 断点规范参考

| 断点名 | 宽度       | Vuetify 3       | Quasar         |
|--------|------------|-----------------|----------------|
| `xs`   | < 600px    | `xs`            | `xs`           |
| `sm`   | ≥ 600px    | `sm`            | `sm`           |
| `md`   | ≥ 960px    | `md`            | `md`           |
| `lg`   | ≥ 1280px   | `lg`            | `lg`           |
| `xl`   | ≥ 1920px   | `xl`            | `xl`           |

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

- [ ] 项目是否用了 Vuetify 3 / Quasar？→ 先用 v-row/v-col 或 row/col-* 做布局，禁止手写 @media
- [ ] 是否在有组件库的情况下手写了 `@media` 查询？→ 改用组件库的断点 prop / class
- [ ] 是否有 `width: Xpx`（非 max-width / min-width）？→ 改为百分比或 clamp()
- [ ] 是否有 `height: Xpx`？→ 改为 `min-height` 或 `aspect-ratio`
- [ ] 是否用 `margin` 控制两个兄弟/相邻组件间的距离？→ 改用父容器 `gap`、`ga-*`（Vuetify）或 `q-gutter-*`（Quasar）
- [ ] 是否移动端没有对应断点？→ 用组件库断点 prop，或补充 Mobile First @media
- [ ] 组件是否有 `margin-top/bottom` 依赖外部环境？→ 交由父容器的 `gap` 或 gutter 控制

---

## 例外情形（允许写固定值的场景）

以下场景允许使用固定像素值：
1. **图标/头像尺寸**：`width: 24px; height: 24px;`（配合 `aspect-ratio: 1` 更佳）
2. **边框/圆角**：`border-radius: 8px;`
3. **最小可点击区域**：`min-width: 44px; min-height: 44px;`（无障碍要求）
4. **固定工具栏高度**：`height: 56px;`（需同时处理安全区域）
5. **组件自身 padding**：卡片/按钮自身的内边距（不影响外部关系）
