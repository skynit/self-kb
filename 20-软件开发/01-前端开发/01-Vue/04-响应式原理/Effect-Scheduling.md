---
tags:
  - vue
  - reactivity
  - scheduler
created: 2026-05-31
---

# 副作用调度

## 速查

| 概念 | 说明 |
|------|------|
| `ReactiveEffect` | 响应式副作用类，持有 `fn` 和 `scheduler` |
| `effect(fn)` | 创建副作用实例并立即执行 |
| `watch(source, cb)` | 基于 `ReactiveEffect` + 调度器实现 |
| `watchEffect(fn)` | `effect` 的用户级 API |
| 微任务队列 | 同一轮数据变化批量合并更新 |
| `queueJob` | 将组件更新任务推入微任务队列 |

## 批量更新机制

```
count.value++    →  trigger → enqueueUpdate(effect)
count.value++    →  trigger → enqueueUpdate(effect)  // 同一个 effect，去重
count.value++    →  trigger → enqueueUpdate(effect)

// 当前宏任务结束后
// 微任务队列执行
  → effect.run()  // 只执行一次！
  → 组件重新渲染
```

> 这就是为什么连续修改多个响应式数据，组件只更新一次。

## watch 的实现原理（简化）

```js
function watch(source, cb, options = {}) {
  let getter
  if (isRef(source)) {
    getter = () => source.value
  } else if (isReactive(source)) {
    getter = () => source
    options.deep = true
  } else if (isFunction(source)) {
    getter = source
  }

  let oldValue

  const job = () => {
    const newValue = effect.run()
    if (hasChanged(newValue, oldValue)) {
      cb(newValue, oldValue)
      oldValue = newValue
    }
  }

  const effect = new ReactiveEffect(getter, {
    scheduler: () => {
      if (options.flush === 'post') {
        queuePostRenderEffect(job)
      } else {
        job()
      }
    }
  })

  if (options.immediate) {
    job()
  } else {
    oldValue = effect.run()
  }
}
```

## 深入：调度队列

```
queueJob(job)
  ↓
queue 数组推入 job
  ↓（如果队列为空，启动微任务）
Promise.resolve().then(flushJobs)
  ↓
flushJobs()
  ├── 排队列（父组件先于子组件）
  ├── 遍历执行 queue
  └── 执行 post 队列（watchPostEffect 等）
```

## 深入：effect 的清理

```js
// effect 在重新执行前会清理旧的依赖
// 步骤：
// 1. 遍历 effect.deps（每个 Set）
// 2. 从每个 Set 中删除当前 effect
// 3. 清空 effect.deps
// 4. 重新执行 fn，收集新的依赖
```

> 这确保了条件分支切换时，不再需要的依赖会被正确清理。

```js
watchEffect(() => {
  if (flag.value) {
    console.log(count.value)  // flag 为 true 时依赖 count
  } else {
    console.log(name.value)   // flag 为 false 时依赖 name
  }
})
// flag 变化时，count/name 的依赖会正确切换
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Reactivity-In-Depth|响应式系统]]
- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Computed-and-Watch|computed 与 watch 用法]]
