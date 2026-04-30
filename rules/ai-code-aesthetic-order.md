---
name: ai-code-aesthetic-order
description: 前端 AI 代码审美与结构秩序规则，约束代码密度、分层、函数重量、对称性与判断方向
alwaysApply: true
priority: 2
---

# 前端代码审美与结构秩序规则

## 适用范围

- 本规则仅适用于前端代码（Vue / React / Web / Electron / H5 / 小程序前端 / 前端 Node 脚本）。
- 本规则用于约束 AI 生成代码的结构质感、阅读节奏和一致性。
- 不适用于后端服务、数据库脚本、中间件、运维脚本或非前端研发任务。
- 如果当前任务是前后端混合开发，本规则仅作用于前端目录和前端改动。

## 强制要求

### 代码块必须按抽象层级组织

- 同一文件内必须按抽象层级分组，不允许静态配置、响应式状态、派生状态、工具函数、事件处理函数、生命周期逻辑随意穿插。
- Vue `<script setup>` 内部必须按以下顺序组织：

  ```text
  imports
  props / emits
  静态常量与静态配置
  ref / reactive 状态
  computed 派生状态
  watch / watchEffect 副作用
  工具函数
  事件处理函数
  生命周期
  ```

- React 组件内部必须按以下顺序组织：

  ```text
  props 解构
  静态派生常量
  state / ref
  memo / derived values
  effects
  callbacks / event handlers
  render helpers
  return JSX
  ```

- 块与块之间必须用一个空行分隔；同一块内部不要用无意义空行切碎。

### 代码密度必须均匀

- 相邻函数、computed、handler 的代码重量必须接近，不允许一个函数承担完整业务链路，旁边函数只做一行转发。
- 三行以内能清楚表达的逻辑禁止展开成十行以上的仪式化代码。
- 复杂逻辑必须拆分为同层级的小函数，但禁止把简单逻辑拆成过多跳转。
- 同一文件内的命名、空行、函数体长度和参数排列必须保持稳定节奏。

### 单个函数必须有长度上限

- 单个业务函数原则上不得超过 15 行。
- 超过 15 行时，必须按真实职责拆成子函数，例如校验、构建参数、提交请求、处理成功结果。
- 禁止一个 `handleXxx` 同时完成校验、确认弹窗、组装请求、发请求、处理响应、刷新列表等完整链路。
- 如果函数超过 15 行但无法拆分，必须有明确原因，例如第三方 API 要求的连续配置对象。

### 同类代码必须保持对称

- 同类函数的参数签名必须保持一致，例如 `handleFileChange(file)` 与 `handleFileRemove(file)` 不得一个接收参数、另一个无参数，除非确实不需要上下文。
- 成对语义必须成对出现并命名稳定，例如 `open/close`、`show/hide`、`start/stop`、`init/destroy`。
- 同一类 handler 的命名、参数顺序、返回行为必须一致。
- 同一类配置对象的字段顺序必须一致，禁止同一语义在不同对象中随意换序。

### 条件判断方向必须统一

- 同一文件内必须统一使用一种主流程表达方式，不允许正向判断和防御性返回混用。
- 如果项目规则要求 `if (code === 200) { ... }` 包裹成功分支，则当前文件内所有请求结果处理都必须保持该方向。
- 如果当前文件已有清晰的卫语句风格，则新增逻辑必须沿用已有方向，不得混入相反写法。
- 类型判断必须基于项目约定的真实类型，不允许同一字段同时出现数字比较、字符串比较和运行时转换比较。

### 派生状态必须合并成清晰链路

- 多个 computed / memo 如果表达同一条数据链路，必须合并或改名为有层次的派生链路。
- 禁止为了凑步骤把一个简单派生过程拆成多个含义重叠的 computed。
- 禁止用监听数组长度、拼接字段等方式硬凑依赖；应重新审视数据来源、派生状态或副作用触发时机。

### 重复结构必须收敛

- 重复出现的表单默认值、查询参数默认值、配置对象默认值，必须抽成 `getDefaultXxx()` 或稳定常量。
- 禁止在 `resetForm`、`handleEdit`、`handleCreate` 等函数中重复手写同一组字段。
- 相同逻辑不得出现两次；必须抽成函数、computed、memo 或配置映射。

### 异步风格必须一致

- 同一文件内异步逻辑必须统一使用 `async/await` 或项目已有封装风格。
- 禁止在大多数逻辑使用 `async/await` 时，局部切换到 callback、`.then()` 或混合链式写法。
- 第三方 API 只提供 callback 时，必须用一个小函数封装后再进入主业务流程。

## 违规示例

```vue
<script setup>
const treeProps = { label: 'name' }
const menuForm = reactive({ id: '', name: '', menuType: 0 })

function getMenuTypeName(type) {
  return String(type) === '0' ? '目录' : '菜单'
}

const selectedId = ref('')
const tableChildren = computed(() => visibleChildren.value)
const visibleChildren = computed(() => menuList.value.filter(item => item.visible))

async function handleImportSyncJson(file) {
  if (!file) return
  await ElMessageBox.confirm('确认导入？')
  const formData = new FormData()
  formData.append('file', file.raw)
  const { code } = await importMenu(formData)
  if (code === 200) {
    ElMessage.success('导入成功')
    await fetchMenuList()
  }
}

function handleSyncFileRemove() {
  syncFile.value = null
}
</script>
```

## 正确示例

```vue
<script setup>
const treeProps = { label: 'name' }

const selectedId = ref('')
const syncFile = ref(null)
const menuForm = reactive(getDefaultMenuForm())

const visibleMenuList = computed(() => {
  return menuList.value.filter(item => item.visible)
})

function getDefaultMenuForm() {
  return {
    id: '',
    name: '',
    menuType: 0,
  }
}

function getMenuTypeName(type) {
  return type === 0 ? '目录' : '菜单'
}

function buildImportFormData(file) {
  const formData = new FormData()
  formData.append('file', file.raw)
  return formData
}

async function importSyncFile(file) {
  const { code } = await importMenu(buildImportFormData(file))
  if (code === 200) {
    ElMessage.success('导入成功')
    await fetchMenuList()
  }
}

async function handleSyncFileChange(file) {
  syncFile.value = file
  await ElMessageBox.confirm('确认导入？')
  await importSyncFile(file)
}

function handleSyncFileRemove(file) {
  if (syncFile.value?.uid === file.uid) {
    syncFile.value = null
  }
}
</script>
```

## 输出前自检

- 静态配置、状态、computed、watch、工具函数、handler、生命周期是否已经按层级分组？
- 相邻函数的长度和重量是否均匀，是否存在一个函数吃完整条业务链路？
- 是否有超过 15 行的业务函数，是否已经按职责拆分？
- 同类 handler 的参数、命名、返回行为是否对称？
- 条件判断方向是否统一，是否混用了正向分支和反向卫语句？
- 同一字段的类型判断是否统一，是否出现数字、字符串、运行时转换混用？
- computed / memo 是否存在含义重叠或硬凑依赖？
- 表单默认值、查询参数、配置对象是否存在重复手写？
- 异步风格是否统一，是否混入 callback 或 `.then()`？
