---
tags:
  - vue
  - index
created: 2026-05-31
---

# Vue 3 知识图谱

> Vue 3 全栈体系速查 + 原理笔记的导航中心

## 核心

- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Vue3-Overview|Vue 3 总览]] — 版本特性、Composition vs Options
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Template-Syntax|模板语法]] — 插值、指令、表达式
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Data-Binding|数据绑定]] — 单向/双向绑定机制
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Computed-and-Watch|计算属性与侦听器]] — 缓存策略与副作用管理
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Class-and-Style|Class 与 Style 绑定]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Conditional-and-List|条件渲染与列表渲染]] — v-if/v-show、v-for/key
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Event-Handling|事件处理]] — 修饰符、自定义事件
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Form-Bindings|表单绑定]] — v-model 本质与自定义

## 组件体系

- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Component-Basics|组件基础]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]] — 类型校验、单向数据流
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Slots|插槽]] — 默认/具名/作用域插槽
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Provide-Inject|Provide / Inject]] — 跨层级通信
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Lifecycle-Hooks|生命周期钩子]] — 顺序、使用场景
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Async-Components|异步组件]] — Suspense 与懒加载

## Composition API

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Setup|setup 函数与 script setup]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Ref-and-Reactive|ref 与 reactive]] — 响应式核心
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Computed-Watch|computed / watch / watchEffect]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables 模式]] — 逻辑复用与设计

## 响应式原理

- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Reactivity-In-Depth|响应式系统深入]] — Proxy 拦截、依赖收集
- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Effect-Scheduling|副作用调度]] — effect、scheduler、队列

## 虚拟 DOM 与编译优化

- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Virtual-DOM|虚拟 DOM diff 算法]]
- [[20-软件开发/01-前端开发/01-Vue/05-虚拟DOM与编译/Compiler-Optimizations|编译器优化]] — 静态提升、Patch Flags、Block Tree

## 生态

- [[20-软件开发/01-前端开发/01-Vue/06-Vue-Router/Router-Essentials|Vue Router 4 核心]]
- [[20-软件开发/01-前端开发/01-Vue/06-Vue-Router/Navigation-Guards|导航守卫]]
- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Essentials|Pinia 状态管理]]
- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Advanced|Pinia 进阶]] — Plugins、SSR、组合式用法
- [[20-软件开发/01-前端开发/01-Vue/08-Vite/Vite-Essentials|Vite 构建工具]]

## CSS 样式

- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/Scoped-CSS|Scoped CSS 与样式隔离]] — :deep / :slotted / :global
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/CSS-Modules|CSS Modules]] — $style 绑定与类名 hash
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/v-bind-in-CSS|v-bind in CSS]] — 响应式 CSS 变量（Vue 3.2+）
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/预处理器|预处理器与 PostCSS]] — Sass、Less、Tailwind 集成
- [[20-软件开发/01-前端开发/01-Vue/13-CSS样式/Transition-Animation|过渡与动画]] — Transition / TransitionGroup / FLIP

## 工程化

- [[20-软件开发/01-前端开发/01-Vue/09-TypeScript/TS-Integration|TypeScript 集成]]
- [[20-软件开发/01-前端开发/01-Vue/10-测试/Testing-Guide|测试指南]] — Vitest + Vue Test Utils
- [[20-软件开发/01-前端开发/01-Vue/11-性能优化/Performance|性能优化清单]]
- [[20-软件开发/01-前端开发/01-Vue/12-最佳实践/Project-Patterns|项目模式与约定]]

## 速查索引

| 主题     | 核心 API / 概念                                                  |
| ------ | ------------------------------------------------------------ |
| 响应式    | `ref` `reactive` `computed` `watch` `watchEffect`            |
| 组件     | `defineProps` `defineEmits` `defineExpose` `defineSlots`     |
| 模板     | `v-if` `v-for` `v-bind` `v-on` `v-model` `v-slot`            |
| 生命周期   | `onMounted` `onUpdated` `onUnmounted` `onErrorCaptured`      |
| 依赖注入   | `provide` `inject`                                           |
| 路由     | `useRouter` `useRoute` `definePage`                          |
| 状态管理   | `defineStore` `storeToRefs`                                  |
| CSS 样式 | `scoped` `:deep()` `v-bind` CSS `<Transition>` `CSS Modules` |
