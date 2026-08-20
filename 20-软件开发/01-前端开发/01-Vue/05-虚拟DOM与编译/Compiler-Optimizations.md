---
tags:
  - vue
  - compiler
  - optimization
created: 2026-05-31
---

# 编译器优化

## 速查：Vue 3 三大编译优化

| 优化 | 解决的问题 | 效果 |
|------|-----------|------|
| 静态提升 (HoistStatic) | 每次渲染重新创建静态 VNode | 静态 VNode 只创建一次 |
| Patch Flags | diff 时全量对比 props | 只对比变化的属性 |
| Block Tree | 遍历整棵 VNode 树 | 只遍历有动态标记的节点 |

## 静态提升 (HoistStatic)

```vue
<template>
  <div>
    <p>这是静态文本</p>  <!-- 永远不变 -->
    <span>{{ msg }}</span>
  </div>
</template>
```

```js
// 未优化：每次 render 重新创建
function render() {
  return createVNode('div', null, [
    createVNode('p', null, '这是静态文本'), // 每次都创建新的
    createVNode('span', null, toDisplayString(msg), 1)
  ])
}

// 优化后：静态节点提升到 render 外部
const _hoisted_1 = createVNode('p', null, '这是静态文本')
function render() {
  return createVNode('div', null, [
    _hoisted_1, // 复用同一个 VNode
    createVNode('span', null, toDisplayString(msg), 1)
  ])
}
```

## Patch Flags

```vue
<!-- 编译器标记每个动态节点的变化类型 -->
<div :id="id">{{ text }}</div>
<!-- 编译为: createVNode('div', { id: _ctx.id }, _ctx.text, PatchFlags.TEXT | PatchFlags.PROPS, ['id']) -->
```

| Flag | 值 | 含义 |
|------|----|------|
| `TEXT` | 1 | 只有文本变化 |
| `CLASS` | 2 | 只有 class 变化 |
| `STYLE` | 4 | 只有 style 变化 |
| `PROPS` | 8 | 有动态属性（除 class/style） |
| `FULL_PROPS` | 16 | 有动态 key 的属性（需要全量对比） |
| `NEED_HYDRATION` | 32 | 需要水合 |
| `STABLE_FRAGMENT` | 64 | 稳定顺序的 fragment |
| `KEYED_FRAGMENT` | 128 | 有 key 的 fragment |
| `UNKEYED_FRAGMENT` | 256 | 无 key 的 fragment |
| `NEED_PATCH` | 512 | 非 prop 的需要对比（ref、指令等） |
| `DYNAMIC_SLOTS` | 1024 | 动态插槽 |
| `HOISTED` | -1 | 静态提升的节点，跳过 diff |
| `BAIL` | -2 | 退出优化模式，全量 diff |

## Block Tree

```
Block (div#app)
├── 静态 p  ← 跳过
├── Block (动态 div)
│   ├── patchFlag: TEXT | PROPS, dynamicProps: ['id']
│   └── → diff 时只对比 text 和 id
├── Block (v-if 分支)
│   └── 当前分支的 Block
└── Block (v-for)
    └── 带 key 的 Fragment Block
```

- **Block**：有动态子节点的 VNode，维护一个 `dynamicChildren` 数组
- **diff 时**：不遍历 children 树，直接遍历 `dynamicChildren` 数组
- 嵌套的 Block 形成树状结构

## 深入：v-once / v-memo 进一步优化

```vue
<!-- v-once：只渲染一次，后续跳过 -->
<p v-once>{{ expensiveComputation() }}</p>

<!-- v-memo：条件缓存 (3.2+) -->
<div v-memo="[item.id === selected]">
  <p>{{ item.name }}</p>
</div>
<!-- 只有当 item.id === selected 变化时才重新渲染该节点 -->
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Virtual-DOM|虚拟 DOM]]
- [[20-软件开发/01-前端开发/01-Vue/11-性能优化/Performance|性能优化]]
