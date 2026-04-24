---
name: ai-code-quality
description: Frontend-only AI code quality rules (Web / Electron / Vue / TS / javascript)
alwaysApply: true
priority: 2
---

# 前端规范（Web / Electron / Vue / TS /javascript）

## 适用范围

- 本规则仅适用于前端代码（Web / Electron / H5 / 小程序前端 / Node 侧前端工程脚本）。
- 不适用于后端代码（Java / Go / Python / Node 服务端 / 数据库脚本 / 中间件配置）。
- 当前任务为前后端一期开发时，仅在前端目录与前端改动中启用本规则，后端按后端规范执行。

## 强制要求

### 禁止 Console

- 禁止在项目代码中出现任何 `console.*`（包含 `console.log / warn / error / info / debug`）。
- 禁止提交被注释掉的 `// console...`。
- 如需日志能力，必须使用项目内统一的 `logger` 封装，且默认不在生产环境输出。

### 错误处理（Try/Catch 使用边界）

- 业务层、组件层、store 层不允许使用 `try/catch`。
- 允许在基础设施层使用 `try/catch` 做错误归一化，例如请求封装、SDK 适配层。
- 基础设施层捕获异常后，必须转换为“可显式判断”的统一返回结构，不得把异常继续抛给业务层作为流程控制。

### Promise / 异步规范

- 业务代码禁止使用 `.then(...)` 与 `.catch(...)`，统一使用 `async/await`。
- 仅允许在少数封装或适配场景使用 `.then/.catch`，且封装对外仍保持 `async/await` 的调用方式。

### 请求处理规范

- API 调用失败不通过异常流转。
- 请求层必须统一返回“可判定结果”，禁止返回 `null`、`undefined` 这类弱语义值。
- 所有 API 请求函数 必须返回统一结构对象：
```
{
code: number,
data: any,
message: string
}
```
- 语义约定：
- code === 200 → 表示成功
- code !== 200 → 表示失败

```js
const { code, data, message } = await getData()
if (code === 200) {
    useData(data)
}
```

## 违规示例

```ts
// ❌ 业务层写 try/catch
try {
  const res = await getUserList()
} catch (error) {
  console.error(error)
}

// ❌ 业务层使用 then/catch
getUserList()
  .then(res => setList(res))
  .catch(() => {})

// ❌ 返回弱语义值
export async function getUserList() {
  return null
}
```

## 正确示例

```ts
// ✅ 基础设施层做错误归一化
export async function requestUserList() {
  try {
    const data = await request.get('/user/list')
    return { code: 200, data, message: 'success' }
  } catch (error) {
    return { code: -1, data: null, message: normalizeError(error) }
  }
}

// ✅ 业务层显式判断结果
const { code, data, message } = await requestUserList()
if (code !== 200) return

renderList(data)
```

## 输出前自检

- 是否残留了任何 `console.*` 或注释掉的 `console`？
- 业务层、组件层、store 层是否写了 `try/catch`？
- 业务代码是否还在使用 `.then/.catch`？
- 请求方法是否返回了明确的可判定结果，而不是 `null/undefined`？
- 调用方是否显式判断了 `code === 200` 再使用 `data`？
