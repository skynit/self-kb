---
tags:
  - vue
  - patterns
created: 2026-05-31
---

# 项目模式与约定

## 推荐项目结构

```
src/
├── assets/              # 静态资源（图片、字体）
├── components/          # 通用组件（无业务逻辑）
│   ├── ui/              # 基础 UI 组件
│   │   ├── BaseButton.vue
│   │   ├── BaseInput.vue
│   │   └── BaseModal.vue
│   └── layout/          # 布局组件
│       ├── AppHeader.vue
│       ├── AppSidebar.vue
│       └── AppFooter.vue
├── composables/         # 组合式函数
│   ├── useApi.ts
│   ├── useAuth.ts
│   └── useLocalStorage.ts
├── features/            # 功能模块
│   ├── auth/
│   │   ├── components/
│   │   │   ├── LoginForm.vue
│   │   │   └── RegisterForm.vue
│   │   ├── composables/
│   │   │   └── useAuth.ts
│   │   └── stores/
│   │       └── auth.ts
│   └── dashboard/
│       ├── components/
│       └── views/
├── router/              # 路由配置
│   └── index.ts
├── stores/              # Pinia stores
├── types/               # TypeScript 类型定义
├── utils/               # 纯工具函数（无 Vue 依赖）
├── views/               # 页面级组件
│   ├── HomeView.vue
│   └── NotFound.vue
├── App.vue
└── main.ts
```

## 命名约定

| 类型 | 规则 | 示例 |
|------|------|------|
| 组件文件 | PascalCase | `UserProfile.vue` |
| 组合函数 | `use` 前缀 | `useAuth.ts` |
| Store | `use` + `Store` 后缀 | `useAuthStore` |
| 工具函数 | camelCase | `formatDate.ts` |
| 类型文件 | camelCase | `userTypes.ts` |
| 测试文件 | 原名 + `.spec` | `UserProfile.spec.ts` |

## 组件设计模式

### 1. 容器/展示组件

```
容器组件（Smart）          展示组件（Dumb）
├── 持有状态               ├── 接收 props
├── 调用 API               ├── 触发 events
├── 处理业务逻辑           ├── 无副作用
└── 例：UserList.vue       └── 例：UserCard.vue
```

### 2. 复合组件模式

```vue
<!-- Tabs.vue + Tab.vue -->
<Tabs v-model="activeTab">
  <Tab name="info">信息内容</Tab>
  <Tab name="settings">设置内容</Tab>
</Tabs>
<!-- 内部通过 provide/inject 通信 -->
```

### 3. Renderless 组件（无渲染组件）

```vue
<!-- 只提供逻辑，通过作用域插槽暴露 -->
<Fetch url="/api/users" #default="{ data, loading, error }">
  <div v-if="loading">Loading...</div>
  <div v-else-if="error">{{ error }}</div>
  <UserList v-else :users="data" />
</Fetch>
```

## 错误处理

```vue
<!-- 全局错误处理 -->
<script setup>
import { onErrorCaptured } from 'vue'

onErrorCaptured((err, instance, info) => {
  console.error('组件错误:', err)
  // 上报到错误监控
  reportError(err, info)
  return false // 阻止向上传播
})
</script>

<!-- 应用级 -->
// main.js
app.config.errorHandler = (err, instance, info) => {
  // 全局错误处理
}
```

## 代码风格

```vue
<!-- 推荐的 <script setup> 组织顺序 -->
<script setup lang="ts">
// 1. 导入
import { ref, computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useUserStore } from '@/stores/user'
import UserCard from '@/components/UserCard.vue'

// 2. Props & Emits
const props = defineProps<{ id: string }>()
const emit = defineEmits<{ update: [data: User] }>()

// 3. Store & Router
const userStore = useUserStore()
const router = useRouter()

// 4. 状态
const loading = ref(false)
const user = ref<User | null>(null)

// 5. 计算属性
const displayName = computed(() => user.value?.name ?? 'Unknown')

// 6. 方法
async function fetchUser() {
  loading.value = true
  try {
    user.value = await userStore.getUser(props.id)
  } finally {
    loading.value = false
  }
}

// 7. 生命周期
onMounted(fetchUser)
</script>
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables]]
- [[20-软件开发/01-前端开发/01-Vue/00-索引/Vue3-Knowledge-Map|知识图谱]]
