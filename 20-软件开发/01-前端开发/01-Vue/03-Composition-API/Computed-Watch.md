---
tags:
  - vue
  - composition-api
  - computed
  - watch
created: 2026-05-31
---

# computed / watch / watchEffect

## 速查

```vue
<script setup>
import { ref, computed, watch, watchEffect } from 'vue'

// computed — 计算属性
const count = ref(0)
const doubled = computed(() => count.value * 2)

// computed 可写
const fullName = computed({
  get: () => first.value + last.value,
  set: (v) => { first.value = v[0]; last.value = v.slice(1) }
})

// watch — 侦听器
watch(count, (newVal, oldVal) => {
  console.log(`${oldVal} → ${newVal}`)
}, { immediate: true, deep: false, once: false, flush: 'pre' })

// watch getter
watch(
  () => state.items.length,
  (newLen) => { /* ... */ }
)

// watch 多源
watch([count, () => state.name], ([c, n], [oc, on]) => {
  // ...
})

// watchEffect — 自动追踪
watchEffect(() => {
  // 内部用到的响应式值变化时自动重新执行
  api.fetch(count.value, state.name)
})
</script>
```

## 计算属性 vs 方法 vs watch

| | computed | method | watch | watchEffect |
|---|---------|--------|-------|-------------|
| 返回值 | 有 | 有 | 无 | 无 |
| 缓存 | 是 | 否 | — | — |
| 副作用 | 不应有 | 可以 | 是 | 是 |
| 依赖声明 | 自动 | — | 手动 | 自动 |
| 异步 | 不支持 | 支持 | 支持 | 支持 |

## 实战模式

### 模式一：搜索防抖

```js
const search = ref('')
const results = ref([])

watch(search, async (query) => {
  if (!query) { results.value = []; return }
  results.value = await api.search(query)
}, { flush: 'post' })
```

### 模式二：数据持久化

```js
const settings = useStorage('settings', { theme: 'dark' })

watch(settings, (val) => {
  localStorage.setItem('settings', JSON.stringify(val))
}, { deep: true })
```

### 模式三：调试副作用触发

```js
onRenderTriggered((event) => {
  console.log('触发渲染的依赖:', event)
})
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Computed-and-Watch|基础用法]]
- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Effect-Scheduling|副作用调度]]
