---
tags:
  - vue
  - forms
created: 2026-05-31
---

# 表单绑定

## 速查

### 文本输入

```vue
<input v-model="text" />
<textarea v-model="text"></textarea>
```

### 复选框

```vue
<!-- 单个 → boolean -->
<input type="checkbox" v-model="checked" />

<!-- 多个 → 数组 -->
<input type="checkbox" v-model="checkedNames" value="Jack" />
<input type="checkbox" v-model="checkedNames" value="John" />
```

### 单选框

```vue
<input type="radio" v-model="picked" value="One" />
<input type="radio" v-model="picked" value="Two" />
```

### 下拉框

```vue
<!-- 单选 -->
<select v-model="selected">
  <option disabled value="">请选择</option>
  <option>A</option>
  <option>B</option>
</select>

<!-- 多选 -->
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
</select>
```

## 深入：表单验证模式

### 模式一：computed 校验

```vue
<script setup>
const email = ref('')
const emailError = computed(() => {
  if (!email.value) return '邮箱不能为空'
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) return '邮箱格式错误'
  return ''
})
</script>

<template>
  <input v-model="email" :class="{ error: emailError }" />
  <span class="error-msg">{{ emailError }}</span>
</template>
```

### 模式二：watch + 防抖

```vue
<script setup>
const search = ref('')
const results = ref([])

watch(search, debounce(async (val) => {
  results.value = await api.search(val)
}, 300))
</script>
```

### 模式三：vee-validate / formkit

大型项目推荐使用成熟的表单库：
- **vee-validate** — 基于 Composition API，声明式校验
- **formkit** — 表单框架，自带校验、多步骤表单

## 相关

- [[20-软件开发/01-前端开发/01-Vue/01-核心概念/Data-Binding|数据绑定]]
- [[20-软件开发/01-前端开发/01-Vue/02-组件体系/Props-and-Emits|Props 与 Emits]]
