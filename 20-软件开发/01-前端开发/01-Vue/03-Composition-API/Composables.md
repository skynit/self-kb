---
tags:
  - vue
  - composition-api
  - composables
created: 2026-05-31
---

# Composables 模式

## 速查

### 基本模式

```js
// composables/useCounter.js
import { ref, computed } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const doubled = computed(() => count.value * 2)
  const increment = () => count.value++
  const decrement = () => count.value--
  const reset = () => { count.value = initial }

  return { count, doubled, increment, decrement, reset }
}
```

```vue
<script setup>
import { useCounter } from '@/composables/useCounter'

const { count, doubled, increment } = useCounter(10)
</script>

<template>
  <button @click="increment">{{ count }} ({{ doubled }})</button>
</template>
```

### 带生命周期的 Composable

```js
export function useEventListener(target, event, callback) {
  onMounted(() => target.addEventListener(event, callback))
  onUnmounted(() => target.removeEventListener(event, callback))
}
```

### 带副作用清理的 Composable

```js
export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(e) {
    x.value = e.clientX
    y.value = e.clientY
  }

  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  return { x, y }
}
```

## 常见 Composables

| 名称 | 功能 |
|------|------|
| `useFetch` | 封装请求、loading、error 状态 |
| `useMouse` | 鼠标位置 |
| `useLocalStorage` | 本地存储同步 |
| `useDebounce` | 防抖值 |
| `useMediaQuery` | 媒体查询匹配 |
| `useIntersectionObserver` | 懒加载 |

## 深入：Composables 设计原则

### 1. 命名以 `use` 开头

```js
// ✓ 正确
function useCounter() {}
function useFetch() {}

// ✗ 错误
function counter() {}
function fetchData() {}
```

### 2. 返回 ref 而非 reactive

```js
// ✓ 返回 ref — 解构安全
return { x, y, count }

// ✗ 返回 reactive — 解构丢失响应性
return reactive({ x, y, count })

// ✓ 或使用 toRefs
return toRefs(reactive({ x, y, count }))
```

### 3. 接受 ref 或 getter 作为参数

```js
// 灵活的参数接受
function useFetch(url) {
  // url 可以是 ref、getter、或普通字符串
  watchEffect(() => {
    const resolvedUrl = toValue(url) // Vue 3.3+ 统一处理
    fetch(resolvedUrl)
  })
}

// 使用
useFetch('/api/data')           // 字符串
useFetch(computed(() => ...))   // computed
useFetch(ref('/api/data'))      // ref
```

### 4. 可选的副作用注册选项

```js
function useMouse() {
  // ...
  // 显式传入 scope，便于控制生命周期
  if (getCurrentScope()) {
    onScopeDispose(() => { /* cleanup */ })
  }
}
```

## 深入：Composables vs Mixins vs Hooks(React)

| 特性 | Composables | Mixins | React Hooks |
|------|-------------|--------|-------------|
| 命名冲突 | 不会 | 会 | 不会 |
| 来源追踪 | 清晰 | 不清晰 | 清晰 |
| 类型推导 | 完整 | 差 | 完整 |
| 调用限制 | 必须在 setup 中 | 无 | 必须在组件顶层 |
| 多实例隔离 | 独立 | 独立 | 独立 |
| 复用逻辑 | 函数组合 | 合并到组件选项 | 函数组合 |

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Ref-and-Reactive|ref 与 reactive]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Lifecycle-Hooks|生命周期钩子]]
