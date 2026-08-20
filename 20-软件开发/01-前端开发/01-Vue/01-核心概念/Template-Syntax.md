---
tags:
  - vue
  - template
created: 2026-05-31
---

# 模板语法

## 速查

### 文本插值

```vue
<span>{{ message }}</span>
<span>{{ ok ? '是' : '否' }}</span>
<span>{{ message.split('').reverse().join('') }}</span>
```

> 只能包含**单个表达式**，不能是语句（不能用 `if` / `for`）。

### 原始 HTML

```vue
<p v-html="rawHtml"></p>
```

> 永远不要对用户内容使用 `v-html`，XSS 风险。

### 属性绑定

```vue
<div v-bind:id="dynamicId"></div>
<!-- 缩写 -->
<div :id="dynamicId"></div>
<!-- 同名简写 (Vue 3.4+) -->
<div :id></div>
<!-- 动态属性名 -->
<div :[attrName]="value"></div>
```

### 指令

| 指令 | 用途 | 示例 |
|------|------|------|
| `v-bind` / `:` | 属性绑定 | `:class="active"` |
| `v-on` / `@` | 事件监听 | `@click="handle"` |
| `v-model` | 双向绑定 | `v-model="text"` |
| `v-if` / `v-else-if` / `v-else` | 条件渲染 | — |
| `v-show` | 显示/隐藏 | — |
| `v-for` | 列表渲染 | `v-for="item in list"` |
| `v-slot` / `#` | 插槽 | `#default` |
| `v-pre` | 跳过编译 | — |
| `v-once` | 一次性插值 | — |
| `v-memo` | 记忆化 (3.2+) | `v-memo="[a, b]"` |

### 修饰符

```vue
<!-- 事件修饰符 -->
@click.stop     <!-- 阻止冒泡 -->
@click.prevent   <!-- 阻止默认行为 -->
@submit.prevent  <!-- 常见于表单 -->
@click.once      <!-- 只触发一次 -->
@keyup.enter     <!-- 按键修饰符 -->

<!-- v-model 修饰符 -->
v-model.trim    <!-- 去首尾空格 -->
v-model.number  <!-- 转数字 -->
v-model.lazy    <!-- change 事件触发 -->
```

## 深入：模板编译过程

```
模板字符串 → AST（抽象语法树）→ 代码生成 → render 函数
```

1. **解析** — 模板被解析为 AST 节点树，识别标签、属性、指令、表达式
2. **转换** — 对 AST 做优化标记（静态节点、静态根节点、Patch Flags）
3. **生成** — 产出 `render` 函数代码字符串，通过 `new Function()` 变为可执行函数

```js
// 模板
<div id="app">{{ msg }}</div>

// 编译产物（简化）
function render(_ctx) {
  return createVNode('div', { id: 'app' }, toDisplayString(_ctx.msg), PatchFlags.TEXT)
}
```

> `<template>` 本质是语法糖，最终都会变成 render 函数。手写 `h()` 函数可以完全替代模板。

## 相关

- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Data-Binding|数据绑定]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Event-Handling|事件处理]]
- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Compiler-Optimizations|编译器优化]]
