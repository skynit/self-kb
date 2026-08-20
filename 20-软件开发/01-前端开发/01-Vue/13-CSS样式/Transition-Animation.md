---
tags:
  - vue
  - css
  - transition
  - animation
created: 2026-05-31
---

# 过渡与动画

## 速查

### `<Transition>` 单元素

```vue
<template>
  <button @click="show = !show">Toggle</button>
  <Transition name="fade">
    <p v-if="show">Hello</p>
  </Transition>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
```

### 过渡类名时序

```
进入：
  v-enter-from → v-enter-active → v-enter-to
  (初始状态)     (过渡中)           (最终状态)

离开：
  v-leave-from → v-leave-active → v-leave-to
  (初始状态)     (过渡中)           (最终状态)
```

### 自定义类名前缀

```vue
<Transition name="slide" duration="500">
<!-- 类名：slide-enter-from, slide-enter-active ... -->
</Transition>

<!-- 完全自定义类名（可配合 animate.css） -->
<Transition
  enter-active-class="animate__animated animate__fadeIn"
  leave-active-class="animate__animated animate__fadeOut"
>
  <p v-if="show">Hello</p>
</Transition>
```

### JavaScript 钩子

```vue
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @before-leave="onBeforeLeave"
  @leave="onLeave"
  @after-leave="onAfterLeave"
>
  <p v-if="show">Hello</p>
</Transition>

<script setup>
function onEnter(el, done) {
  // el: DOM 元素
  // done: 必须调用（告诉 Vue 动画结束）
  el.style.opacity = 0
  requestAnimationFrame(() => {
    el.style.transition = 'opacity 0.3s'
    el.style.opacity = 1
    el.addEventListener('transitionend', done, { once: true })
  })
}
</script>
```

### `<TransitionGroup>` 列表过渡

```vue
<template>
  <TransitionGroup name="list" tag="ul">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </TransitionGroup>
</template>

<style scoped>
.list-enter-active,
.list-leave-active {
  transition: all 0.4s ease;
}
.list-enter-from {
  opacity: 0;
  transform: translateX(-30px);
}
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
.list-move {
  transition: transform 0.4s ease;
}
</style>
```

## 深入：过渡模式

```vue
<!-- mode 控制进入/离开的时序 -->
<Transition mode="out-in">
  <!-- 先离开，再进入（切换组件常用） -->
  <component :is="currentView" />
</Transition>

<Transition mode="in-out">
  <!-- 先进入，再离开 -->
</Transition>
```

| 模式 | 行为 | 场景 |
|------|------|------|
| 默认 | 进入和离开同时 | — |
| `out-in` | 先离开再进入 | Tab 切换、路由切换 |
| `in-out` | 先进入再离开 | 少用 |

## 深入：`<KeepAlive>` + `<Transition>`

```vue
<Transition mode="out-in">
  <KeepAlive>
    <component :is="currentTab" />
  </KeepAlive>
</Transition>
```

> 先 `KeepAlive` 缓存组件，再用 `Transition` 切换动画。

## 深入：列表过渡性能

```vue
<!-- TransitionGroup 优化 -->
<TransitionGroup
  name="list"
  tag="ul"
  :css="false"
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @leave="onLeave"
>
  <!-- 使用 JS 动画时设 :css="false" 跳过 CSS 类检测 -->
</TransitionGroup>
```

### Flip 动画技术

```js
// FLIP: First → Last → Invert → Play
function onEnter(el, done) {
  // Last: 计算新位置
  const rect = el.getBoundingClientRect()
  // Invert: 反转到旧位置
  el.style.transform = `translateY(${oldY - rect.top}px)`
  // Play: 过渡到新位置
  requestAnimationFrame(() => {
    el.style.transition = 'transform 0.3s'
    el.style.transform = ''
    el.addEventListener('transitionend', done, { once: true })
  })
}
```

## 常用过渡效果

```css
/* 淡入淡出 */
.fade-enter-active, .fade-leave-active { transition: opacity 0.3s; }
.fade-enter-from, .fade-leave-to { opacity: 0; }

/* 缩放 */
.scale-enter-active, .scale-leave-active { transition: transform 0.2s; }
.scale-enter-from, .scale-leave-to { transform: scale(0.9); }

/* 滑入 */
.slide-enter-active, .slide-leave-active { transition: transform 0.3s; }
.slide-enter-from { transform: translateY(-10px); }
.slide-leave-to { transform: translateY(10px); }
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Async-Components|异步组件与 Suspense]]
- [[20-软件开发/01-前端开发/01-Vue/11-性能优化/Performance|性能优化]]
