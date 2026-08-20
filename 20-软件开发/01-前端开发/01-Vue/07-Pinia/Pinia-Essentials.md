---
tags:
  - vue
  - pinia
  - state-management
created: 2026-05-31
---

# Pinia 状态管理

## 速查

### 定义 Store

```js
// stores/counter.js — Setup Store（推荐）
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  // state
  const count = ref(0)
  const name = ref('Vue')

  // getters
  const doubleCount = computed(() => count.value * 2)

  // actions
  function increment() {
    count.value++
  }

  async function fetchData() {
    const data = await api.getData()
    name.value = data.name
  }

  return { count, name, doubleCount, increment, fetchData }
})
```

```js
// Options Store 写法
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0, name: 'Vue' }),
  getters: {
    doubleCount: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    }
  }
})
```

### 使用 Store

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'
import { storeToRefs } from 'pinia'

const store = useCounterStore()

// 解构时保持响应性
const { count, doubleCount } = storeToRefs(store)
// actions 直接解构
const { increment } = store

// 也可以直接访问
store.count++
store.increment()
</script>

<template>
  <p>{{ count }} × 2 = {{ doubleCount }}</p>
  <button @click="increment">+1</button>
</template>
```

### Store 重置

```js
store.$reset() // 仅 Options Store
```

### 批量更新

```js
store.$patch({ count: 10, name: 'Pinia' })
store.$patch((state) => {
  state.count++
  state.name = 'Updated'
})
```

## 深入：Setup Store vs Options Store

| | Setup Store | Options Store |
|---|------------|---------------|
| 写法 | Composition API | Options API |
| 类型推导 | 天然支持 | 需要额外声明 |
| 灵活性 | 完全自由 | 受选项结构限制 |
| `$reset` | 不支持 | 支持 |
| 代码组织 | 按功能聚合 | 按选项分类 |

> 推荐使用 Setup Store — 与 `<script setup>` 风格统一，composable 可直接复用。

## 深入：Pinia vs Vuex

| 特性 | Pinia | Vuex 4 |
|------|-------|--------|
| mutations | 无（直接修改 state） | 有 |
| modules | 无需嵌套（扁平化） | 嵌套模块 |
| TypeScript | 原生支持 | 需要额外配置 |
| 体积 | ~1KB | ~10KB |
| devtools | 支持 | 支持 |
| SSR | 支持 | 支持 |

## 相关

- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Advanced|Pinia 进阶]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables]]
