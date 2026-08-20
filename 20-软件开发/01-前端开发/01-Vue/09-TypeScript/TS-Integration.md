---
tags:
  - vue
  - typescript
created: 2026-05-31
---

# TypeScript 集成

## 速查

### 基础配置

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "jsx": "preserve",
    "paths": { "@/*": ["./src/*"] },
    "types": ["vite/client"]
  },
  "include": ["src/**/*.ts", "src/**/*.vue"]
}
```

### 组件 Props 类型

```vue
<script setup lang="ts">
// 方式一：类型声明（推荐）
interface Props {
  title: string
  count?: number
  items: string[]
  status: 'active' | 'inactive'
}
const props = defineProps<Props>()

// 带默认值
const props = withDefaults(defineProps<Props>(), {
  count: 0,
  items: () => []
})

// 方式二：运行时声明
const props = defineProps({
  title: { type: String, required: true },
  count: { type: Number, default: 0 }
})
</script>
```

### Emits 类型

```vue
<script setup lang="ts">
// 类型声明
const emit = defineEmits<{
  submit: [data: FormData]
  delete: [id: number]
  change: [value: string]
}>()

// 触发时有完整类型提示
emit('submit', formData)
emit('delete', 42)
</script>
```

### ref / reactive 类型

```vue
<script setup lang="ts">
import { ref, reactive } from 'vue'

// ref 通常可以自动推导
const count = ref(0)         // Ref<number>
const name = ref('Vue')      // Ref<string>

// 复杂类型需要显式声明
interface User {
  id: number
  name: string
  email: string
}
const user = ref<User | null>(null)

// reactive 自动推导
const state = reactive({
  count: 0,
  items: [] as string[]  // 需要 as 断言空数组类型
})
</script>
```

### computed 类型

```vue
<script setup lang="ts">
// 自动推导返回类型
const doubled = computed(() => count.value * 2) // ComputedRef<number>

// 需要显式声明的场景
const filtered = computed<User[]>(() => {
  return users.value.filter(u => u.active)
})
</script>
```

### composables 类型

```ts
// composables/useApi.ts
interface UseApiReturn<T> {
  data: Ref<T | null>
  error: Ref<string | null>
  loading: Ref<boolean>
  fetch: () => Promise<void>
}

export function useApi<T>(url: string): UseApiReturn<T> {
  const data = ref<T | null>(null) as Ref<T | null>
  const error = ref<string | null>(null)
  const loading = ref(false)

  async function fetch() {
    loading.value = true
    try {
      const res = await window.fetch(url)
      data.value = await res.json()
    } catch (e) {
      error.value = (e as Error).message
    } finally {
      loading.value = false
    }
  }

  return { data, error, loading, fetch }
}
```

## 深入：模板中的类型检查

```vue
<template>
  <!-- Vue Language Tools (Volar) 会检查模板中的类型 -->
  <p>{{ user.name }}</p>  <!-- ✓ 类型正确 -->
  <p>{{ user.age }}</p>   <!-- ✗ 类型错误：User 没有 age -->
</template>
```

> 安装 **Vue - Official** (原 Volar) 扩展，获得模板中的完整类型检查。

## 深入：常见类型问题

### ref 解包类型

```ts
const count = ref(0)
// count 是 Ref<number>
// count.value 是 number
// 模板中自动解包：{{ count }} 直接是 number

// 深层 ref 不会自动解包
const nested = ref({ count: ref(0) })
// nested.value.count 是 Ref<number>，不会自动解包
```

### 组件实例类型

```vue
<script setup lang="ts">
import MyComponent from './MyComponent.vue'

// 获取组件实例类型
const compRef = ref<InstanceType<typeof MyComponent> | null>(null)

// 使用
onMounted(() => {
  compRef.value?.someMethod()  // 类型安全
})
</script>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Ref-and-Reactive|ref 与 reactive]]
- [[20-软件开发/01-前端开发/01-Vue/08-Vite/Vite-Essentials|Vite 配置]]
