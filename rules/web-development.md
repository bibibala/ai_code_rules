---
name: web-development
alwaysApply: false
priority: 1
description: Vue 3 + JavaScript web project rules for Trae AI
---

# Vue 3 Web Project Rules

## Scope

- This rule set is frontend-only and applies to Vue 3 web projects.
- It does not constrain backend service implementation, database design, or server runtime standards.
- In full-stack delivery, apply this file only to frontend directories and frontend code changes.

## Tech Stack

- **Framework**: Vue 3 (Composition API)
- **Language**: JavaScript (no TypeScript)
- **Styling**: Plain CSS (scoped per component)
- **Build Tool**: Vite

## Project Structure

```text
src/
├── assets/
│ └── styles/
├── components/
│ └── common/
├── views/
├── router/
│ └── index.js
├── stores/
├── composables/
├── api/
├── utils/
└── App.vue
```

- Place page components in `views/`, not in `components/`.
- Place reusable logic in `composables/` as `useXxx.js` files.
- Never place business logic directly inside `.vue` templates.

## Vue 3 Conventions

### Composition API

- Always use `<script setup>` syntax.
- Use `ref()` for primitives and `reactive()` for objects.
- Use `computed()` for derived state, never compute values in the template.
- Use `watch()` / `watchEffect()` for side effects and clean up when needed.
- Prefer `defineProps` and `defineEmits` with explicit definitions.

```vue
<script setup>
const props = defineProps({
  title: { type: String, required: true },
  count: { type: Number, default: 0 },
});

const emit = defineEmits(['update', 'close']);
</script>
```

### Component Design

- One component per file.
- Keep components small and focused on a single responsibility.
- Extract repeated UI patterns into `components/common/`.
- Pass data down via props and communicate events up via emits.
- Do not mutate props directly.

### Template Rules

- Use `v-if` / `v-else` for conditional rendering.
- Prefer `v-show` only when toggling frequently.
- Always provide a `:key` when using `v-for`.
- Avoid combining `v-if` and `v-for` on the same element.
- Use `:prop` and `@event` shorthands.

## Naming Conventions

| Type                  | Convention                  | Example           |
| --------------------- | --------------------------- | ----------------- |
| Component files       | PascalCase                  | `UserCard.vue`    |
| Composable files      | camelCase with `use` prefix | `useAuth.js`      |
| Store files           | camelCase                   | `userStore.js`    |
| CSS class names       | kebab-case                  | `.user-card`      |
| Variables / functions | camelCase                   | `getUserList`     |
| Constants             | UPPER_SNAKE_CASE            | `MAX_RETRY_COUNT` |
| Emitted events        | kebab-case                  | `update-value`    |

## State Management (Pinia)

- Use Pinia for all shared or global state.
- Define one store per domain.
- Use the setup store syntax.

```js
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useUserStore = defineStore('user', () => {
  const user = ref(null);
  const isLoggedIn = computed(() => !!user.value);

  function setUser(data) {
    user.value = data;
  }

  return { user, isLoggedIn, setUser };
});
```

## API

- Centralize all HTTP calls in `src/api/`.
- 新增接口前，先确定本次会新增或修改哪些 `src/api/*` 文件，再开始实现。
- 对外暴露的 API 方法必须返回“可判定结果”，禁止通过 `throw` 把异常流转到业务层。
- `try/catch` 仅允许在请求封装或基础设施层用于错误归一化。
- Never call `fetch`, `axios`, or `request.xxx(...)` directly from a component or store.
- API 文件组织必须遵守 `api-file-split.md`。

```js
export async function fetchUser(id) {
  const res = await request.get(`/api/users/${id}`);
  if (!res.ok) return res;
  return res;
}
```

## CSS Rules

- Use scoped CSS in every component.
- Use CSS custom properties for colors, spacing, and typography.
- Follow BEM-inspired naming where appropriate.
- Do not use inline styles except for truly dynamic values.
- Do not use `!important`.

## Routing (Vue Router)

- Define all routes in `src/router/index.js`.
- Use lazy loading for page-level view components.
- Use named routes.
- Protect authenticated routes with navigation guards when needed.

```js
const routes = [
  {
    path: '/dashboard',
    name: 'Dashboard',
    component: () => import('@/views/DashboardView.vue'),
    meta: { requiresAuth: true },
  },
];
```

## Error Handling

- 不通过异常做业务流程控制；所有异步调用必须被显式处理，例如判断 `ok`。
- UI 层只展示对用户友好的错误信息。
- 技术细节通过项目内统一的 `logger` 处理。
- Use a composable like `useErrorHandler` when the project has that pattern.

## Code Quality

- Functions should do one thing.
- Avoid deep nesting and prefer early returns.
- Remove `console.log` statements before committing.
- Prefer `const` over `let`, and never use `var`.
- Do not add comments unless they explain non-obvious logic.

## Do Not

- Do not use the Options API.
- Do not import using relative paths like `../../`; use the `@/` alias.
- Do not put API logic inside Vue components.
- Do not commit commented-out code blocks.

## Output Checklist

- Are new page-level components placed in `views/` rather than `components/`?
- Are reusable logic and side-effect helpers extracted into `composables/` when appropriate?
- Are all HTTP requests placed in `src/api/`, and split by page or module instead of piling into one file?
- Did you avoid direct `fetch / axios / request` calls in views and stores?
- Are state changes, error handling, and rendering flow explicit and easy to trace?
