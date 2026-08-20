---
tags:
  - vue
  - pinia
  - advanced
created: 2026-05-31
---

# Pinia 进阶

## 速查

### Plugins

```js
// plugins/logger.js
export function loggerPlugin({ store }) {
  store.$subscribe((mutation, state) => {
    console.log(`[${store.$id}] ${mutation.type}`, state)
  })

  store.$onAction(({ name, args, after, onError }) => {
    const start = Date.now()
    after(() => console.log(`[${store.$id}] ${name} (${Date.now() - start}ms)`))
    onError((err) => console.error(`[${store.$id}] ${name} failed:`, err))
  })
}

// main.js
pinia.use(loggerPlugin)
```

### 持久化

```js
// plugins/persist.js
export function persistPlugin({ store }) {
  const key = `pinia-${store.$id}`

  // 恢复状态
  const saved = localStorage.getItem(key)
  if (saved) store.$patch(JSON.parse(saved))

  // 保存状态
  store.$subscribe((mutation, state) => {
    localStorage.setItem(key, JSON.stringify(state))
  })
}

// 或使用 pinia-plugin-persistedstate
import piniaPersistedstate from 'pinia-plugin-persistedstate'
pinia.use(piniaPersistedstate)

// store 中声明
export const useUserStore = defineStore('user', () => {
  // ...
  return { name, avatar }
}, { persist: true })
```

### Store 间引用

```js
// stores/user.js
export const useUserStore = defineStore('user', () => {
  const name = ref('')
  return { name }
})

// stores/cart.js — 引用其他 store
export const useCartStore = defineStore('cart', () => {
  const user = useUserStore() // 直接调用

  const greeting = computed(() => {
    return `${user.name}的购物车`
  })

  return { greeting }
})
```

### 在组件外使用

```js
// router/guards.js
import { useUserStore } from '@/stores/user'
import { createPinia } from 'pinia'

// 必须先安装 pinia
const pinia = createPinia()
app.use(pinia)

// 在 router guard 中使用
router.beforeEach((to) => {
  const user = useUserStore() // 在 router guard 中访问 store
  if (to.meta.requiresAuth && !user.isAuthenticated) {
    return '/login'
  }
})
```

## 深入：最佳实践

### Store 拆分原则

```
stores/
├── auth.js        # 认证状态
├── user.js        # 用户信息
├── cart.js        # 购物车
├── products.js    # 产品数据
└── ui.js          # UI 状态（侧边栏、主题等）
```

### 避免的模式

```js
// ✗ 不要在 store 中直接操作 DOM
// ✗ 不要在 store 中存储组件实例
// ✗ 不要把所有状态都放一个 store（太大）
// ✗ 不要在 store 外部直接修改 state（用 actions）
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Essentials|Pinia 基础]]
- [[20-软件开发/01-前端开发/01-Vue/06-Vue-Router/Navigation-Guards|导航守卫]]
