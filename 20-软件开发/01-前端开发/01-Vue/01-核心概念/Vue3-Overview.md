---
tags:
  - vue
  - overview
created: 2026-05-31
---

# Vue 3 总览

## 速查

| 特性 | 说明 |
|------|------|
| 响应式系统 | 基于 `Proxy`，替代 Vue 2 的 `Object.defineProperty` |
| API 风格 | Composition API（推荐） + Options API（兼容） |
| 性能 | 编译时优化（静态提升、Patch Flags、Block Tree） |
| Tree-shaking | 按需引入，未用 API 不打包 |
| TypeScript | 原生 TS 编写，一等公民支持 |
| 新增组件 | `<Teleport>` `<Suspense>` `<Transition>` 增强 |
| 片段 | 组件支持多根节点（Fragment） |

## Composition API vs Options API

```vue
<!-- Composition API (推荐) -->
<script setup>
import { ref, computed } from 'vue'

const count = ref(0)
const doubled = computed(() => count.value * 2)
const increment = () => count.value++
</script>

<!-- Options API -->
<script>
export default {
  data: () => ({ count: 0 }),
  computed: {
    doubled() { return this.count * 2 }
  },
  methods: {
    increment() { this.count++ }
  }
}
</script>
```

### 何时选哪种

| 场景 | 推荐 |
|------|------|
| 新项目 | Composition API + `<script setup>` |
| 逻辑复用（composables） | Composition API |
| 简单组件 / 快速原型 | Options API 也行，无性能差异 |
| 维护旧项目 | 保持现有风格，逐步迁移 |

## 应用实例创建

```js
// main.js
import { createApp } from 'vue'
import App from './App.vue'

const app = createApp(App)
app.use(router)
app.use(pinia)
app.mount('#app')
```

> `createApp` 每个实例独立，不会污染全局。Vue 2 的 `Vue.prototype` 全局修改已成为历史。

## 深入：为什么从 Options API 转向 Composition API

**Options API 的痛点：**
1. **逻辑碎片化** — 同一功能的 data/methods/computed/watch 分散在不同选项中
2. **代码组织** — 按选项类型而非功能组织，大组件难以维护
3. **逻辑复用** — mixins 有命名冲突、来源不清晰的问题

**Composition API 的解法：**
1. **逻辑聚合** — 一个功能的所有逻辑（状态 + 方法 + 计算 + 侦听）集中在一个函数里
2. **Composables** — 取代 mixins，返回值有明确的类型和来源
3. **更好的 TS 推导** — 不依赖 `this` 上下文，类型推导自然

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Setup|Setup]]
- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Reactivity-In-Depth|响应式原理]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Template-Syntax|模板语法]]
