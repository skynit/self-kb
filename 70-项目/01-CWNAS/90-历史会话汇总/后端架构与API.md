---
date: 2026-07-14
tags: [opencode, backend, api, gin, nginx, 认证]
category: backend
---

# 后端架构与 API（畅网 AiNAS 项目）

> 来源：OpenCode 历史会话 | ~18 条

## Agent-rag 调用排查

Postman 正常但项目代码调用失败 → 对比排查：
1. **请求头**：Content-Type、Authorization、User-Agent 是否一致
2. **代理设置**：HTTP_PROXY/HTTPS_PROXY 环境变量
3. **请求体编码**：JSON 格式、嵌套结构
4. **超时/重试**：HTTP 客户端超时配置
5. **URL 拼接**：末尾斜杠、路径参数

## 用户认证

- 从 `ctx` 获取 `user_id`
- 接口路径修改时需在路由注册层面处理
- 重启后账号密码无法登录：
  - session 存储丢失（内存存储重启清空）
  - JWT secret 不一致
  - 数据库连接问题

## 限流机制

- 前后端限流实现方式
- 限流策略测试验证
- 可能的实现：token bucket / sliding window / 固定窗口

## nginx 反向代理

`getWikiContentUrl` 失败时检查：
- proxy_pass 配置是否正确
- 后端服务是否运行
- CORS 头是否被正确转发
- nginx 错误日志 `error.log`

## i18n 多语言

- 站点数据与国际化配置查找
- vue-i18n 用法
- 语言文件位置和组织

## account 模块

- 查找 account 路由（route registration）
- 查找 account model/struct 定义
- 添加 `enable` 字段控制账户启用/禁用状态
- 从 ctx 获取 user_id 时修改接口路径

## API 路由追踪

理解项目请求路由注册与派发流程：
- Gin 框架路由注册机制
- 中间件链
- 路由分组（group）

## 畅网 AiNAS 文档 API

- 响应格式：JSON
- 接口文件：`cw-smb-api.json`
- 符合 Postman Collection v2.x 格式
- 搜索接口返回 `file_token` 用于后续文件访问

## CORS 配置

```go
// Gin CORS 中间件
r.Use(cors.New(cors.Config{
    AllowOrigins: []string{"*"},
    AllowMethods: []string{"GET", "POST", "PUT", "DELETE"},
    AllowHeaders: []string{"Content-Type", "Authorization"},
}))
```

## 后端架构探索

- 后端 API 结构
- 后端架构与分层
- 搜索接口返回格式
- file_token 使用说明
