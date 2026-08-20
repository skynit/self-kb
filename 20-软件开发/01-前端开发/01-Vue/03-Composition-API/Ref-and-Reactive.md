---
tags:
  - vue
  - composition-api
  - reactivity
created: 2026-05-31
---

# ref 与 reactive

## 速查

### ref

```js
import { ref } from 'vue'

const count = ref(0)
console.log(count.value) // 0 — JS 中需要 .value
count.value++
// 模板中自动解包：{{ count }} — 不需要 .value
```

- 适用于**任意类型**（原始值、对象、数组）
- 模板中自动解包（unwrap）
- 传递给函数时保持响应性（因为传的是 ref 对象引用）

### reactive

```js
import { reactive } from 'vue'

const state = reactive({
  count: 0,
  nested: { deep: 1 }
})
console.log(state.count) // 0 — 不需要 .value
state.count++
state.nested.deep++      // 深层响应式
```

- 只适用于**对象类型**（Object、Array、Map、Set）
- 不需要 `.value`
- 不能替换整个对象（会丢失响应性）

## ref vs reactive

| 特性 | ref | reactive |
|------|-----|----------|
| 类型限制 | 任意 | 仅对象 |
| 访问方式 | `.value` | 直接访问 |
| 可替换整体 | `ref.value = newObj` | 不可以 |
| 解构 | 保持响应性 | 丢失响应性（需 toRefs） |
| 模板自动解包 | 是 | N/A（本身就是对象） |
| 推荐场景 | 原始值、需替换引用 | 复杂对象、不需替换引用 |

## 深入：reactive 的局限

```js
// 1. 不能替换引用
let state = reactive({ count: 0 })
state = reactive({ count: 1 }) // 原始 state 丢失响应性

// 2. 解构丢失响应性
const { count } = reactive({ count: 0 })
// count 此时是普通数字 0，不再是响应式

// 3. 原始值不能用 reactive
const count = reactive(0) // ❌ 不行

// 4. Map/Set 需要 reactive
const map = reactive(new Map())
map.set('key', 'value') // ✓ 响应式
```

## toRef / toRefs

```js
import { toRef, toRefs } from 'vue'

const state = reactive({ count: 0, name: 'Vue' })

// toRef — 单个属性转 ref
const count = toRef(state, 'count')
count.value++ // 同时修改 state.count

// toRefs — 所有属性转 ref（解构时保持响应性）
const { count, name } = toRefs(state)
// count.value → 0，且修改会同步到 state
```

### 典型场景：composable 返回值

```js
// 不好的写法 — 解构后丢失响应性
function useMouse() {
  const state = reactive({ x: 0, y: 0 })
  // ...
  return state
}
const { x, y } = useMouse() // x, y 不是响应式的 ❌

// 好的写法
function useMouse() {
  const state = reactive({ x: 0, y: 0 })
  // ...
  return toRefs(state) // 每个属性都是 ref
}
const { x, y } = useMouse() // x, y 是 ref ✓
```

## 深入：为什么推荐 ref 而非 reactive

Vue 官方团队（尤雨溪）推荐**默认使用 ref**，原因：

1. **一致性** — ref 适用于所有类型，不需要记忆哪种类型用什么
2. **可替换性** — `ref.value = newObj` 保持响应性
3. **解构安全** — 传参、解构、返回值都不丢失响应性
4. **原始值支持** — `ref(0)` 完全没问题

> 实践建议：默认用 `ref`，只有当你明确需要深层响应式对象且不想写 `.value` 时用 `reactive`。

## 相关

- [[20-软件开发/01-前端开发/01-Vue/04-响应式原理/Reactivity-In-Depth|响应式原理]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Computed-Watch|computed / watch]]
- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables]]
