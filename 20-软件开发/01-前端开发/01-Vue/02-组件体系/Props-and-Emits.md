---
tags:
  - vue
  - components
  - props
created: 2026-05-31
---

# Props 与 Emits

## 速查

### Props

```vue
<script setup>
// 运行时声明
const props = defineProps({
  title: String,
  likes: Number,
  propA: [String, Number], // 多类型
  status: {
    type: String,
    required: true,
    default: 'pending',
    validator: (v) => ['pending', 'active', 'done'].includes(v)
  },
  // 对象/数组默认值必须用工厂函数
  items: {
    type: Array,
    default: () => []
  }
})

// 基于类型的声明（TypeScript 推荐）
const props = defineProps<{
  title: string
  likes?: number
  items?: string[]
}>()

// 带默认值（Vue 3.3+）
const props = withDefaults(defineProps<{
  title: string
  likes?: number
}>(), {
  likes: 0
})
</script>
```

### Emits

```vue
<script setup>
// 运行时声明
const emit = defineEmits(['submit', 'delete'])

// 带验证
const emit = defineEmits({
  submit: (payload) => typeof payload === 'object',
  delete: null // 不需要验证
})

// TypeScript 声明
const emit = defineEmits<{
  submit: [data: FormData]
  delete: [id: number]
}>()

// 触发
emit('submit', formData)
emit('delete', 42)
</script>
```

### 单向数据流

```
父组件 state
    ↓ (prop down)
子组件 props（只读！）
    ↑ (event up)
父组件 通过事件更新 state
```

> **永远不要在子组件中直接修改 prop**。需要本地副本时：
> ```js
> // 方式一：computed
> const titleCopy = computed(() => props.title.toUpperCase())
> // 方式二：本地 ref 副本
> const localTitle = ref(props.title)
> // 方式三：watch 同步
> watch(() => props.title, (v) => { localTitle.value = v })
> ```

## 深入：Attrs 透传

```vue
<!-- 父组件 -->
<MyButton class="large" @click="handler" />

<!-- 子组件未声明 class 和 click -->
<script setup>
// $attrs 包含所有未被 props/emits 声明的属性
// 自动透传到组件的根元素
</script>
<template>
  <button>Click</button>
  <!-- 渲染为: <button class="large">Click</button> -->
</template>
```

### 禁用透传

```vue
<script setup>
defineOptions({ inheritAttrs: false })
</script>

<template>
  <div class="wrapper">
    <button v-bind="$attrs">Click</button>
  </div>
</template>
```

### 多根元素手动指定

```vue
<template>
  <header>...</header>
  <main v-bind="$attrs">...</main>  <!-- 手动指定透传到哪个元素 -->
  <footer>...</footer>
</template>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Slots|插槽]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Component-Basics|组件基础]]
