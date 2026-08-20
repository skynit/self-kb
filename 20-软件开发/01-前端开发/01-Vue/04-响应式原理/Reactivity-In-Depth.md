---
tags:
  - vue
  - reactivity
  - internals
created: 2026-05-31
---

# 响应式系统深入

## 速查：核心概念

| 概念 | 作用 |
|------|------|
| `reactive()` | 创建 Proxy 代理对象 |
| `ref()` | 包装原始值，内部用 reactive 处理对象 |
| `effect` | 追踪依赖的副作用函数 |
| `track()` | 依赖收集：记录"谁在读取" |
| `trigger()` | 触发更新：通知"数据变了" |
| `WeakMap → Map → Set` | 依赖存储结构 |

## Proxy 实现原理

```js
// 极简实现
function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      track(target, key)       // 依赖收集
      const result = Reflect.get(target, key, receiver)
      // 深层响应式：如果值是对象，递归代理
      if (typeof result === 'object' && result !== null) {
        return reactive(result)
      }
      return result
    },
    set(target, key, value, receiver) {
      const oldValue = target[key]
      const result = Reflect.set(target, key, value, receiver)
      if (oldValue !== value) {
        trigger(target, key)   // 触发更新
      }
      return result
    }
  })
}
```

## 依赖收集数据结构

```
WeakMap {
  target → Map {
    key → Set {
      effect1,
      effect2,
      ...
    }
  }
}
```

- **WeakMap**：以响应式对象为 key，对象被 GC 时自动清理
- **Map**：以属性 key 为 key，追踪哪些属性被读取
- **Set**：存储依赖该属性的所有 effect，自动去重

### track & trigger

```js
let activeEffect = null

function track(target, key) {
  if (!activeEffect) return // 没有正在执行的 effect，不需要收集

  let depsMap = targetMap.get(target)
  if (!depsMap) targetMap.set(target, (depsMap = new Map()))

  let deps = depsMap.get(key)
  if (!deps) depsMap.set(key, (deps = new Set()))

  deps.add(activeEffect)
  activeEffect.deps.push(deps) // 双向引用，便于清理
}

function trigger(target, key) {
  const depsMap = targetMap.get(target)
  if (!depsMap) return
  const deps = depsMap.get(key)
  if (!deps) return

  const effectsToRun = new Set(deps) // 复制一份避免无限循环
  effectsToRun.forEach(effect => effect.run())
}
```

## 深入：Vue 2 vs Vue 3 响应式对比

| | Vue 2 | Vue 3 |
|---|-------|-------|
| 实现 | `Object.defineProperty` | `Proxy` |
| 深度监听 | 递归遍历所有属性（初始化慢） | 惰性代理（按需） |
| 新增属性 | `Vue.set()` 必需 | 直接赋值即可 |
| 数组索引 | 不能检测 | 可以检测 |
| Map/Set | 不支持 | 支持 |
| 性能 | 初始化开销大 | 按需代理，性能更好 |

## 深入：ref 的内部实现

```js
function ref(value) {
  return new RefImpl(value)
}

class RefImpl {
  constructor(value) {
    this._value = isObject(value) ? reactive(value) : value
    this.__v_isRef = true // 标记为 ref
  }

  get value() {
    track(this, 'value')  // 收集依赖
    return this._value
  }

  set value(newVal) {
    if (newVal !== this._value) {
      this._value = isObject(newVal) ? reactive(newVal) : newVal
      trigger(this, 'value') // 触发更新
    }
  }
}
```

> `ref` 本质是用 `get/set` 包装了一个值，访问 `.value` 时触发 track，修改 `.value` 时触发 trigger。

## 相关

- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Effect-Scheduling|副作用调度]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Ref-and-Reactive|ref 与 reactive 用法]]
