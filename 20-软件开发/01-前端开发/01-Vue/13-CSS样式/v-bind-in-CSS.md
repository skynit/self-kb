---
tags:
  - vue
  - css
created: 2026-05-31
---

# v-bind in CSS

## 速查

### 基本用法（Vue 3.2+）

```vue
<script setup>
import { ref } from 'vue'
const theme = ref('red')
const fontSize = ref(16)
</script>

<template>
  <p class="text">动态样式</p>
</template>

<style scoped>
.text {
  color: v-bind(theme);
  font-size: v-bind(fontSize + 'px');
}
</style>

<!-- 编译后 -->
<style>
.text[data-v-xxx] {
  color: var(--xxxxxxxx);
}
</style>
```

> `v-bind` 在 CSS 中编译为 CSS 变量（`var(--xxx)`），运行时通过内联 style 更新变量值。

### 动态 class vs v-bind in CSS

```vue
<!-- 方式一：动态 class（传统） -->
<div :class="{ dark: isDark, large: isLarge }">
<style>
.dark { background: #333; }
.large { font-size: 20px; }
</style>

<!-- 方式二：v-bind in CSS（简洁） -->
<script setup>
const bg = ref('#333')
const size = ref(20)
</script>
<style>
.box {
  background: v-bind(bg);
  font-size: v-bind(size + 'px');
}
</style>
```

### 使用场景

| 场景 | 推荐方式 |
|------|----------|
| 颜色、尺寸等连续值 | `v-bind` in CSS |
| 开关式样式（有/无） | 动态 `:class` |
| 复杂条件组合 | 计算属性 + `:class` / `:style` |
| 主题切换 | CSS 变量（全局） > `v-bind`（组件级） |

## 深入：性能考虑

```
v-bind 在 CSS 中的更新流程：
  ref 变化
    → 触发组件更新
    → 更新根元素内联 style 的 CSS 变量值
    → 浏览器重新计算样式
```

- 高频更新（如鼠标跟随颜色）会产生样式重计算
- 此类场景建议用 `:style` 直接绑定，或 CSS Houdini

## 深入：与 CSS 变量的关系

```vue
<style>
/* CSS 变量 — 全局/组件级主题 */
:root {
  --primary-color: #1677ff;
  --border-radius: 8px;
}

.card {
  color: var(--primary-color);
  border-radius: var(--border-radius);
}
</style>

<script setup>
// v-bind in CSS — 组件内动态值
const cardColor = ref('#1677ff')
</script>
<style scoped>
.card {
  color: v-bind(cardColor); /* 运行时响应式 */
}
</style>
```

| | CSS 变量 | v-bind in CSS |
|---|---------|--------------|
| 作用域 | 全局 / 级联 | 组件内 |
| 响应式 | 手动更新 `setProperty` | 自动更新 |
| 用途 | 主题系统 | 组件动态样式 |

## 相关

- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/Scoped-CSS|Scoped CSS]]
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/CSS-Modules|CSS Modules]]
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/预处理器|预处理器]]
