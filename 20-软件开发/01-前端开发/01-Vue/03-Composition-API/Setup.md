---
tags:
  - vue
  - composition-api
created: 2026-05-31
---

# setup 函数与 script setup

## 速查

### script setup（推荐）

```vue
<script setup>
import { ref, computed } from 'vue'
import MyComponent from './MyComponent.vue'

// 顶层变量/函数自动暴露给模板
const count = ref(0)
const doubled = computed(() => count.value * 2)
const increment = () => count.value++

// 组件自动注册（import 即注册）
</script>

<template>
  <MyComponent :count="count" @click="increment" />
</template>
```

### script setup 编译后（理解原理用）

```js
// 等价于
export default {
  setup() {
    const count = ref(0)
    const doubled = computed(() => count.value * 2)
    const increment = () => count.value++
    return { count, doubled, increment }
  }
}
```

## defineProps / defineEmits / defineExpose

```vue
<script setup>
// 这三个是编译器宏，不需要 import
const props = defineProps({ msg: String })
const emit = defineEmits(['change'])

// 暴露给父组件（通过 ref 访问）
defineExpose({ count, increment })
</script>
```

### defineOptions

```vue
<script setup>
// Vue 3.3+ — 定义其他选项
defineOptions({
  name: 'MyComponent',
  inheritAttrs: false
})
</script>
```

### defineSlots（类型辅助）

```vue
<script setup>
// Vue 3.3+ — 仅为类型声明
defineSlots<{
  default(props: { item: Item }): any
  header(props: { title: string }): any
}>()
</script>
```

## useSlots / useAttrs

```vue
<script setup>
import { useSlots, useAttrs } from 'vue'

const slots = useSlots()
const attrs = useAttrs()

// slots.header() — 渲染具名插槽
// attrs — 透传属性
</script>
```

## 深入：script setup 的限制

1. **不能使用顶层 await**（除非配合 `<Suspense>`）
2. **不能使用自定义顶层属性**（如 `name`，改用 `defineOptions`）
3. **不能使用 `this`**（没有 Options API 的上下文）
4. **不能动态组件名**（`import` 语句在编译时处理）

## 深入：为什么 script setup 性能更好

`<script setup>` 编译时优化：
- 所有顶层绑定直接内联到 render 函数中，减少对象解构
- `defineProps` / `defineEmits` 被编译为运行时声明，无额外开销
- 组件导入自动注册，不经过运行时 resolveComponent

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Ref-and-Reactive|ref 与 reactive]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Vue3-Overview|Vue 3 总览]]
