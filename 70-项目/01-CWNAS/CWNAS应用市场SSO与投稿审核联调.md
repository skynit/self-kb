---
title: CWNAS 应用市场 SSO 与投稿审核联调
created: 2026-08-31
updated: 2026-08-31
type: project/engineering
tags:
  - CWNAS
  - AppStore
  - SSO
  - Casdoor
  - Vue
  - Go
  - MySQL
---

# CWNAS 应用市场 SSO 与投稿审核联调

## 结论

CWNAS 应用市场的普通用户 SSO、用户投稿、管理员审核和审核后上架已经在隔离环境中完成真实前后端联调：外部 Casdoor 授权成功回调，本地 HttpOnly cookie 会话生效；带图标投稿在管理员批准前不进入公共目录，批准后同时进入公共 Catalog 与 CW 读取模型。后续又在本机真实 CWNAS 运行实例上完成应用市场源列表、详情、Compose、图标和下载验收；安装阶段因本机 Docker daemon 不可用而终止。

普通用户和管理员身份保持两条独立链路：

- 普通用户使用服务端 SSO code exchange 与本地 cookie session，浏览器不接触 provider token，也不把身份写入浏览器存储。
- 管理员仍使用 `/admin/login` 的本地用户名密码登录和内存 Bearer；公开导航、普通用户菜单没有管理员入口。
- 新应用允许先提交无图标材料，但批准上架必须有有效图标和完整中英文发布信息。

本次没有提交、推送或部署代码。SSO 与投稿审核验证使用一次性 MySQL、对象目录、API 和 Vite 实例；CWNAS 源接入验收使用现有本地 AppStore API 和本机 CWNAS 运行实例。测试后已恢复原同源商店配置并删除临时凭据、隧道、代理和应用包。

## 端到端流程

```text
公共页用户按钮
  -> POST /api/v1/auth/sso/start
  -> 外部 Casdoor 授权
  -> GET /api/v1/auth/sso/callback
  -> 服务端交换 code 并建立 HttpOnly cookie
  -> GET /api/v1/user/session
  -> POST /api/v1/submissions
  -> pending，仅用户与管理员可见
  -> POST /api/v1/admin/submissions/{id}/approve
  -> approved + Catalog 发布 + CW 读取模型 + Audit outbox
```

用户 API：

| 用途 | API |
| --- | --- |
| 启动 SSO | `POST /api/v1/auth/sso/start` |
| SSO 回调 | `GET /api/v1/auth/sso/callback` |
| 用户会话 | `GET /api/v1/user/session` |
| 用户退出 | `POST /api/v1/user/logout` |
| 投稿列表/创建 | `GET/POST /api/v1/submissions` |
| 投稿详情 | `GET /api/v1/submissions/{id}` |

管理员审核 API：

| 用途 | API |
| --- | --- |
| 投稿列表 | `GET /api/v1/admin/submissions` |
| 投稿详情 | `GET /api/v1/admin/submissions/{id}` |
| 批准发布 | `POST /api/v1/admin/submissions/{id}/approve` |
| 拒绝投稿 | `POST /api/v1/admin/submissions/{id}/reject` |

## 真实联调证据

### SSO 与会话

- 授权页正确识别应用客户端并接受 loopback callback。
- 登录成功后回调返回 `303` 到原始应用详情页。
- `/api/v1/user/session` 返回 `200`，页头由“使用 SSO 登录”切换为普通用户菜单。
- provider code/token 只在后端交换；前端仅观察本地用户会话。

开发回调可使用 loopback HTTP，但限制必须同时成立：

1. 环境是 `development`。
2. scheme 是 `http`。
3. host 是 `localhost` 或 loopback IP。

生产 callback 和所有 provider endpoint 仍只接受 HTTPS。

### 投稿与审核

第一次无图标投稿创建成功并进入 `pending`，公共查询返回 `not_found`。管理员尝试批准时返回：

```text
422 publication_invalid / current_icon_required
```

该投稿随后按真实原因拒绝，终态为 `rejected / revision 2`，没有创建公开应用。

第二次投稿携带有效 PNG 图标：

- 用户详情显示 `pending / revision 1 / has_icon=true`。
- 批准前公共 Catalog 查询是 `not_found`。
- 管理员批准返回 `200`，投稿变为 `approved / revision 2`，`published_revision=2`。
- 用户详情刷新后显示“已通过”。
- 公共应用详情展示图标、中文描述、分类、当前 `v1.0.0` 和 Compose 下载入口。
- CW list 与 info 接口均能读取新应用。
- 图标对象返回 `200 image/png`，Compose 返回 `200 application/yaml`。
- MySQL 中应用为 `published / revision 2`，对应 1 个 Release、1 个 icon asset 和 1 个批准审计 outbox。
- 浏览器最终 warning/error 日志为空。

### CWNAS 真实应用市场源验收

验收目标是本机 `https://127.0.0.1:9081`上的真实 CWNAS 进程。测试期间临时把 AppStore API 作为外部市场源，使用 Dozzle 进行完整消费验证：

- 商店列表返回 `200`，共 213 个应用；Dozzle 详情与 Compose 均返回 `200`。
- 图标解码与下载成功，大小为 56,850 字节。
- `POST /api/v1/apps/{uuid}/actions/download` 返回 `200` 且业务成功，生成 `compose.template.yaml`、`metadata.yaml` 和 `icon.png`，审计事件为 `app.download.success`。
- `POST /api/v1/apps/{uuid}/actions/install` 返回 `500`，唯一阻断是本机无法连接 `unix:///var/run/docker.sock` 的 Docker daemon。应用保持 `disabled`，没有创建容器，也没有启动入口端口。
- `DELETE /api/v1/apps/{uuid}/package` 返回 `200`，下载包、应用记录和落盘目录均已清除，审计事件为 `app.package.remove.success`。

清理后 CWNAS 恢复默认同源市场路径 `/api/v1/store/apps`。同源源当前返回 `200 / total=0`，Dozzle 记录与目录均不存在；CWNAS 继续监听 `9081`，本次创建的临时代理与隧道端口均已释放。

## 联调发现并修复的问题

### 领域层重复拒绝 loopback HTTP callback

配置层已经允许开发 loopback HTTP，但 `NewOAuthState` 仍硬编码要求 HTTPS，导致 SSO start 返回 `401`。

修复后领域规则统一为：

- HTTPS 绝对 URI；或
- HTTP 且 host 是 `localhost`/loopback IP；
- 拒绝远程 HTTP、userinfo、fragment 和无 host URI。

生产环境是否允许 HTTP 仍由配置层拒绝，领域层只保留 URI 本身的安全边界。

### 空集合被编码成 `null`

无附加标签或镜像时，后端 DTO 曾输出：

```json
{"tags":null,"images":null}
```

前端契约是 `string[]`，详情组件会调用 `tags.join()` 和 `images.length`，导致用户详情与管理员详情只显示页头，审核按钮无法渲染。

应用层 view 现在保证输出：

```json
{"tags":[],"images":[]}
```

JSON 序列化测试直接锁定该线协议。

### 已通过投稿仍显示“待审核”提示

详情 URL 保留 `notice=submitted` 时，刷新已通过投稿仍会显示“当前状态为待审核”。现在只有 query notice 存在且服务端状态确实是 `pending` 时才显示提交成功提示；`approved` 和 `rejected` 以服务端终态为准。

## 聚焦复验

后端没有运行无界 `go test ./...`，只覆盖改动包与直接消费者：

```bash
go test ./internal/catalog/application ./internal/catalog/adapter/submissionhttp ./internal/identity/domain ./internal/identity/application ./internal/identity/adapter/httpapi ./internal/identity/adapter/casdoor ./internal/platform/config ./cmd/api
```

```bash
go build ./cmd/api
```

```bash
./scripts/check-governance.sh
```

前端只运行投稿详情和管理员审核的聚焦测试：

```bash
npm test -- tests/unit/submission-detail-view.test.ts tests/unit/submission-detail-panel.test.ts tests/unit/admin-submissions-view.test.ts
```

```bash
npm run typecheck
```

```bash
npm run check:governance
```

两仓 `git diff --check` 均通过。

## 证据边界

本次证明了真实 Casdoor 登录、loopback callback、本地 cookie session、真实 MySQL 事务、对象读写、用户与管理员前端、公共 Catalog/CW 读取、本地浏览器交互，以及本机真实 CWNAS 对外部市场源的列表、详情、Compose、图标和下载消费。

仍未证明：

- 生产 Nginx、systemd 与部署配置；
- 真实 OSS/CDN；
- 生产域名 callback 与生产 cookie；
- CWNAS 中的实际容器安装、启动和入口访问（本机 Docker daemon 不可用）；
- 独立设备上的部署配置、Docker 运行时与真实网络路径；
- 外部并发审核与生产流量下的行为。

## 来源

- 当前 Codex 联调会话与直接运行证据。
- 后端实现任务：`01a04ce2-9cc4-7502-b4b3-29e843fd00e2`。
- 前端实现任务：`01a04d08-67d2-7c90-affa-f1ee7df17755`。
- 独立 QA 任务：`01a04d20-37fe-7483-8b62-9f2616378107`。
- 代码范围：`/home/skynit/workspace/appstore_backend`、`/home/skynit/workspace/appstore_frontend`。
- 已排除：SSO 账号密码、客户端密钥、cookie、Bearer、临时管理员密码及不可复用的临时端口和目录。
