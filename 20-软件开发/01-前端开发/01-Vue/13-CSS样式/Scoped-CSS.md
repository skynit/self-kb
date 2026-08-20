---
tags:
  - vue
  - css
  - scoped
created: 2026-05-31
updated: 2026-06-01
---

# Scoped CSS 与样式隔离

## 问题：为什么需要样式隔离

Vue 组件化开发中，所有 `<style>` 默认是**全局**的：

```vue
<!-- Button.vue -->
<style>
.button { background: blue; }
</style>

<!-- Card.vue -->
<style>
.button { background: red; }  <!-- 冲突！覆盖了 Button 的样式 -->
</style>
```

两个组件都定义了 `.button`，后加载的会覆盖先加载的。项目越大，样式冲突越严重。

**Scoped CSS 就是解决这个问题**的：让样式只作用于当前组件。

---

## 基本用法

```vue
<template>
  <button class="button">Click</button>
</template>

<style scoped>
.button {
  background: blue;
}
</style>
```

加一个 `scoped` 属性就行了。编译后 Vue 会自动做两件事：

```
1. 模板中每个元素加一个唯一属性 data-v-xxxx
2. CSS 选择器末尾追加属性选择器 [data-v-xxxx]
```

编译结果：

```html
<!-- DOM -->
<button class="button" data-v-7a7a37b1>Click</button>

<!-- CSS -->
.button[data-v-7a7a37b1] {
  background: blue;
}
```

这样 `.button[data-v-7a7a37b1]` 只能选中**带有该属性的元素**，即当前组件内的元素。其他组件的 `.button` 没有这个属性，不会被匹配。

---

## 实现原理详解

### 第一步：生成唯一 hash

编译器根据**文件路径 + 内容**生成一个唯一标识，如 `data-v-7a7a37b1`。每个组件的 hash 不同。

### 第二步：模板注入

```vue
<!-- 源码 -->
<template>
  <div class="container">
    <span class="label">hello</span>
  </div>
</template>

<!-- 编译后 -->
<template>
  <div class="container" data-v-7a7a37b1>
    <span class="label" data-v-7a7a37b1>hello</span>
  </div>
</template>
```

**每个元素**都会加上 `data-v-7a7a37b1`。

### 第三步：CSS 改写

```css
/* 源码 */
.container { padding: 10px; }
.label { color: red; }

/* 编译后 */
.container[data-v-7a7a37b1] { padding: 10px; }
.label[data-v-7a7a37b1] { color: red; }
```

选择器从 `.container` 变成 `.container[data-v-7a7a7a37b1]`，只能匹配同时有该 class **和**该属性的元素。

---

## 深度选择器：穿透 scoped

Scoped 隔离了样式，但有时你需要**影响子组件内部**的样式。

### 问题场景

```vue
<!-- Parent.vue -->
<template>
  <ChildComponent />
</template>

<style scoped>
/* ❌ 不生效！.title 属于 ChildComponent 内部，没有父组件的 data-v-xxx */
.title {
  color: red;
}
</style>
```

`.title[data-v-parent]` 要求元素同时有 `data-v-parent` 属性，但 `.title` 在子组件内部，只有子组件的 `data-v-child` 属性。

### :deep() 穿透

```vue
<style scoped>
/* ✅ 穿透到子组件内部 */
:deep(.title) {
  color: red;
}
</style>
```

编译结果：

```css
/* data-v-xxx 放在了 :deep() 前面的元素上，不是 .title 上 */
[data-v-parent] .title {
  color: red;
}
```

**关键区别**：
- 普通 scoped：`.title[data-v-parent]` → 要求 `.title` 本身带属性
- `:deep()`：`[data-v-parent] .title` → 只要求**祖先**带属性，`.title` 可以是任意后代

### :slotted() 作用域插槽内容

```vue
<!-- Child.vue -->
<template>
  <slot></slot>
</template>

<style scoped>
/* ❌ 不生效！插槽内容属于父组件，有父组件的 data-v，没有子组件的 */
.slot-content {
  color: blue;
}

/* ✅ 用 :slotted() 选中插槽渲染出来的内容 */
:slotted(.slot-content) {
  color: blue;
}
</style>
```

编译结果：

```css
/* :slotted() 把属性选择器放在插槽容器上 */
[data-v-child] .slot-content {
  color: blue;
}
```

### :global() 全局样式

```vue
<style scoped>
/* 这个组件里只想写一条全局样式，不想再开一个 <style> 块 */
:global(.global-tooltip) {
  z-index: 9999;
}
</style>
```

编译结果：直接输出 `.global-tooltip { z-index: 9999; }`，不加任何属性选择器。

### 三种选择器总结

| 选择器 | 作用 | 编译结果 |
|--------|------|----------|
| 普通 scoped | 只选当前组件元素 | `.class[data-v-xxx]` |
| `:deep(.class)` | 穿透到子组件内部 | `[data-v-xxx] .class` |
| `:slotted(.class)` | 选插槽渲染的内容 | `[data-v-xxx] .class`（作用在 slot 容器上） |
| `:global(.class)` | 写全局样式 | `.class`（无属性选择器） |

> Vue 2 的 `/deep/`、`::v-deep`、`>>>` 在 Vue 3 中已废弃，统一用 `:deep()`。

---

## 局限性

### 1. 父组件 scoped 会影响子组件根元素

```vue
<!-- Parent.vue -->
<template>
  <ChildComponent class="wrapper" />
</template>

<style scoped>
.wrapper {
  background: gray;  /* ✅ 生效！因为子组件根元素会同时获得父组件的 data-v */
}
</style>
```

**原因**：子组件的**根元素**会同时拥有自己的 `data-v-child` 和父组件的 `data-v-parent`。这是 Vue 的设计——让父组件可以调整子组件根元素的样式。

### 2. v-html 内容不受 scoped 影响

```vue
<template>
  <div v-html="rawHtml"></div>
</template>

<style scoped>
/* ❌ 不生效！v-html 插入的内容在运行时才生成，没有编译时注入 data-v */
.content {
  color: red;
}

/* ✅ 用 :deep() 穿透 */
:deep(.content) {
  color: red;
}
</style>
```

### 3. 属性选择器性能

属性选择器 `[data-v-xxx]` 比 class 选择器略慢，但在实际项目中**可忽略**。只有极端场景（数万 DOM 节点 + 大量 scoped 规则）才可能有影响。

---

## scoped vs 非 scoped vs CSS Modules

| | 普通 `<style>` | `<style scoped>` | `<style module>` |
|--|--------------|-----------------|-----------------|
| 作用域 | 全局 | 组件内 | 组件内 |
| 原理 | 无处理 | 属性选择器 `[data-v-xxx]` | 类名 hash 化 |
| 类名冲突 | ✅ 会冲突 | ❌ 不冲突 | ❌ 不冲突 |
| 穿透子组件 | 默认穿透 | 需 `:deep()` | 不适用（通过 props 传类名） |
| 模板中引用 | `class="name"` | `class="name"` | `:class="$style.name"` |
| 运行时开销 | 无 | 属性选择器微小开销 | 无 |

### CSS Modules 写法对比

```vue
<!-- Scoped CSS：类名不变，靠属性选择器隔离 -->
<style scoped>
.button { background: blue; }
</style>
<template>
  <button class="button">Click</button>
</template>

<!-- CSS Modules：类名被 hash 化，彻底避免冲突 -->
<style module>
.button { background: blue; }
</style>
<template>
  <button :class="$style.button">Click</button>
  <!-- 渲染为 <button class="_button_1a2b3"> -->
</template>
```

---

## 最佳实践

```
1. 默认所有组件都用 <style scoped>
2. 需要影响子组件内部 → :deep()
3. 需要影响插槽内容 → :slotted()
4. 全局样式（重置、主题变量）→ 单独的 <style>（不加 scoped）
5. v-html 内容 → 用 :deep()
```

```vue
<!-- 一个组件中可以同时有多个 style 块 -->
<style scoped>
/* 组件内部样式 */
.container { padding: 16px; }
</style>

<style>
/* 全局样式（如第三方组件覆盖） */
.el-dialog { border-radius: 8px; }
</style>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/CSS-Modules|CSS Modules]]
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/v-bind-in-CSS|v-bind in CSS]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Class-and-Style|Class 与 Style 绑定]]
