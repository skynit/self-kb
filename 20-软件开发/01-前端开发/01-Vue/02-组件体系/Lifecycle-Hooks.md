---
tags:
  - vue
  - lifecycle
created: 2026-05-31
---

# 生命周期钩子

## 速查

```
创建阶段
  └── setup() ← 在所有钩子之前执行
        ↓
      onBeforeMount → 挂载前
        ↓
      onMounted → 挂载后（DOM 已就绪）
        ↓
更新阶段
      onBeforeUpdate → 数据变化、DOM 更新前
        ↓
      onUpdated → DOM 更新后
        ↓
卸载阶段
      onBeforeUnmount → 卸载前（清理副作用）
        ↓
      onUnmounted → 卸载后
```

### Composition API 写法

```vue
<script setup>
import {
  onBeforeMount, onMounted,
  onBeforeUpdate, onUpdated,
  onBeforeUnmount, onUnmounted,
  onActivated, onDeactivated,
  onErrorCaptured,
  onRenderTracked, onRenderTriggered  // 调试用
} from 'vue'

onMounted(() => {
  console.log('组件已挂载，可以操作 DOM')
  // 常见用途：API 请求、DOM 测量、第三方库初始化
})

onUnmounted(() => {
  console.log('组件已卸载')
  // 常见用途：清理定时器、取消订阅、移除事件监听
})
</script>
```

### Options API 写法

```js
export default {
  beforeCreate() {},  // Vue 3 中极少使用，用 setup() 替代
  created() {},       // Vue 3 中用 setup() 替代
  beforeMount() {},
  mounted() {},
  beforeUpdate() {},
  updated() {},
  beforeUnmount() {},
  unmounted() {},
  activated() {},
  deactivated() {},
  errorCaptured() {},
  renderTracked() {},
  renderTriggered() {}
}
```

## 常见使用场景

| 钩子 | 典型用途 |
|------|----------|
| `onMounted` | API 请求、DOM 操作、初始化第三方库（如 chart.js） |
| `onUpdated` | DOM 更新后的操作（不推荐修改状态，避免循环） |
| `onUnmounted` | 清理 `setInterval`、`removeEventListener`、abort 请求 |
| `onErrorCaptured` | 捕获子组件错误，上报错误监控 |
| `onActivated` | `<KeepAlive>` 缓存的组件激活时（刷新数据） |
| `onDeactivated` | `<KeepAlive>` 缓存的组件停用时 |

## 深入：父子组件生命周期顺序

```
父 beforeMount
  → 子 setup / beforeMount / mounted
父 mounted
```

```
父 beforeUpdate
  → 子 beforeUpdate / updated
父 updated
```

```
父 beforeUnmount
  → 子 beforeUnmount / unmounted
父 unmounted
```

> 子组件在父组件之前完成挂载/更新/卸载。

## 深入：异步操作与清理模式

```vue
<script setup>
import { onMounted, onUnmounted } from 'vue'

// 模式：AbortController 清理请求
let controller
onMounted(() => {
  controller = new AbortController()
  fetch('/api/data', { signal: controller.signal })
})
onUnmounted(() => controller?.abort())

// 模式：组合式函数封装
function useAsyncData(url) {
  const data = ref(null)
  const controller = new AbortController()

  onMounted(async () => {
    const res = await fetch(url, { signal: controller.signal })
    data.value = await res.json()
  })
  onUnmounted(() => controller.abort())

  return data
}
</script>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Setup|Setup]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Async-Components|异步组件]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables]]
