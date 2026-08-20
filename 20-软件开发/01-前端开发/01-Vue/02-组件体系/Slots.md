---
tags:
  - vue
  - components
  - slots
created: 2026-05-31
---

# 插槽

## 速查

### 默认插槽

```vue
<!-- 子组件 (AlertBox.vue) -->
<template>
  <div class="alert">
    <slot>默认内容</slot>  <!-- 没有传入内容时显示 -->
  </div>
</template>

<!-- 父组件 -->
<AlertBox>
  <p>这是插槽内容</p>
</AlertBox>
```

### 具名插槽

```vue
<!-- 子组件 -->
<template>
  <header><slot name="header"></slot></header>
  <main><slot></slot></main>
  <footer><slot name="footer"></slot></footer>
</template>

<!-- 父组件 -->
<BaseLayout>
  <template #header>
    <h1>标题</h1>
  </template>

  <template #default>
    <p>主体内容</p>
  </template>

  <template #footer>
    <p>底部</p>
  </template>
</BaseLayout>
```

### 作用域插槽

```vue
<!-- 子组件 -->
<template>
  <ul>
    <li v-for="item in items">
      <slot :item="item" :index="$index"></slot>
    </li>
  </ul>
</template>

<!-- 父组件 -->
<MyList :items="list">
  <template #default="{ item, index }">
    <span>{{ index }}. {{ item.name }}</span>
  </template>
</MyList>

<!-- 独占默认插槽的缩写 -->
<MyList :items="list" #default="{ item }">
  <span>{{ item.name }}</span>
</MyList>
```

### 动态插槽名

```vue
<template #[dynamicSlotName]>
  内容
</template>
```

## 深入：渲染作用域

```
父组件模板    子组件模板
┌─────────┐   ┌──────────┐
│ 父组件数据 │   │ 子组件数据  │
│ 可见     │ → │ 插槽内容可见 │
│ 子组件数据 │   │ 父组件数据  │
│ 不可见   │ ← │ 不可见    │
└─────────┘   └──────────┘
```

- 插槽内容在**父组件作用域**中编译
- 子组件通过作用域插槽将数据"传递"给插槽内容

## 深入：高阶模式

### 无渲染组件

```vue
<!-- MouseTracker.vue — 只提供数据，不负责渲染 -->
<script setup>
const x = ref(0)
const y = ref(0)
window.addEventListener('mousemove', (e) => {
  x.value = e.clientX
  y.value = e.clientY
})
</script>

<template>
  <slot :x="x" :y="y"></slot>
</template>

<!-- 使用 -->
<MouseTracker #default="{ x, y }">
  鼠标位置：{{ x }}, {{ y }}
</MouseTracker>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Component-Basics|组件基础]]
