---
tags:
  - vue
  - router
  - guards
created: 2026-05-31
---

# 导航守卫

## 速查

### 全局守卫

```js
// 全局前置守卫
router.beforeEach((to, from) => {
  if (to.meta.requiresAuth && !isAuthenticated()) {
    return { name: 'login', query: { redirect: to.fullPath } }
  }
  // 返回 undefined → 放行
  // 返回 false → 中断导航
  // 返回路由地址 → 重定向
})

// 全局解析守卫（所有组件内守卫和异步路由解析后）
router.beforeResolve(async (to) => {
  // 适合：数据获取
})

// 全局后置钩子
router.afterEach((to, from, failure) => {
  document.title = to.meta.title || 'App'
})
```

### 路由独享守卫

```js
const routes = [
  {
    path: '/admin',
    component: Admin,
    beforeEnter: (to, from) => {
      if (!isAdmin()) return false
    }
  }
]
```

### 组件内守卫

```vue
<script setup>
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'

// 进入守卫 — 在 setup 中用 beforeRouteEnter 选项
// (Composition API 没有 onBeforeRouteEnter，因为组件还未创建)

// 更新守卫（路由参数变化但组件复用时）
onBeforeRouteUpdate(async (to, from) => {
  // 重新获取数据
  userData.value = await fetchUser(to.params.id)
})

// 离开守卫（适合未保存表单提示）
onBeforeRouteLeave((to, from) => {
  if (hasUnsavedChanges.value) {
    const answer = window.confirm('有未保存的更改，确认离开？')
    if (!answer) return false // 取消导航
  }
})
</script>
```

## 深入：执行顺序

```
1. 导航被触发
2. 在失活组件调用 beforeRouteLeave
3. 调用全局 beforeEach
4. 在复用组件调用 beforeRouteUpdate
5. 在路由配置调用 beforeEnter
6. 解析异步路由组件
7. 在被激活组件调用 beforeRouteEnter
8. 调用全局 beforeResolve
9. 导航被确认
10. 调用全局 afterEach
11. 触发 DOM 更新
12. 调用 beforeRouteEnter 的 next 回调
```

## 深入：鉴权模式

```js
// 白名单模式
const publicPaths = ['/login', '/register', '/forgot-password']

router.beforeEach((to) => {
  const isPublic = publicPaths.includes(to.path)
  const isAuth = useAuthStore().isAuthenticated

  if (!isPublic && !isAuth) {
    return { name: 'login', query: { redirect: to.fullPath } }
  }
  if (to.path === '/login' && isAuth) {
    return { name: 'dashboard' }  // 已登录时重定向
  }
})
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/06-Vue-Router/Router-Essentials|Vue Router 核心]]
- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Essentials|Pinia 状态管理]]
