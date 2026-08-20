---
tags:
  - vue
  - virtual-dom
  - internals
created: 2026-05-31
---

# 虚拟 DOM 与 diff 算法

## 速查

### VNode 结构

```js
// 编译产物
const vnode = {
  type: 'div',
  props: { id: 'app', class: 'container' },
  children: [
    { type: 'span', props: null, children: 'hello' },
    { type: VNodeFlags.COMPONENT, ... }
  ],
  shapeFlag: ShapeFlags.ELEMENT, // 类型标记
  patchFlag: PatchFlags.TEXT,     // 更新标记
  dynamicProps: ['class']         // 动态属性
}
```

### diff 核心流程

```
新旧 VNode 对比
  ├── 类型不同 → 销毁旧节点，创建新节点
  ├── 类型相同
  │   ├── 元素 → 对比 props 和 children
  │   └── 组件 → 对比 props，触发子组件更新
  └── children 对比
       ├── 文本 → 直接替换文本节点
       ├── 单个子节点 → 递归 patch
       └── 多个子节点 → diff 算法
```

## 双端对比算法

Vue 3 的 children diff 采用**双端 + 最长递增子序列**策略：

```
旧: [A, B, C, D, E, F]
新: [A, D, B, H, F]

步骤:
1. 从头部开始: A == A ✓, B ≠ D → 停止
2. 从尾部开始: F == F ✓, E ≠ H → 停止
3. 中间乱序部分: [B, C, D, E] → [D, B, H]
4. 构建 keyToNewIndex 映射
5. 计算最长递增子序列确定"不动节点"
6. 移动/新增/删除剩余节点
```

### 最长递增子序列（LIS）

```js
// 核心目的：找出哪些节点不需要移动
// 旧序列中，相对顺序已经正确的节点保持不动

旧: [B, C, D, E]  → newIndex: [2, 0, -1, 3]
                   有效序列: [1(B→0), 3(D→3)]
                   LIS: [1, 3] → B, D 不需要移动
```

## 深入：为什么 Vue 3 比 Vue 2 diff 更快

| 优化 | Vue 2 | Vue 3 |
|------|-------|-------|
| 静态节点 | 无 | 静态提升，完全跳过 diff |
| Block Tree | 无 | 只遍历有动态标记的节点 |
| Patch Flags | 无 | 精确知道哪些属性变化 |
| 事件缓存 | 无 | 内联事件函数缓存 |

## 深入：渲染流程总览

```
模板 Template
  ↓ 编译器
render 函数 + 静态分析标记
  ↓ 运行时调用
VNode 树
  ↓ patch
真实 DOM
```

```
数据变化
  ↓ reactive trigger
组件重新执行 render
  ↓ 生成新 VNode 树
新旧 VNode diff
  ↓ patch flags 指导
最小化 DOM 更新
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Compiler-Optimizations|编译器优化]]
- [[20-软件开发/01-前端开发/01-Vue/11-性能优化/Performance|性能优化]]
