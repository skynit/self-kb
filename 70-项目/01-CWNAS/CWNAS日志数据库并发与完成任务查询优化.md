---
title: CWNAS 日志数据库并发与完成任务查询优化
created: 2026-08-22
updated: 2026-08-22
type: project/engineering
tags:
  - CWNAS
  - SQLite
  - WAL
  - taskmanage
  - 性能
---

# CWNAS 日志数据库并发与完成任务查询优化

## 结论

这次故障的核心不是每秒写入量超过 SQLite 能力，而是 `log.db` 在 `DELETE` journal 模式下发生读写锁竞争，同时完成任务接口把一个业务请求放大为最多 12 条 SQL。锁持续超过驱动默认的 5 秒等待后，`ListFinishedTasks()` 返回 `SQLITE_BUSY`，路由又把数据库错误错误映射为 `404 TaskNotFound`。

当前已经落地两项修复：

1. `ListFinishedTasks()` 改为 1 条分页明细查询加 1 条条件聚合查询。
2. 日志写连接和 API 查询连接统一通过日志 SQLite 包打开，并显式启用 WAL、5 秒锁等待和 WAL checkpoint 参数。

本次没有修改主业务数据库 `cw.db`，也没有保留后来试验过的完成任务部分索引创建代码。

## 当前实现

### 日志数据库 DSN

日志数据库的两个入口共用同一个 Dialector：

```go
dsn := fmt.Sprintf("file:%s?mode=rwc&_pragma=journal_mode(WAL)&_pragma=busy_timeout(5000)&_pragma=synchronous(NORMAL)&_pragma=wal_autocheckpoint(1000)", filePath)
```

调用关系：

```text
appconfig.InitLog()
  -> pkg/log/db/sqlite.Open()
    -> github.com/glebarez/sqlite.Open(dsn)
```

`appconfig/log.go` 不再直接导入 `github.com/glebarez/sqlite`，是为了避免查询连接绕过统一 DSN。驱动仍在 `pkg/log/db/sqlite/sqlite.go` 中使用。

参数含义：

| 参数 | 含义 | 相比原实现 |
| --- | --- | --- |
| `file:%s` | 用 SQLite URI 打开指定文件 | 目标文件不变 |
| `mode=rwc` | 读写打开，不存在时创建 | 将原来的隐式行为显式化 |
| `journal_mode(WAL)` | 新页面先追加到 `log.db-wal` | 从通常的 `DELETE` 模式切换为 WAL，普通读写可并行 |
| `busy_timeout(5000)` | 遇锁最多等待 5000 ms | 驱动原本已有 5000 ms 默认值，现在不再依赖隐式默认 |
| `synchronous(NORMAL)` | WAL 提交减少同步刷盘次数 | 降低日志高频写入的同步 I/O；突然断电时可能丢失最近少量日志 |
| `wal_autocheckpoint(1000)` | WAL 约累计 1000 页后自动尝试 checkpoint | 固定 SQLite 常见默认值 |

日志库没有加入主库使用的 `cache=shared` 和 `foreign_keys(1)`：日志读写使用独立连接，共享缓存可能额外引入表级 `SQLITE_LOCKED`；当前日志表也没有需要启用的外键约束。

### 完成任务查询

旧实现调用通用 `QueryLogs()` 四次：

| 用途 | 旧 SQL 数量 |
| --- | ---: |
| 当前页列表和总数 | 3 |
| 成功数量 | 3 |
| 失败数量 | 3 |
| 取消数量 | 3 |
| 合计 | 12 |

每次 `QueryLogs()` 都执行 `MAX(id)`、`COUNT(*)` 和 `SELECT`。其中：

- `MAX(id)` 原本用于分页快照，但接口没有把 `MaxID` 返回给客户端，也没有在下一页复用，因此是纯额外开销。
- 三次状态统计只需要数量，却仍执行了 `SELECT * LIMIT 1`。

新实现固定为两条 SQL：

1. 分页查询当前页任务明细，用于响应中的 `Tasks`。
2. 一次条件聚合得到 `Total`、`SuccessCnt`、`FailedCnt` 和 `CancelledCnt`。

聚合逻辑等价于：

```sql
SELECT
    COUNT(*) AS total,
    COALESCE(SUM(CASE WHEN json_extract(attrs, '$.task_status') = 'success' THEN 1 ELSE 0 END), 0) AS success_cnt,
    COALESCE(SUM(CASE WHEN json_extract(attrs, '$.task_status') = 'failed' THEN 1 ELSE 0 END), 0) AS failed_cnt,
    COALESCE(SUM(CASE WHEN json_extract(attrs, '$.task_status') = 'cancelled' THEN 1 ELSE 0 END), 0) AS cancelled_cnt
FROM logs
WHERE module = 'taskmanager'
  AND user_id = ?
  AND json_extract(attrs, '$.task_flag') = 'finished';
```

分页明细不能删除，因为接口还要返回任务 ID、类型、状态、描述、路径和完成时间等当前页数据；聚合 SQL 只返回计数。

## DELETE 与 WAL 的并发差异

`DELETE` 模式提交时需要修改主数据库并删除 rollback journal。写事务准备提交时必须等待已有读事务结束，长查询会扩大写入等待窗口。

```text
读查询持有 SHARED 锁
  -> 日志写事务准备提交
  -> 等待更强的文件锁
  -> 等待超过 busy_timeout
  -> SQLITE_BUSY
```

WAL 模式先把新页面追加到 `log.db-wal`。读事务读取 `log.db` 加开始时可见的 WAL 快照，写事务可以继续向 WAL 末尾追加，因此普通读取通常不再阻塞日志提交。

WAL 仍有边界：

- SQLite 仍然只有一个写者，写写冲突没有消失。
- 长读事务可能阻止 checkpoint 完全回收 WAL，表现为 `log.db-wal` 增长。
- 建表、改索引和切换 journal 模式仍需要更强的锁。
- 磁盘满、I/O 错误和异常事务仍需单独处理。

## 故障证据与证据边界

事故采样中，日志库约 862 MB、123 万行；`call API func` 约 120.8 万行，占 97.93%，而 `taskmanager` 记录约 1047 行。最近 75 分钟的文件日志速率约为：

| 来源 | 速率 |
| --- | ---: |
| 全部日志 | 199 条/分钟 |
| API 中间件日志 | 119 条/分钟 |
| 文件访问审计 | 67 条/分钟 |

因此高负载的主要来源是全量 API 审计和文件访问审计，不是完成任务查询本身。199 条/分钟约等于 3.3 条/秒，正常 SQLite 足以承受；问题在于长读锁窗口、每条日志独立事务、多个连接和错误处理缺失共同放大了碰撞概率。

故障期间直接观察到：

- `ListFinishedTasks()` 的受阻 SQL 等待约 5 秒后返回 `SQLITE_BUSY`。
- 持久异常阶段存在长期未消失的 `log.db-journal`，主进程同时持有相关读写锁，数据库停止成功增长。
- 日志写入路径 `_BaseHandler.process() -> GORM Create()` 没有读取 `.Error`，所以没有留下写事务到底在 `COMMIT` 返回 `BUSY`、卡在提交阶段，还是遇到其他写错误的直接日志证据。

因此可以确认的是“数据库存在持久锁且写入错误不可观测”；不能把每一次 `ListFinishedTasks()` 的 `SQLITE_BUSY` 都解释为写事务已经永久损坏。持久锁形成前也存在短时竞争：读请求可能失败，但写事务随后正常提交，下一次读取便能成功。

## 验证结果

本地定向验证仅覆盖直接相关包，没有运行全量测试：

```bash
go test ./pkg/log/db/sqlite ./cmd/server/appconfig ./pkg/taskmanage
```

真机验收基线：

| 项目 | 结果 |
| --- | --- |
| 分支 / commit | `logdb` / `8d262b32` |
| 主程序 SHA-256 | `c59d4691dbc748b5c8049e37b263edb67768c8e370344920736dd33a0bc46d19` |
| 服务状态 | `active/running`，`NRestarts=0` |
| 运行数据库 | `/var/lib/cwnas/config/log.db` |
| journal 模式 | `wal` |
| 完整性检查 | `PRAGMA quick_check` 返回 `ok` |
| 锁错误 | 验收窗口内未出现 `SQLITE_BUSY` 或 `SQLITE_LOCKED` |

受控验证中，外部写锁保持 3 秒时，完成任务同形只读查询约 13 ms 完成；同时产生的 50 条 API 审计日志在锁释放后全部写入。测试库约 9.55 万条日志，新的两条完成任务 SQL 合计约 10 至 28 ms。

鉴权后的测试 NAS HTTP 成功路径没有完成：测试机没有可用的已知账号或浏览器登录态，因此没有伪造 JWT 或修改账号。另一个本地运行实例曾记录 14 次 `/api/v1/tasks/finished` 全部返回 200，耗时约 1.53 至 3.99 ms，但不能代替目标测试机的鉴权验收。

## 尚未实施的改进

以下内容在审查中被识别，但不属于本次已落地范围：

- 将日志写入改为“最多 50 条或定时器触发”的批量提交。
- 检查 `Create().Error`，对明确的 `SQLITE_BUSY`、`SQLITE_LOCKED` 做有限重试，并确保失败连接恢复。
- 将日志写连接池限制为单写连接。
- 增加 `busy`、`locked`、写失败、通道丢弃和批次重试计数；这些指标不能再写回同一个 `log.db`。
- 增加日志保留周期或容量上限，防止主库、WAL 和历史日志耗尽根分区。
- 将数据库锁错误从 `404 TaskNotFound` 改为 `500` 或 `503`。
- 完成任务部分索引曾被试验，但按后续明确要求已删除，当前初始化不会创建该索引。

> [!warning] 容量风险
> 真机验收时根分区使用率已达到 98%。WAL 会额外占用 `log.db-wal` 空间，因此容量治理仍是独立的运行门禁。

## 来源

- Codex 会话：`01a01e60-a1ee-7053-acbf-97e899d8effb`
- 代码范围：`pkg/log/db`、`cmd/server/appconfig`、`pkg/taskmanage`
- 已排除：会话中的明文账号密码、私有 IP、SSH 授权细节和不可复用的临时主机信息。
