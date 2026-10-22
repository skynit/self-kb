---
title: Service Worker 介绍与应用
created: 2026-10-12
updated: 2026-10-12
type: concept
tags:
  - frontend
  - service-worker
  - PWA
  - offline
---

# Service Worker 介绍与应用

## 核心结论

Service Worker 是浏览器在页面主线程之外运行的事件驱动脚本。它位于网页和网络之间，可以拦截请求并决定访问网络、读取缓存或返回降级内容，是离线 Web 应用、PWA、弱网优化、后台同步和消息推送的基础能力。

它不是普通的长期后台线程：浏览器可以随时终止它，因此不能依赖全局变量保存持久状态；它也不能直接访问 DOM，需要通过 `postMessage()` 与页面通信。

可以把它理解为以下模型：

```text
页面请求
   ↓
Service Worker 的 fetch 事件
   ├─ 访问网络
   ├─ 查询 Cache Storage
   ├─ 更新缓存
   └─ 返回离线降级内容
```

## 使用条件

- 生产环境必须使用 HTTPS，`localhost` 通常可用于本地开发。
- 页面和 Service Worker 必须符合同源策略。
- Service Worker 的文件位置决定默认作用域。
- 浏览器不支持或注册失败时，网站仍应具备基本在线访问能力。

例如：

```text
/sw.js      默认可控制整个站点
/app/sw.js  默认只能控制 /app/ 及其子路径
```

通常将 `sw.js` 放在网站根目录。确实需要扩大作用域时，可以由服务器配置 `Service-Worker-Allowed` 响应头，但清晰的目录和作用域设计更容易维护。

## 生命周期

Service Worker 的主要生命周期是：

```text
注册
  → installing
  → installed / waiting
  → activating
  → activated
  → redundant
```

| 阶段 | 说明 |
| --- | --- |
| `installing` | 下载并执行新脚本，通常在此预缓存应用外壳 |
| `waiting` | 新版本安装成功，等待旧版本退出控制 |
| `activating` | 清理旧缓存、迁移数据或准备新版本 |
| `activated` | 可以处理 `fetch`、`message`、`push` 等事件 |
| `redundant` | 安装失败或已被其他版本替代 |

首次注册后，当前已经打开的页面通常不会立刻受控，需要刷新或重新打开。`clients.claim()` 可以让刚激活的版本尽快接管页面；`skipWaiting()` 可以跳过等待阶段，但可能让旧页面突然由新逻辑处理请求，需要配合明确的更新提示和刷新流程使用。

## 注册方式

在页面入口注册：

```js
if ("serviceWorker" in navigator) {
  window.addEventListener("load", async () => {
    try {
      const registration = await navigator.serviceWorker.register("/sw.js", {
        scope: "/",
      });

      console.log("Service Worker scope:", registration.scope);
    } catch (error) {
      console.error("Service Worker registration failed:", error);
    }
  });
}
```

注册脚本应作为渐进增强能力。不能因为注册失败就阻止主页面初始化。

## 基础实现

下面的 `sw.js` 演示三个基本动作：安装时预缓存、激活时清理旧缓存、请求时按资源类型选择策略。

```js
const VERSION = "v1";
const STATIC_CACHE = `static-${VERSION}`;
const PAGE_CACHE = `page-${VERSION}`;
const APP_SHELL = ["/", "/index.html", "/styles.css", "/app.js", "/offline.html"];

self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open(STATIC_CACHE).then((cache) => cache.addAll(APP_SHELL)),
  );
});

self.addEventListener("activate", (event) => {
  const activeCaches = new Set([STATIC_CACHE, PAGE_CACHE]);

  event.waitUntil(
    caches
      .keys()
      .then((names) =>
        Promise.all(
          names
            .filter((name) => !activeCaches.has(name))
            .map((name) => caches.delete(name)),
        ),
      )
      .then(() => self.clients.claim()),
  );
});

self.addEventListener("fetch", (event) => {
  const request = event.request;

  if (request.method !== "GET") {
    return;
  }

  if (request.mode === "navigate") {
    event.respondWith(networkFirst(request, PAGE_CACHE, "/offline.html"));
    return;
  }

  if (new URL(request.url).origin === self.location.origin) {
    event.respondWith(cacheFirst(request, STATIC_CACHE));
  }
});

async function cacheFirst(request, cacheName) {
  const cached = await caches.match(request);
  if (cached) {
    return cached;
  }

  const response = await fetch(request);
  if (response.ok) {
    const cache = await caches.open(cacheName);
    await cache.put(request, response.clone());
  }
  return response;
}

async function networkFirst(request, cacheName, fallbackUrl) {
  try {
    const response = await fetch(request);
    if (response.ok) {
      const cache = await caches.open(cacheName);
      await cache.put(request, response.clone());
    }
    return response;
  } catch {
    return (await caches.match(request)) ?? caches.match(fallbackUrl);
  }
}
```

响应体通常只能读取一次，因此同一个响应既要返回给页面、又要写入缓存时，需要使用 `response.clone()`。

## 缓存策略

缓存策略应根据资源性质选择，不能对所有请求统一使用“缓存优先”。

| 策略 | 执行方式 | 适用内容 | 主要风险 |
| --- | --- | --- | --- |
| Cache First | 先读缓存，未命中再请求网络 | 带内容哈希的 JS、CSS、字体、图标 | 资源可能长期不更新 |
| Network First | 先请求网络，失败后读缓存 | HTML、需要保持新鲜的接口 | 弱网下等待时间较长 |
| Stale While Revalidate | 立即返回缓存，同时后台更新 | 图片、文章列表、非关键数据 | 当前请求可能拿到旧数据 |
| Cache Only | 只读缓存 | 安装时确定的固定离线资源 | 缓存缺失时直接失败 |
| Network Only | 始终访问网络 | 登录、支付、写操作、强实时数据 | 离线时不可用 |

### Stale While Revalidate 示例

```js
async function staleWhileRevalidate(request, cacheName) {
  const cache = await caches.open(cacheName);
  const cached = await cache.match(request);

  const update = fetch(request).then((response) => {
    if (response.ok) {
      cache.put(request, response.clone());
    }
    return response;
  });

  return cached ?? update;
}
```

如果把后台更新放进 `fetch` 事件，生产代码可配合 `event.waitUntil()`，让浏览器知道仍有缓存写入任务需要完成。

## 典型应用方式

### 离线应用外壳

在 `install` 阶段缓存首页、基础样式、JavaScript、图标和离线页面。断网后仍可显示应用框架，并根据本地数据提供有限功能。

### 弱网与重复访问优化

对带哈希的静态资源使用 Cache First，对图片或普通列表使用 Stale While Revalidate，减少重复下载和等待时间。

### 离线内容阅读

将已经访问过的文章、文档或商品页面保存到缓存。Cache Storage 适合保存请求和响应；需要查询、排序和修改的结构化业务数据更适合保存到 IndexedDB。

### 请求路由

根据请求目标、URL 和方法选择不同策略：

```js
const url = new URL(event.request.url);

if (event.request.mode === "navigate") {
  // 页面导航
} else if (url.pathname.startsWith("/api/")) {
  // API 请求
} else if (event.request.destination === "image") {
  // 图片资源
}
```

### 后台同步

在浏览器支持的情况下，可以将离线期间产生的操作写入 IndexedDB，恢复网络后再通过 Background Sync 提交。提交接口必须考虑重复发送，并通过业务 ID、幂等键或服务端状态检查保证幂等。

### 消息推送

Service Worker 可以在页面未打开时接收 Push API 事件，再调用 Notifications API 展示系统通知。推送必须经过用户授权，并提供关闭订阅和降级路径。

## 更新方案

修改缓存名称可以触发最简单的缓存版本升级：

```js
const VERSION = "v2";
```

生产环境建议使用以下流程：

1. 构建产物使用内容哈希文件名。
2. 构建时自动生成预缓存清单。
3. 页面检测 `registration.waiting`。
4. 提示用户发现新版本。
5. 用户确认后通知等待中的 Service Worker 执行 `skipWaiting()`。
6. 页面监听 `controllerchange`，由页面决定何时刷新。

项目规模增大后，可以使用 Workbox 管理预缓存清单、请求路由、缓存过期和更新流程，避免手工 Service Worker 逐渐变成难以验证的请求分发器。

## 常见问题

### 页面没有被控制

- 检查页面是否位于注册作用域内。
- 首次注册后刷新页面。
- 查看 Service Worker 是否安装失败或停在 `waiting`。
- 确认生产环境使用 HTTPS。

### 更新代码后页面仍是旧版本

- 新 Worker 可能正在等待旧页面退出。
- HTML 被 Network First 之外的策略长期缓存。
- 缓存名称未更新，旧资源没有被清理。
- 构建产物没有内容哈希，URL 不变导致缓存无法区分版本。

### 离线页面没有生效

- `/offline.html` 没有在安装阶段成功缓存。
- `install` 中的 `cache.addAll()` 因任一资源失败而整体失败。
- 导航请求没有通过 `event.respondWith()` 返回降级响应。

### 接口数据异常或用户数据串用

- 不要无条件缓存鉴权接口和个性化响应。
- 缓存键可能需要包含完整查询参数。
- 用户退出时需要清理与账号相关的本地数据。
- 支付、登录和写操作默认使用 Network Only，并单独设计离线队列。

### 缓存持续增长

Cache Storage 不会替业务自动完成版本和容量治理。需要设置缓存版本，并根据资源数量、时间或业务范围执行淘汰。大量结构化数据应使用 IndexedDB，而不是无限加入 Cache Storage。

### 跨域资源无法判断是否成功

`no-cors` 请求可能返回 opaque response，JavaScript 不能读取其状态和响应内容。跨域缓存需要明确评估 CORS、缓存更新和错误响应处理方式。

## 调试方法

在 Chromium 浏览器开发者工具中查看：

```text
Application → Service Workers
Application → Cache Storage
Network → Offline
```

重点验证：

- Service Worker 是否注册成功。
- 当前页面是否已被控制。
- 新版本是否处于 `waiting`。
- 请求来自网络、内存缓存还是 Service Worker。
- Cache Storage 是否包含预期资源。
- 离线时导航、静态资源和降级页面是否正常。
- 多标签页打开时，新旧版本切换是否符合预期。

开发阶段可以清理注册和缓存：

```js
const registration = await navigator.serviceWorker.getRegistration();
await registration?.unregister();

const names = await caches.keys();
await Promise.all(names.map((name) => caches.delete(name)));
```

## 设计原则

1. 把 Service Worker 作为渐进增强能力，不能让不支持它的浏览器无法使用网站。
2. 根据资源的新鲜度、敏感性和失败代价分别选择缓存策略。
3. 把缓存失效、版本更新和存储清理视为核心功能，而不是后续补丁。
4. 不缓存不明确的鉴权响应、写请求和敏感数据。
5. 离线提交必须考虑顺序、冲突、重试和幂等。
6. 先实现“离线页面 + 静态资源缓存”，验证更新流程后再增加复杂能力。

## 参考资料

- [MDN：Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
- [MDN：Using Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)
- [web.dev：The Service Worker Lifecycle](https://web.dev/articles/service-worker-lifecycle)
- [W3C Service Workers 规范](https://w3c.github.io/ServiceWorker/)

