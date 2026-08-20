---
tags:
  - vue
  - testing
created: 2026-05-31
---

# 测试指南

## 速查

### 工具栈

| 工具 | 用途 |
|------|------|
| **Vitest** | 测试运行器（Vite 原生，速度快） |
| **Vue Test Utils** | Vue 组件测试工具库 |
| **@vue/test-utils** | 挂载、交互、断言 |
| **MSW** | Mock Service Worker，API mock |
| **Playwright** | E2E 测试 |

### Vitest 配置

```ts
// vitest.config.ts
import { defineConfig } from 'vitest/config'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: ['./tests/setup.ts']
  }
})
```

### 组件测试

```ts
// tests/Counter.spec.ts
import { describe, it, expect } from 'vitest'
import { mount } from '@vue/test-utils'
import Counter from '@/components/Counter.vue'

describe('Counter', () => {
  it('renders correctly', () => {
    const wrapper = mount(Counter, {
      props: { initialCount: 5 }
    })
    expect(wrapper.text()).toContain('5')
  })

  it('increments on click', async () => {
    const wrapper = mount(Counter)
    await wrapper.find('button').trigger('click')
    expect(wrapper.text()).toContain('1')
  })

  it('emits event', async () => {
    const wrapper = mount(Counter)
    await wrapper.find('button').trigger('click')
    expect(wrapper.emitted('update')).toHaveLength(1)
    expect(wrapper.emitted('update')![0]).toEqual([1])
  })
})
```

### 常用 API

```ts
// 挂载
const wrapper = mount(Component, {
  props: { msg: 'Hello' },
  slots: { default: '<p>Slot content</p>' },
  global: {
    plugins: [router, pinia],
    stubs: { MyIcon: true }
  }
})

// 查询
wrapper.find('.class')
wrapper.findAll('button')
wrapper.get('[data-testid="input"]')  // 找不到则抛错

// 交互
await wrapper.find('button').trigger('click')
await wrapper.find('input').setValue('hello')
await wrapper.find('input').trigger('input')

// 断言
expect(wrapper.text()).toContain('Hello')
expect(wrapper.find('.error').exists()).toBe(true)
expect(wrapper.emitted('submit')).toBeTruthy()
```

### Store 测试

```ts
import { setActivePinia, createPinia } from 'pinia'
import { useCounterStore } from '@/stores/counter'

describe('CounterStore', () => {
  beforeEach(() => {
    setActivePinia(createPinia()) // 每次测试重置
  })

  it('increments', () => {
    const store = useCounterStore()
    expect(store.count).toBe(0)
    store.increment()
    expect(store.count).toBe(1)
  })
})
```

## 深入：测试策略

```
E2E (Playwright)
  ↑ 少量关键路径
组件测试 (Vitest + Vue Test Utils)
  ↑ 覆盖交互逻辑
单元测试 (Vitest)
  ↑ 覆盖工具函数、composables、store
```

| 层级 | 速度 | 可靠性 | 覆盖率 |
|------|------|--------|--------|
| 单元测试 | 最快 | 高 | 逻辑函数 |
| 组件测试 | 快 | 高 | UI 交互 |
| E2E | 慢 | 最高 | 完整流程 |

## 深入：测试最佳实践

1. **测试行为，不测实现** — 用 `wrapper.text()` 而非检查内部状态
2. **用 data-testid** — 比 `.class` 选择器更稳定
3. **mock API，不 mock 组件内部** — 用 MSW mock 请求
4. **每个测试独立** — `beforeEach` 重置状态
5. **避免 snapshot 测试** — 维护成本高，容易失效

## 相关

- [[20-软件开发/01-前端开发/01-Vue/03-Composition-API/Composables|Composables]]
- [[20-软件开发/01-前端开发/01-Vue/07-Pinia/Pinia-Essentials|Pinia]]
