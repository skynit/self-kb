---
tags:
  - vue
  - components
  - async
created: 2026-05-31
---

# 异步组件与 Suspense

## 速查

### defineAsyncComponent

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() => import('./HeavyComponent.vue'))

// 带选项
const AsyncComp = defineAsyncComponent({
  loader: () => import('./HeavyComponent.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorDisplay,
  delay: 200,           // 延迟显示 loading（避免闪烁）
  timeout: 3000,        // 超时时间
  suspensible: false,   // 是否触发 Suspense
  onError(error, retry, fail, attempts) {
    if (attempts <= 3) retry()
    else fail()
  }
})
```

### Suspense

```vue
<template>
  <Suspense>
    <!-- 默认插槽：异步组件 -->
    <template #default>
      <AsyncUserProfile />
    </template>

    <!-- fallback：加载中显示 -->
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>
```

## Suspense 的触发条件

组件的 `setup` 是**异步函数**时触发 Suspense：

```vue
<script setup>
// 这个组件会触发父级 <Suspense>
const data = await fetch('/api/user').then(r => r.json())
</script>
```

## 深入：Suspense 嵌套

```vue
<Suspense>
  <template #default>
    <div>
      <Header />  <!-- 异步 -->
      <Suspense>
        <template #default>
          <Content />  <!-- 另一个异步组件 -->
        </template>
        <template #fallback>
          <Skeleton />
        </template>
      </Suspense>
    </div>
  </template>
  <template #fallback>
    <FullPageLoader />
  </template>
</Suspense>
```

> 外层 Suspense 等所有异步子组件就绪才 resolve。内层独立 resolve。

## 深入：路由级懒加载

```js
// router/index.js
const routes = [
  {
    path: '/dashboard',
    component: () => import('../views/Dashboard.vue')  // 自动代码分割
  }
]
```

> 配合 Vite 的动态 import，构建时自动分割为独立 chunk，按需加载。

## 相关

- [[20-软件开发/01-前端开发/01-Vue/06-Vue-Router/Router-Essentials|Vue Router]]
- [[20-软件开发/01-前端开发/01-Vue/11-性能优化/Performance|性能优化]]
