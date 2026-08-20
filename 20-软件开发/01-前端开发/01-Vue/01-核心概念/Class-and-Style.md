---
tags:
  - vue
  - binding
created: 2026-05-31
---

# Class 与 Style 绑定

## 速查

### Class 绑定

```vue
<!-- 对象语法 -->
<div :class="{ active: isActive, 'text-danger': hasError }"></div>

<!-- 数组语法 -->
<div :class="[activeClass, errorClass]"></div>

<!-- 数组 + 对象混合 -->
<div :class="[isActive ? activeClass : '', { 'text-danger': hasError }]"></div>

<!-- 组件上 — 会合并到根元素 -->
<MyComponent :class="{ active: isActive }" />
```

### Style 绑定

```vue
<!-- 对象语法 -->
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

<!-- 绑定样式对象 -->
<div :style="styleObject"></div>

<!-- 数组（合并多个对象） -->
<div :style="[baseStyles, overridingStyles]"></div>

<!-- 自动前缀 -->
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

## 深入：类名合并规则

1. **静态 class 和动态 :class 会合并**
   ```vue
   <div class="static" :class="{ active: isActive }">
   <!-- 渲染为 -->
   <div class="static active">
   ```

2. **组件根元素继承**
   - 单根组件：class/style 自动透传到根元素
   - 多根组件：需用 `$attrs` 手动指定

## 相关

- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Template-Syntax|模板语法]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
