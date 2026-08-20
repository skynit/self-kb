---
tags:
  - vue
  - components
created: 2026-05-31
---

# 组件基础

## 速查

### 定义组件

```vue
<!-- MyComponent.vue -->
<script setup>
import { ref } from 'vue'
const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times</button>
</template>
```

### 使用组件

```vue
<script setup>
import MyComponent from './MyComponent.vue'
</script>

<template>
  <!-- 单标签 -->
  <MyComponent />
  <!-- 多实例 -->
  <MyComponent />
  <MyComponent />
</template>
```

### 全局注册

```js
// main.js — 全局注册（不推荐，不利于 tree-shaking）
app.component('MyComponent', MyComponent)
```

### 动态组件

```vue
<script setup>
import TabA from './TabA.vue'
import TabB from './TabB.vue'

const currentTab = ref('TabA')
const tabs = { TabA, TabB }
</script>

<template>
  <component :is="tabs[currentTab]" />
</template>
```

> `is` 的值可以是：组件选项对象、已注册组件名字符串

## 组件组织方式

```
src/
├── components/          # 通用组件
│   ├── Button.vue
│   ├── Modal.vue
│   └── index.ts         # 统一导出
├── features/            # 按功能组织
│   └── auth/
│       ├── LoginForm.vue
│       └── useAuth.ts
└── views/               # 页面级组件
    ├── HomeView.vue
    └── AboutView.vue
```

## 深入：组件实例与渲染

```
模板中的 <MyComponent />
  ↓
Vue 创建组件实例
  ↓
执行 setup() / Composition API
  ↓
调用 render 函数生成 VNode
  ↓
patch 到真实 DOM
```

- 每个组件实例拥有独立的响应式状态
- 父组件更新时，子组件默认也会更新（可通过 `defineComponent` + `shouldUpdateComponent` 优化）

## 相关

- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Slots|插槽]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Lifecycle-Hooks|生命周期钩子]]
