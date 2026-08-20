---
tags:
  - vue
  - binding
created: 2026-05-31
---

# 数据绑定

## 速查

| 类型 | 语法 | 方向 |
|------|------|------|
| 单向插值 | `{{ expr }}` | JS → 文本 |
| 单向属性 | `:attr="expr"` | JS → DOM 属性 |
| 双向绑定 | `v-model="ref"` | JS ↔ 表单元素 |
| 事件绑定 | `@event="handler"` | DOM → JS |

## v-model 的本质

```vue
<!-- 这两行等价 -->
<input v-model="text">

<input
  :value="text"
  @input="text = $event.target.value"
>
```

### 不同表单元素的 v-model

| 元素 | 绑定属性 | 触发事件 |
|------|----------|----------|
| `<input type="text">` | `value` | `input` |
| `<input type="checkbox">` | `checked` | `change` |
| `<input type="radio">` | `checked` | `change` |
| `<select>` | `value` | `change` |
| `<textarea>` | `value` | `input` |

### 组件上的 v-model

```vue
<!-- 父组件 -->
<MyInput v-model="name" />

<!-- 等价于 -->
<MyInput :modelValue="name" @update:modelValue="name = $event" />

<!-- 多个 v-model -->
<UserName v-model:first="first" v-model:last="last" />
```

## 深入：v-model 自定义实现

```vue
<!-- CustomInput.vue -->
<script setup>
const props = defineProps({
  modelValue: String
})
const emit = defineEmits(['update:modelValue'])
</script>

<template>
  <input
    :value="modelValue"
    @input="emit('update:modelValue', $event.target.value)"
  >
</template>
```

### 自定义修饰符

```vue
<!-- 使用 -->
<TextInput v-model.capitalize="text" />

<!-- 组件内接收 -->
<script setup>
const props = defineProps({
  modelValue: String,
  modelModifiers: { default: () => ({}) }
})
const emit = defineEmits(['update:modelValue'])

function handleInput(e) {
  let value = e.target.value
  if (props.modelModifiers.capitalize) {
    value = value.charAt(0).toUpperCase() + value.slice(1)
  }
  emit('update:modelValue', value)
}
</script>
```

## 深入：为什么 v-model 是语法糖

Vue 3 的 `v-model` 统一了 Vue 2 中三种模式：

| Vue 2 | Vue 3 |
|-------|-------|
| `v-model` | `v-model`（= `modelValue` + `update:modelValue`） |
| `.sync` 修饰符 | `v-model:propName` |
| 自定义组件 `model` 选项 | `modelValue` prop |

本质上 `v-model` 是**单向数据流 + 事件回调**的语法糖，不是"真正的双向绑定"。数据始终从父到子单向流动，子组件通过事件通知父组件更新。

## 相关

- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Form-Bindings|表单绑定]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
