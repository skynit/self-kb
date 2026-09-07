---
title: CWNAS 系统 Shell SSH 实现与验收
created: 2026-09-03
updated: 2026-09-03
type: project/engineering
tags:
  - CWNAS
  - SSH
  - RBAC
  - AppConfig
  - Windows
  - Linux
---

# CWNAS 系统 Shell SSH 实现与验收

## 当前结论

CWNAS 的系统 Shell SSH 与原 CLI SSH 是两个独立服务：原 CLI SSH 保持端口 `2222`，系统 Shell SSH 默认端口为 `22222`、默认不自动启动。当前配置迁移方案是 **AppConfig V7 直接升级到 V8**，V8 一次性补入完整的 `system_ssh_config`；不存在 V9 迁移。

最终代码已完成相关包测试和主程序构建，但 V7→V8、API 响应收紧及后续生命周期修改尚未重新部署到 Linux 或 Windows 真机。此前的双平台真机结果验证了系统 Shell SSH 的认证、PTY、会话和启停链路，但使用的是更早的配置版本实现，不能替代最终 V8 构建的部署验收。

## 验收流程

按以下顺序验收最终构建，避免把服务进程存活等同于功能成功：

1. 检查分支、工作区、`git diff --check` 和本次改动范围，只运行直接相关的包测试。
2. 构建目标平台产物，记录文件大小、格式和 SHA-256。
3. 确认目标架构、运行目录和磁盘空间，备份现有程序与配置后再替换。
4. 确认 CWNAS 主服务稳定、管理 API 端口可用、原 CLI SSH `2222` 未受影响。
5. 调用系统 Shell SSH API 完成 `stopped -> running -> stopped` 闭环。
6. 在 `running` 状态下建立真实 `80x24` PTY，会话内执行命令并校验实际系统身份。
7. 收尾确认连接数和会话数归零、`22222` 已释放、主服务和 `2222` 仍正常。

成功响应契约：

```json
{
  "code": 200,
  "status": "success"
}
```

`POST /ssh/start` 和 `POST /ssh/stop` 成功时不返回 `data`。`GET /ssh/status` 的 `data` 只包含：

```json
{
  "state": "stopped",
  "listen_address": "",
  "active_connections": 0,
  "active_sessions": 0
}
```

状态响应不包含 `last_error`；错误继续通过调用返回值、审计和服务日志表达。

## 生命周期与授权边界

### 应用上下文

系统 Shell SSH Server 在路由注册阶段创建并由路由层持有，因此必须接收应用级 `context.Context`：

```text
应用 serverCtx
  -> registerRouter(ctx)
  -> sshroutes.Register(group, ctx, ...)
  -> ctx.Done()
  -> server.Stop(context.Background())
```

不能在 routes 内使用 `context.Background()` 代替应用上下文，因为它的 `Done()` 永远不会关闭。关闭动作使用新的 context，是为了避免把已经取消的应用 context 直接传入清理流程；Server 自身的 `ShutdownTimeout` 负责限制等待时间。

### HTTP 与 SSH 两层 RBAC

`Register` 不再单独注入 `ssh.PermissionChecker`。`api.RoutGroup` 已实现 `HasPermission`，可直接传给 `ssh.NewNASAccess`。

两层检查不能合并：

- HTTP 中间件保护 `/ssh/start`、`/ssh/stop` 和 `/ssh/status`。
- 客户端直连 `22222` 不经过 HTTP 中间件，仍需 `NASAccess` 在认证和会话期间检查权限撤销。

审计调用使用 `systemaudit.LogAction(request, currentUser, action, target, err)`；该函数自行读取 `request.Context()`，不能额外传入 context。

## AppConfig V7 到 V8

V8 迁移只负责登记相邻版本变化，迁移引擎通过缺失字段合并补入完整的 `system_ssh_config`。迁移必须满足：

- 旧配置的 `schema_version` 从 `7` 更新到 `8`。
- 缺失的系统 SSH 配置获得默认端口 `22222` 和 `auto_start=false`。
- 原 CLI SSH 配置和端口保持不变。
- V7 配置中已经存在的系统 SSH 自定义值不得被默认值覆盖。
- 默认配置指纹按 V8 重新计算并登记，不能直接把变化后的 V7 哈希登记为合法值。

对应的启动错误：

```text
配置 "server_app_config" 的持久化默认值已改变但 schema 登记未更新
```

这类错误表示持久化默认结构发生变化，但 schema、相邻迁移或指纹登记没有同步；不能只替换报错中的哈希绕过迁移治理。

## 双平台真机观察

### Linux

较早构建已在 Linux AMD64 设备完成以下闭环：

- 主服务保持 `active/running`，重启计数为 0。
- 系统 SSH 从 stopped 启动并监听 `22222`。
- 管理员认证成功，真实 `80x24` PTY 打开系统 Shell。
- 会话结束后 stop 成功，连接数和会话数归零，端口释放。
- 原 CLI SSH `2222` 始终保持监听。

当标准 Docker 构建入口不可用时，曾在确认 ELF 架构、动态依赖和目标机 glibc 兼容后使用本机 Linux AMD64 Go 工具链构建。该偏差属于一次验收事实，不应自动成为后续发布流程的默认替代方案。

### Windows

较早构建已在 Windows AMD64 设备完成 API 启停、密码认证、PTY PowerShell 命令执行及收尾检查。可复用的排错结论包括：

- 通过 Windows OpenSSH 会话启动的 `Start-Process` 可能随 SSH 宿主作业结束；需要独立进程生命周期时，可使用 `Win32_Process.Create`。
- `script | ssh` 的自动输入不能可靠证明 Windows ConPTY 内命令已执行；协议级客户端直接申请 PTY、打开 Shell、发送命令并读取输出更可靠。
- 只有审计中的 `authentication=success` 和 `session_open=success` 不足以证明命令执行；还要匹配命令输出，并确认正常 `session_close`。

## 本地验证

最终 V8 迁移已经通过：

```bash
go test -count=1 ./cmd/server/appconfig
```

系统 SSH 相关包已经通过：

```bash
go test -count=1 ./pkg/ssh
```

主程序入口已经完成构建检查：

```bash
go build -o /tmp/cwnas-v8-config-check ./cmd/server
```

另有针对路由响应和注册链的定向测试与构建检查通过。没有运行全量测试。

## 与 FTP 生命周期的关系

SSH 的应用退出清理促使 FTP 生命周期也明确区分“管理员禁用”和“进程退出释放资源”。FTP 的具体约定见 [[70-项目/01-CWNAS/01-设计文档/FTP|CWNAS FTP 生命周期与优雅关闭]]。

## 证据边界

- 已证明：较早构建在 Linux 和 Windows 上的真实认证、PTY、Shell、启停和端口隔离；最终代码的相关包测试和主入口构建。
- 尚未证明：最终 V8 构建在真实 Linux/Windows 旧配置上的迁移、收紧后的 API JSON 形状、应用退出时 SSH/FTP 的真实优雅关闭。
- 已排除：明文密码、Token、Cookie、私有 IP、用户名、主机名和临时授权命令。

## 来源

- Codex 会话：`01a05663-f6c1-79d0-938b-47c5377fcc13`
- 代码范围：`cmd/server/appconfig`、`pkg/ssh`、`pkg/ssh/routes`、`pkg/fileserver/ftp`

