---
tags:
  - vue
  - template
created: 2026-05-31
---

# 条件渲染与列表渲染

## 速查

### 条件渲染

```vue
<!-- v-if / v-else-if / v-else -->
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else>Not A/B</div>

<!-- template 分组（渲染时不产生额外 DOM） -->
<template v-if="ok">
  <h1>Title</h1>
  <p>Content</p>
</template>

<!-- v-show — 只切换 display -->
<div v-show="isVisible">Always in DOM</div>
```

### v-if vs v-show

| 特性 | `v-if` | `v-show` |
|------|--------|----------|
| 渲染方式 | 条件为 false 时不渲染 DOM | 始终渲染，切换 `display` |
| 编译开销 | 惰性，首次为 false 时不编译 | 始终编译 |
| 切换开销 | 高（销毁/重建组件） | 低（CSS 切换） |
| 适用场景 | 条件很少改变 | 频繁切换 |

### 列表渲染

```vue
<!-- 基本用法 -->
<li v-for="item in items" :key="item.id">
  {{ item.name }}
</li>

<!-- 带索引 -->
<li v-for="(item, index) in items" :key="item.id">
  {{ index }} - {{ item.name }}
</li>

<!-- 遍历对象 -->
<li v-for="(value, key, index) in myObject" :key="key">
  {{ index }}. {{ key }}: {{ value }}
</li>

<!-- 遍历范围 -->
<span v-for="n in 10">{{ n }}</span>

<!-- template 上使用 -->
<template v-for="item in items" :key="item.id">
  <li>{{ item.name }}</li>
  <li class="divider" role="presentation"></li>
</template>
```

## 深入：key 的作用

```vue
<!-- 错误：index 作为 key 不稳定 -->
<li v-for="(item, index) in items" :key="index">

<!-- 正确：唯一且稳定的 id -->
<li v-for="item in items" :key="item.id">
```

**key 的本质**：key 是 Virtual DOM 中节点的唯一标识。diff 算法通过 key 判断节点是否"相同"：
- key 相同 → 复用 DOM，对比更新
- key 不同 → 销毁旧节点，创建新节点

**用 index 作 key 的问题**：
```
删除 items[1] 后：
index 0 → item A ✓ (不变)
index 1 → item C ✗ (原来是 B，DOM 复用错误)
index 2 → item D ✗ (原来是 C)
```

## 深入：v-for 与 v-if 的优先级

Vue 3 中 **`v-if` 优先级高于 `v-for`**（与 Vue 2 相反）。

```vue
<!-- 错误：v-if 访问不到 item -->
<li v-for="item in items" v-if="item.isActive">

<!-- 正确：用 template 包裹 -->
<template v-for="item in items" :key="item.id">
  <li v-if="item.isActive">{{ item.name }}</li>
</template>

<!-- 或用 computed 过滤 -->
<script setup>
const activeItems = computed(() => items.filter(i => i.isActive))
</script>
<template>
  <li v-for="item in activeItems" :key="item.id">{{ item.name }}</li>
</template>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Virtual-DOM|虚拟 DOM diff 算法]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Template-Syntax|模板语法]]
