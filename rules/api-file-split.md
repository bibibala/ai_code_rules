---
name: api-file-split
description: Vue3前端 API 文件按页面拆分，禁止集中在单一文件
alwaysApply: true
priority: 2
---

# API 文件结构规范

## 适用范围

- 仅适用于前端工程目录（如 `src/api`）。
- 不适用于后端项目的 controller/service/repository 分层或包结构。
- 前后端一期开发时，本规则只约束前端 API 文件组织方式。

## 强制要求

- 严禁把多个页面或多个业务模块的接口集中写在一个文件中。
- 每次新增页面或模块接口时，必须新建对应 API 文件或对应目录。
- 一个 API 文件只服务一个页面、一个业务域，或一个清晰的公共能力。
- `index.ts` 仅允许做聚合导出，禁止写真实请求实现。
- 页面、组件、store 中禁止直接写 `fetch` / `axios` / `request.xxx(...)`。
- 若发现历史代码已把多个业务域堆在同一文件，当前任务涉及该区域时，优先先拆分再继续新增。

## 推荐目录结构

### 扁平结构

```text
src/api/
├── user.ts
├── order.ts
├── dashboard.ts
```

### 目录结构（复杂项目）

```text
src/api/
├── user/
│   └── index.ts
├── order/
│   └── index.ts
├── dashboard/
│   └── index.ts
```

## 文件拆分规则

- 一个页面或模块对应一个 API 文件，例如：
  - `src/api/user.ts`
  - `src/api/order.ts`
  - `src/api/dashboard.ts`
- 少量跨页面复用的能力允许单独归类，但必须是“公共能力”，例如：
  - `src/api/common.ts`
  - `src/api/shared/upload.ts`
  - `src/api/shared/dict.ts`
- `src/api/index.ts` 只能写导出，例如：

```ts
export * from './user';
export * from './order';
```

## 违规示例

以下写法都视为违规：

```ts
// ❌ 一个文件混写多个业务域
// src/api/index.ts
export async function getUserList() {}
export async function createOrder() {}
export async function getDashboardData() {}

// ❌ 在页面里直接请求
// src/views/UserView.vue
await request.get('/user/list');

// ❌ 在 store 里直接请求
// src/stores/userStore.js
await axios.get('/user/list');
```

## 正确示例

```ts
// ✅ src/api/user.ts
export async function getUserList() {}
export async function getUserDetail() {}

// ✅ src/api/order.ts
export async function createOrder() {}
export async function getOrderDetail() {}

// ✅ src/api/index.ts
export * from './user';
export * from './order';
```

## 输出前自检

- 本次新增接口是否按页面或业务域落到了独立文件？
- `index.ts` 是否只做导出，没有写请求实现？
- 是否把多个无关模块接口继续堆进了同一个文件？
- 页面、组件、store 中是否出现了直接请求？
- 如果发现历史大文件，本次是否至少没有继续往里面追加新的无关接口？
