---
tags:
  - vue
  - css
  - css-modules
created: 2026-05-31
---

# CSS Modules

## 速查

### 基本用法

```vue
<template>
  <p :class="$style.red">红色文字</p>
  <p :class="{ [$style.active]: isActive }">条件样式</p>
  <p :class="[$style.text, $style.bold]">组合样式</p>
  <p :class="$style['text-center']">kebab-case</p>
</template>

<style module>
.red {
  color: red;
}
.active {
  font-weight: bold;
}
.text {
  font-size: 14px;
}
.bold {
  font-weight: 700;
}
.text-center {
  text-align: center;
}
</style>
```

### 命名模块

```vue
<script setup>
import { useCssModule } from 'vue'

// 获取命名模块
const styles = useCssModule('classes')
// styles.red → "red_xxxxx"（hash 化类名）
</script>

<template>
  <p :class="classes.red">命名模块样式</p>
</template>

<style module="classes">
.red { color: red; }
</style>
```

### 动态样式

```vue
<script setup>
import { computed } from 'vue'
const props = defineProps({ theme: String })

const classes = computed(() => ({
  [$style.card]: true,
  [$style.dark]: props.theme === 'dark',
  [$style.light]: props.theme === 'light'
}))
</script>

<template>
  <div :class="classes">...</div>
</template>
```

## 深入：CSS Modules vs Scoped CSS

| 特性   | CSS Modules        | Scoped CSS       |
| ---- | ------------------ | ---------------- |
| 类名生成 | hash 化唯一类名         | 原类名 + 属性选择器      |
| 性能   | 无额外选择器开销           | 属性选择器有微小开销       |
| 调试   | 类名不直观（需 sourcemap） | 类名保持原样           |
| 穿透   | 不需要（用全局类名混用即可）     | 需要 `:deep()`     |
| 变量注入 | `$style` 动态绑定      | 只能 `:class` 条件切换 |

### 适用场景

- **CSS Modules** — 组件库、需要极致性能、样式完全隔离
- **Scoped CSS** — 一般业务开发、快速原型

## 相关

- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/Scoped-CSS|Scoped CSS]]
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/v-bind-in-CSS|v-bind in CSS]]
