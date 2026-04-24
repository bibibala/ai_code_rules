---
name: ai-no-loop-api-calls
description: 禁止通过循环调用 API 获取数据，如有需要必须由后端提供接口
alwaysApply: true
priority: 2
---

# API 调用规范

## 适用范围

- 仅适用于前端发起 API 请求的场景（页面、组件、store、前端 BFF）。
- 不适用于后端服务之间调用、任务调度、数据同步等服务端链路。
- 前后端一期开发时，只约束前端代码实现方式，不限制后端内部实现策略。

## 核心规则

- 禁止为了“拼接分页 / 补齐全量 / 遍历列表明细”而通过循环调用同一类 API 获取数据。
- 禁止使用顺序循环、递归、批量并发等方式伪装成“不是循环”的补齐请求。
- 如现有接口无法满足需求，必须由后端新增接口或提供批量接口，一次性返回所需数据。

## 允许行为

- 允许在页面初始化时并行请求少量互不相关的接口，例如 2 到 3 个独立资源。
- 允许使用后端提供的批量接口，例如 `/xxx/batch`、`/xxx/aggregate`。
- 允许并行请求不同资源，但前提不是为了补齐同一资源的分页或详情。

## 违规示例

```ts
// ❌ while 循环请求
while (hasNext) {
  await request.get('/user/page', { page });
}

// ❌ for 循环请求
for (let i = 1; i <= totalPage; i++) {
  await request.get('/user/page', { page: i });
}

// ❌ 递归请求
const fetchAll = async (page = 1) => {
  const res = await request.get('/user/page', { page });
  if (res.hasNext) {
    await fetchAll(page + 1);
  }
};

// ❌ Promise.all 批量请求分页
await Promise.all(pages.map(page => request.get('/user/page', { page })));
```

## 正确示例

```ts
// ✅ 由后端直接提供批量接口
await request.post('/user/batch-detail', { ids });

// ✅ 页面初始化时并行获取互不相关资源
const [userRes, dictRes, configRes] = await Promise.all([
  requestUser(),
  requestDict(),
  requestConfig(),
]);
```

## 输出前自检

- 是否为了补齐分页、遍历列表、补全详情而写了循环请求？
- 是否用递归、`Promise.all`、`map + request` 伪装成“不是循环”的批量拉取？
- 当前需求是否应该改为让后端提供批量或聚合接口？
- 当前并行请求是否真的属于互不相关资源，而不是同一资源的拆分拉取？
