---
tags:
  - vue
  - performance
created: 2026-05-31
---

# 性能优化清单

## 速查：编译时优化（自动生效）

Vue 3 编译器自动处理，无需手动优化：
- ✅ 静态提升 (HoistStatic)
- ✅ Patch Flags
- ✅ Block Tree
- ✅ 事件缓存
- ✅ Tree-shaking

## 运行时优化

### 1. 组件懒加载

```js
// 路由级
const routes = [
  { path: '/dashboard', component: () => import('@/views/Dashboard.vue') }
]

// 组件级
const HeavyChart = defineAsyncComponent(() => import('@/components/HeavyChart.vue'))
```

### 2. v-once / v-memo

```vue
<!-- 只渲染一次的静态内容 -->
<h1 v-once>{{ title }}</h1>

<!-- 条件缓存（复杂列表项） -->
<div v-for="item in list" :key="item.id" v-memo="[item.selected, item.id === activeId]">
  <ExpensiveComponent :item="item" />
</div>
```

### 3. 大列表优化

```vue
<!-- 虚拟滚动 — 只渲染可视区域 -->
<!-- 使用 vue-virtual-scroller -->
<RecycleScroller :items="largeList" :item-size="50" key-field="id">
  <template #default="{ item }">
    <ListItem :item="item" />
  </template>
</RecycleScroller>
```

### 4. 避免不必要的组件更新

```vue
<script setup>
// shallowRef — 只追踪 .value 变化，不深度代理
const largeData = shallowRef(hugeObject)

// shallowReactive — 只代理第一层
const state = shallowReactive({ items: [] })
</script>
```

### 5. computed 缓存 vs method 重复执行

```vue
<!-- ✓ 有缓存，依赖不变不重算 -->
{{ expensiveComputed }}

<!-- ✗ 每次渲染都执行 -->
{{ expensiveMethod() }}
```

### 6. 长列表 key 选择

```vue
<!-- ✓ 稳定的唯一 id -->
<div v-for="item in list" :key="item.id">

<!-- ✗ index 不稳定 -->
<div v-for="(item, i) in list" :key="i">
```

### 7. KeepAlive 缓存

```vue
<!-- 缓存切换频繁的组件 -->
<KeepAlive :max="10">
  <component :is="currentTab" />
</KeepAlive>
```

### 8. 防抖 / 节流

```js
import { useDebounceFn, useThrottleFn } from '@vueuse/core'

const debouncedSearch = useDebounceFn(async (query) => {
  results.value = await api.search(query)
}, 300)
```

## 打包优化

```js
// vite.config.js
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router', 'pinia'],
          chart: ['echarts']
        }
      }
    },
    chunkSizeWarningLimit: 500 // KB
  }
})
```

## 性能诊断

```vue
<script setup>
import { onRenderTracked, onRenderTriggered } from 'vue'

// 调试：追踪哪些依赖触发了渲染
onRenderTriggered((event) => {
  console.log('triggered by:', event)
})
</script>
```

| 工具 | 用途 |
|------|------|
| Vue Devtools | 组件树、状态、性能火焰图 |
| Chrome Performance | 运行时性能分析 |
| `rollup-plugin-visualizer` | 打包分析 |
| Lighthouse | 综合性能评分 |

## 深入：优化优先级

```
1. 减少包体积（代码分割、tree-shaking）
2. 减少渲染次数（v-once、shallowRef、computed）
3. 减少 DOM 操作（虚拟滚动、v-memo）
4. 优化数据结构（避免深层 reactive 大对象）
5. 网络优化（懒加载、预取、CDN）
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Compiler-Optimizations|编译器优化]]
- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Virtual-DOM|虚拟 DOM]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Async-Components|异步组件]]
