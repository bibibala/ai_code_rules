---
name: ai-select-use-list-api
description: 下拉框等选项类组件必须使用 list 接口，禁止分页接口
alwaysApply: true
priority: 2
---

# 下拉框数据获取规范

## 适用范围

以下场景必须遵守本规则：

- Select（下拉框）
- Dropdown（下拉菜单）
- Cascader（级联选择）
- Radio / Checkbox 选项列表
- 任意“可选项列表”组件
- 本规则仅约束前端“选项加载方式”，不约束后端内部存储与查询实现。

## 核心规则

- 所有选项数据必须通过 list 接口或专用 options 接口获取。
- 禁止使用任何分页接口（`page / size / cursor` 等）为选项组件提供数据。
- 禁止在前端为下拉、单选、多选、级联组件循环拼接分页数据。

## 强制要求

- 必须优先使用 `/xxx/list` 或 `/xxx/options` 类型接口。
- 接口应直接返回完整选项数据，或由后端根据关键字过滤后返回结果。
- 选项组件拿到的数据应能直接渲染，不需要前端额外分页补齐。

## 数据量过大时的处理

- 当选项数据量无法一次性返回时，必须由后端提供“专用选项接口”，例如 `/xxx/options` 或 `/xxx/list` + `keyword`。
- 由后端完成过滤、搜索、裁剪。
- 仍然禁止在前端通过分页循环把数据拼接成全量选项，包括 `while / for / 递归 / Promise.all`。

## 禁止行为

```ts
// ❌ 使用分页接口
request.get('/user/page', { page: 1, size: 10 });

// ❌ 在 list 接口中传递分页参数
request.get('/user/list', { page: 1, size: 100 });

// ❌ 循环分页拼接
while (hasNext) {
  await request.get('/user/page', { page });
}
```

## 正确示例

```ts
// ✅ 直接使用 list 接口
request.get('/user/list');

// ✅ 由后端处理关键字过滤
request.get('/user/options', { keyword });
```

## 输出前自检

- 当前数据是否用于 Select、Dropdown、Cascader、Radio、Checkbox 或其他选项组件？
- 是否误用了分页接口来喂选项组件？
- 是否在 list/options 接口中偷偷传了分页参数？
- 是否为了拿全量选项写了循环、递归、并发拼接？
- 如果数据量过大，是否改为后端提供过滤后的专用 options 接口？
