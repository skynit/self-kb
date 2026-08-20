---
tags:
  - vue
  - components
  - provide
  - inject
created: 2026-05-31
---

# Provide / Inject

## 速查

```vue
<!-- 祖先组件 -->
<script setup>
import { ref, provide } from 'vue'

const theme = ref('dark')
provide('theme', theme)           // 提供响应式值
provide('static-key', 'hello')    // 提供静态值

// 使用 Symbol 避免命名冲突
export const ThemeKey = Symbol()
provide(ThemeKey, theme)
</script>

<!-- 后代组件（任意层级） -->
<script setup>
import { inject } from 'vue'
import { ThemeKey } from './keys'

const theme = inject(ThemeKey)            // 必须
const theme = inject(ThemeKey, 'light')   // 带默认值
const theme = inject(ThemeKey, () => 'light') // 工厂函数默认值
</script>
```

## 应用级 Provide

```js
// main.js — 所有组件都能 inject
import { createApp } from 'vue'

const app = createApp(App)
app.provide('appName', 'My App')
```

## 深入：响应式与只读性

```js
// 提供响应式 ref — 后代组件能自动更新
const count = ref(0)
provide('count', count)

// 只读包装 — 防止后代修改
import { readonly } from 'vue'
provide('count', readonly(count))
```

## Provide/Inject vs Props 传递

| 场景 | 推荐方案 |
|------|----------|
| 直接父子通信 | Props + Emits |
| 跨 1-2 层 | Props 逐层传递 |
| 深层嵌套 / 任意层级 | Provide / Inject |
| 全局状态 | Pinia |

> **注意**：Provide/Inject 让数据流隐式化，大型项目中过度使用会降低可维护性。大多数场景用 Pinia 更好。

## 相关

- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Essentials|Pinia]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
