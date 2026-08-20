---
tags:
  - vue
  - events
created: 2026-05-31
---

# 事件处理

## 速查

### 内联处理器

```vue
<button @click="count++">+1</button>
<button @click="handleClick($event, 'arg')">Click</button>
```

### 方法处理器

```vue
<script setup>
function handleClick(event) {
  // event 是原生 DOM Event 对象
  console.log(event.target)
}
</script>

<template>
  <button @click="handleClick">Click</button>
</template>
```

### 事件修饰符

```vue
<!-- 事件传播 -->
@click.stop       <!-- 阻止冒泡 -->
@click.capture    <!-- 捕获模式 -->
@click.self       <!-- 仅当 event.target 是元素本身时触发 -->
@click.once       <!-- 只触发一次 -->
@click.passive    <!-- 不阻止默认行为（移动端性能优化） -->

<!-- 按键修饰符 -->
@keyup.enter
@keyup.tab
@keyup.delete
@keyup.esc
@keyup.space
@keyup.up
@keyup.ctrl      <!-- 系统修饰键 -->
@keyup.ctrl.exact  <!-- 仅按 ctrl 时触发 -->
@keyup.shift.enter  <!-- 组合键 -->
```

### 鼠标按钮修饰符

```vue
@click.left
@click.right
@click.middle
```

## 深入：修饰符链式调用顺序

```vue
<!-- 顺序有影响！先 stop，再 prevent -->
<a @click.stop.prevent="handler">

<!-- 修饰符可以链式调用，但顺序是从左到右执行 -->
<!-- .stop.prevent = event.stopPropagation(); event.preventDefault() -->
```

## 深入：自定义组件事件

```vue
<!-- 子组件 -->
<script setup>
const emit = defineEmits(['submit', 'delete'])
emit('submit', { data: 123 })
</script>

<!-- 父组件 -->
<MyForm @submit="onSubmit" @delete="onDelete" />

<!-- 事件验证 -->
<script setup>
const emit = defineEmits({
  submit: (payload) => {
    return typeof payload.data === 'number'
  }
})
</script>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Form-Bindings|表单绑定]]
