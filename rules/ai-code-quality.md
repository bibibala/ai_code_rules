---
name: ai-code-quality
description: Frontend-only AI code quality rules for Web / Electron / H5 / mini-program frontend / frontend Node scripts
alwaysApply: true
priority: 2
---

# 前端代码质量规范（仅前端适用）

## 适用范围

- 本规则仅适用于前端代码（Web / Electron / H5 / 小程序前端 / Node 侧前端工程脚本）。
- 不适用于后端代码（Java / Go / Python / Node 服务端 / 数据库脚本 / 中间件配置）。
- 当前任务为前后端一期开发时，仅在前端目录与前端改动中启用本规则，后端按后端规范执行。
- 如果任务同时包含前端与后端内容，本规则不能跨目录套用到后端实现、后端接口封装或数据库脚本。

## 强制要求

### 简洁优先，禁止过度防御

- 代码必须保持短、直、清楚，禁止为低概率问题堆叠多层保护性写法。
- 禁止出现重复判空、重复 fallback、重复 normalize、重复包装返回值的代码。
- 没有真实复用价值时，不要为了"更安全"额外封装一层函数、hook、service、adapter。
- 组件、store、api 的返回值和数据流必须足够直接，避免来回包裹和拆包。

### 禁止 Console

- 禁止在项目代码中出现任何 `console.*`（包含 `console.log / warn / error / info / debug`）。
- 禁止提交被注释掉的 `// console...`。
- 如需日志能力，必须使用项目内统一的 `logger` 封装，且默认不在生产环境输出。

### 错误处理（Try/Catch 使用边界）

- 业务层、组件层、store 层不允许使用 `try/catch`。
- 允许在基础设施层使用 `try/catch` 做错误归一化，例如请求封装、SDK 适配层。
- 基础设施层捕获异常后，必须转换为"可显式判断"的统一返回结构，不得把异常继续抛给业务层作为流程控制。

### Promise / 异步规范

- 业务代码禁止使用 `.then(...)` 与 `.catch(...)`，统一使用 `async/await`。
- 仅允许在少数封装或适配场景使用 `.then/.catch`，且封装对外仍保持 `async/await` 的调用方式。

### 命名规范

- 变量、函数、状态命名必须简洁清晰，避免过长复合命名。
- 命名长度必须以"扫一眼能理解"为准，不要把实现细节、时序、容错策略全部塞进名字里。
- 同一语义优先沿用项目已有短命名，避免为了显得严谨而重新发明一套超长术语。

### 请求处理规范

- API 调用失败不通过异常流转。
- 请求层必须统一返回"可判定结果"，禁止返回 `null`、`undefined` 这类弱语义值。
- 所有 API 请求函数必须返回统一结构对象：

  ```
  {
    code: number,
    data: any,
    message: string
  }
  ```

- 语义约定：
  - `code === 200` → 成功，在此分支内执行业务逻辑
  - `code !== 200` → 失败，不进入业务分支，axios 拦截层已统一处理错误提示

- 业务层统一使用 `if (code === 200)` 包裹业务逻辑，**禁止**用 `if (code !== 200) return` 的提前返回风格：

  ```js
  const { code, data } = await getData()
  if (code === 200) {
    useData(data)
  }
  ```

  > 原因：axios 拦截器已在请求层统一处理失败情况（toast 提示、日志上报等），业务层无需对失败路径做任何额外处理，`if (code === 200)` 即为完整写法。

## 违规示例

```ts
// ❌ 过度防御 + 重复包装
function buildSafeListData(response) {
  const safeData = response?.data ?? []
  const normalizedData = Array.isArray(safeData) ? safeData : []
  return { list: normalizedData }
}

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

// ❌ 命名过长
const shouldShowUserListEmptyStateWhenRequestFinished = false

// ❌ 用 !== 200 提前 return（禁止，失败处理已在拦截器完成）
const { code, data } = await getUserList()
if (code !== 200) return
renderList(data)
```

## 正确示例

```ts
// ✅ 保持直接
function getList(response) {
  return Array.isArray(response?.data) ? response.data : []
}

// ✅ 业务层用 if (code === 200) 包裹，失败分支由拦截器处理，此处无需关心
const { code, data } = await requestUserList()
if (code === 200) {
  renderList(data)
}

// ✅ 简洁命名
const showEmpty = false
```

## 输出前自检

- 是否写出了"层层保护、层层包装、层层转换"的臃肿代码？
- 是否存在没有明确收益的 adapter / wrapper / normalize / safeXxx 一类封装？
- 是否残留了任何 `console.*` 或注释掉的 `console`？
- 业务层、组件层、store 层是否写了 `try/catch`？
- 业务代码是否还在使用 `.then/.catch`？
- 命名是否简短直观，而不是长到需要再解释一遍？
- 请求方法是否返回了明确的可判定结果，而不是 `null/undefined`？
- 业务层是否用 `if (code === 200)` 包裹业务逻辑，而非用 `!== 200` 提前 return？
