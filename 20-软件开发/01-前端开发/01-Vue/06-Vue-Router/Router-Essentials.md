---
tags:
  - vue
  - router
created: 2026-05-31
---

# Vue Router 4 核心

## 速查

### 基本配置

```js
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),        // HTML5 History 模式
  // history: createWebHashHistory(),  // Hash 模式
  // history: createMemoryHistory(),   // SSR / 测试
  routes: [
    { path: '/', component: () => import('@/views/Home.vue') },
    { path: '/about', component: () => import('@/views/About.vue') },
    { path: '/user/:id', name: 'user', component: () => import('@/views/User.vue') },
    { path: '/:pathMatch(.*)*', name: 'NotFound', component: () => import('@/views/404.vue') }
  ]
})

export default router
```

### 嵌套路由

```js
const routes = [
  {
    path: '/user/:id',
    component: UserLayout,
    children: [
      { path: '', component: UserProfile },
      { path: 'posts', component: UserPosts },
      { path: 'settings', component: UserSettings }
    ]
  }
]
```

```vue
<!-- UserLayout.vue -->
<template>
  <nav>...</nav>
  <router-view />  <!-- 子路由渲染位置 -->
</template>
```

### Composition API

```vue
<script setup>
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()   // 路由实例（操作）
const route = useRoute()     // 当前路由（信息）

// 当前路由参数
console.log(route.params.id)
console.route.query.search
console.log(route.hash)
console.log(route.path)
console.log(route.name)

// 编程式导航
router.push('/about')
router.push({ name: 'user', params: { id: 1 } })
router.push({ path: '/user', query: { sort: 'name' } })
router.replace('/login')         // 替换当前历史记录
router.go(-1)                    // 后退一步
router.back()                    // 等同于 go(-1)
</script>
```

### 动态路由

```js
// 运行时添加路由
router.addRoute({
  path: '/admin',
  component: AdminLayout,
  meta: { requiresAdmin: true }
})

// 添加子路由
router.addRoute('admin', { path: 'settings', component: AdminSettings })

// 移除路由
router.removeRoute('admin')
```

### 路由组件传参

```js
// 通过 props 解耦（推荐）
const routes = [
  { path: '/user/:id', component: User, props: true },
  // 或者函数形式
  { path: '/search', component: Search, props: (route) => ({ query: route.query.q }) }
]

// User.vue 中
const props = defineProps({ id: String }) // 不再依赖 useRoute()
```

## 深入：HTML5 History vs Hash 模式

| | History | Hash |
|---|---------|------|
| URL | `/about` | `/#/about` |
| SEO | 友好 | 差 |
| 服务端配置 | 需要 fallback | 不需要 |
| 部署 | 需配置 Nginx/Apache | 开箱即用 |

```nginx
# Nginx 配置
location / {
  try_files $uri $uri/ /index.html;
}
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/06-Vue-Router/Navigation-Guards|导航守卫]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Async-Components|异步组件与懒加载]]
