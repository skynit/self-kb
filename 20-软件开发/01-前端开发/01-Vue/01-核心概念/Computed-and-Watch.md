---
tags:
  - vue
  - computed
  - watch
created: 2026-05-31
---

# 计算属性与侦听器

## 速查

### computed

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('张')
const lastName = ref('三')

// 只读
const fullName = computed(() => firstName.value + lastName.value)

// 可写
const fullNameRW = computed({
  get: () => firstName.value + lastName.value,
  set: (val) => {
    firstName.value = val[0]
    lastName.value = val.slice(1)
  }
})
</script>
```

| 特性 | computed | method |
|------|----------|--------|
| 缓存 | 有（依赖不变则不重算） | 无（每次渲染都调用） |
| 返回值 | 返回 ref | 返回调用结果 |
| 副作用 | 不应有 | 可以有 |

### watch

```vue
<script setup>
import { ref, watch, watchEffect } from 'vue'

const count = ref(0)
const obj = ref({ nested: { count: 0 } })

// 侦听 ref
watch(count, (newVal, oldVal) => {
  console.log(`${oldVal} → ${newVal}`)
})

// 侦听 getter 函数（推荐侦听对象属性）
watch(
  () => obj.value.nested.count,
  (newVal, oldVal) => { /* ... */ }
)

// 侦听多个源
watch([count, () => obj.value.nested.count], ([newC, newN], [oldC, oldN]) => {
  // ...
})

// 深度侦听
watch(obj, (newVal) => { /* ... */ }, { deep: true })

// 立即执行
watch(count, (n, o) => { /* ... */ }, { immediate: true })

// 一次性侦听 (Vue 3.5+)
watch(count, (n, o) => { /* ... */ }, { once: true })

// watchEffect — 自动追踪依赖
watchEffect(() => {
  console.log(count.value) // 自动侦听 count
})
</script>
```

## watch vs watchEffect

| 特性 | watch | watchEffect |
|------|-------|-------------|
| 依赖声明 | 显式指定 | 自动收集 |
| 旧值访问 | 可以 | 不可以 |
| 惰性执行 | 默认惰性 | 立即执行 |
| 典型场景 | 需要新旧值对比、精确控制 | 副作用与多个响应式源联动 |

## 深入：computed 的缓存机制

```
computed(getter)
  ├── 首次访问 → 执行 getter → 缓存结果 → 标记 clean
  ├── 依赖未变 → 直接返回缓存
  └── 依赖变化 → 标记 dirty → 下次访问时重新执行 getter
```

`computed` 使用**惰性求值**策略：
1. 创建时不立即执行 getter
2. 首次被读取时执行，并收集依赖
3. 依赖变化时标记为 dirty，但不立即重算
4. 下次读取时发现 dirty 才重新计算

> 这就是为什么在模板中使用 computed 性能优于 method — 避免了每次渲染都重复执行。

## 深入：watch 的 flush 选项

```js
watch(source, callback, {
  flush: 'pre'  // 默认：在组件更新前触发
  // flush: 'post'  // 组件更新后触发
  // flush: 'sync'  // 同步触发（性能代价高）
})
```

| flush | 时机 | 场景 |
|-------|------|------|
| `pre` | DOM 更新前 | 获取更新前的 DOM 状态 |
| `post` | DOM 更新后 | 操作更新后的 DOM（如测量尺寸） |
| `sync` | 同步执行 | 需要严格顺序（少见） |

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Computed-Watch|Composition API 中的用法]]
- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Effect-Scheduling|副作用调度原理]]
