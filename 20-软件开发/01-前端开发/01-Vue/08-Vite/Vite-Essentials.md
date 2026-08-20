---
tags:
  - vue
  - vite
  - tooling
created: 2026-05-31
---

# Vite 构建工具

## 速查

### 为什么 Vite 快

| 环节 | Webpack | Vite |
|------|---------|------|
| 开发启动 | 先打包所有模块 | 按需编译（ESM 原生加载） |
| 热更新 | 重新打包受影响的链路 | 只更新变化的模块 |
| 生产构建 | Webpack 自身 | Rollup（tree-shaking 更好） |

### 创建项目

```bash
npm create vite@latest my-vue-app -- --template vue
npm create vite@latest my-vue-ts -- --template vue-ts
```

### 配置文件

```js
// vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  },
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true
      }
    }
  },
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router', 'pinia']
        }
      }
    }
  }
})
```

### 环境变量

```bash
# .env              → 所有环境
# .env.development  → 开发环境
# .env.production   → 生产环境

VITE_API_BASE=/api
VITE_APP_TITLE=My App
```

```js
// 使用
console.log(import.meta.env.VITE_API_BASE)
console.log(import.meta.env.MODE)
console.log(import.meta.env.PROD)
console.log(import.meta.env.DEV)
```

> 只有 `VITE_` 前缀的变量会暴露给客户端。

## 深入：ESM 与 HMR 原理

```
浏览器请求 /main.js
  ↓
Vite dev server 拦截
  ↓
编译 .vue 文件为 JS
  ↓
返回 ESM 模块（保持 import/export）
  ↓
浏览器原生 import 执行
  ↓（遇到新的 import）
再次请求 Vite server
  ↓
按需编译，缓存
```

### HMR（热模块替换）

```
文件变化
  ↓
Vite server 检测
  ↓
WebSocket 推送更新消息到客户端
  ↓
客户端 HMR runtime 判断更新类型
  ├── .vue 组件 → 精确替换（不刷新页面）
  ├── CSS → 即时替换
  └── 普通 JS → 执行 accept 回调或冒泡
```

## 深入：常用插件

| 插件 | 用途 |
|------|------|
| `@vitejs/plugin-vue` | Vue 3 SFC 支持 |
| `@vitejs/plugin-vue-jsx` | JSX/TSX 支持 |
| `vite-plugin-svg-icons` | SVG 图标 |
| `unplugin-auto-import` | 自动导入 API |
| `unplugin-vue-components` | 组件自动注册 |
| `vite-plugin-mock` | Mock 数据 |

```js
// 自动导入示例
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { NaiveUiResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  plugins: [
    AutoImport({
      imports: ['vue', 'vue-router', 'pinia'],
      dts: 'src/auto-imports.d.ts'
    }),
    Components({
      resolvers: [NaiveUiResolver()],
      dts: 'src/components.d.ts'
    })
  ]
})
```

## 相关

- [[20-软件开发/01-前端开发/01-Vue/09-TypeScript/TS-Integration|TypeScript 集成]]
- [[20-软件开发/01-前端开发/01-Vue/11-性能优化/Performance|性能优化]]
