---
title: Intel 部件教程（一）：SmarTune 资源平衡与监控
created: 2026-09-20
updated: 2026-09-20
type: project/tutorial
tags:
  - CWNAS
  - Intel
  - SmarTune
  - eBPF
  - cgroup
  - XPU
---

# Intel 部件教程（一）：SmarTune 资源平衡与监控

同系列：
[[Intel部件教程二-HeliconSearch安装与使用|（二）HeliconSearch 多模态检索]]、
[[Intel部件教程三-EdgeCraftRAG安装与使用|（三）EdgeCraftRAG 本地 RAG 问答]]。

## 1. 这个部件是干什么的

Intel XPU SmarTune 是 CWNAS Intel 版内置的**宿主机资源治理服务**，一个进程同时干两件事：

- **监控**：采集 Linux PSI（CPU/内存/IO 压力）、GPU/NPU、磁盘、网卡等指标，算出 0~4 级压力等级（low / medium / high / critical）。
- **平衡**：通过 eBPF 拦截受控应用的 execve 启动与退出，用 cgroups v2 给「最耗资源的应用」动态限额（CPU 配额、memory.high、io.weight、每盘 io.max 读写带宽与 IOPS），并按优先级排队、保活、按压力恢复。

它面向的是**宿主机上运行的进程**（本地应用、命令行程序），不是 Docker 容器。这点很重要：HeliconSearch 和 EdgeCraftRAG 跑在容器里，不属于 SmarTune 的受控对象。

关键坐标：

| 项目 | 值 |
| --- | --- |
| 安装目录 | `/opt/intel-xpu-smartune` |
| 监听地址 | `https://127.0.0.1:9001`（自签证书，HTTPS） |
| systemd 单元 | `/etc/systemd/system/smartune.service` |
| 启动脚本 | `cwnas-smartune-bootstrap`（`/usr/local/sbin/`） |
| 就绪状态文件 | `/var/lib/cwnas/smartune/bootstrap.env` |
| API 令牌 | `/opt/intel-xpu-smartune/key/api_token`（0600，仅 root 可读） |
| 运行日志 | `/opt/intel-xpu-smartune/logs/smartune.log`，stderr 进 journal |
| 上游版本 | `github.com/intel/intel-xpu-smartune`，commit `1a25fdb9` |

## 2. 安装形态：镜像内自带，离线可用

CWNAS Intel 版把 SmarTune **源码归档直接打进 RootFS**，安装系统时就已经落盘，不需要联网下载：

1. RootFS 构建阶段把 `smartune/intel-xpu-smartune.tar.xz` 解压到 `/opt/intel-xpu-smartune`，并写入上游 commit 号到 `.cwnas-upstream-commit`。
2. 同一阶段用 `pip install --break-system-packages --requirement /opt/intel-xpu-smartune/requirements.txt` 装好 Python 依赖（psutil、peewee、flask 等）。
3. 单元文件 `smartune.service` 安装到 `/etc/systemd/system/`，并 `systemctl enable smartune.service`，开机自启。
4. 构建期只做「enable」，不启动服务；**真正启动并确认「就绪」发生在首次配置阶段**（见 3.2）。

所以安装分两段：装机时落盘，首次启动后由 AI 配置流程正式启用。

## 3. 安装步骤

### 3.1 前提条件

- 系统已装好（CWNAS Intel 版 ISO 安装完成）。
- `kheaders` 内核模块可用：单元里 `ExecStartPre=/usr/sbin/modprobe kheaders`，缺失会直接启动失败（eBPF 编译需要）。
- `bcc`、`cpupower` 已随镜像安装；否则监控与调频不可用。
- 端口 `9001` 未被占用。
- 以 root 执行。

### 3.2 随首次配置自动启用（推荐路径）

系统联网后，以 root 执行总的 AI 配置脚本，它的第 2 阶段会调用 SmarTune 的 bootstrap：

```bash
sudo ~/setup-ai.sh
```

也可以单独只做 SmarTune 这一步，不碰其它部件：

```bash
sudo /usr/local/sbin/cwnas-smartune-bootstrap
```

脚本做的事，按顺序：

1. 检查 `cat chmod curl jq mkdir sleep systemctl` 是否可用，`/opt/intel-xpu-smartune/start_smartune_service.sh` 与单元文件是否存在。
2. `systemctl daemon-reload` + `systemctl enable --now smartune.service`。
3. 每 2 秒轮询一次，最多 120 次（4 分钟），同时满足三个条件才算就绪：服务 active、`key/api_token` 非空、`GET /smartune/capabilities` 返回 `data.capabilities == 1`。
4. 就绪后写状态文件 `/var/lib/cwnas/smartune/bootstrap.env`（0600）：

```text
STATUS=ready
MODE=balancer_and_monitor
URL=https://127.0.0.1:9001/smartune/capabilities
TOKEN_FILE=/opt/intel-xpu-smartune/key/api_token
```

**脚本不是幂等的守护进程**：它只负责「启动 + 等就绪 + 记录状态」，之后服务由 systemd 托管，重启机器会自动拉起。4 分钟内没就绪就直接报错退出，并打印 `systemctl status`。

### 3.3 安装结果核对

```bash
systemctl is-enabled smartune.service      # 应输出 enabled
systemctl is-active  smartune.service      # 应输出 active
cat /var/lib/cwnas/smartune/bootstrap.env  # 应包含 STATUS=ready
sudo cat /opt/intel-xpu-smartune/key/api_token
```

手工验一次接口（`-k` 是接受自签证书）：

```bash
TOKEN=$(sudo cat /opt/intel-xpu-smartune/key/api_token)
curl -fsSk -H "X-Auth-Token: $TOKEN" https://127.0.0.1:9001/smartune/capabilities
# {"retcode":0,"retmsg":"...","data":{"capabilities":1}}
```

`capabilities` 为 `1` 表示「balancer + monitor 全功能」；为 `0` 表示只跑监控（用 `-m` 模式启动）。bootstrap 要求必须是 `1`。

## 4. 使用

### 4.1 服务控制

```bash
sudo systemctl status smartune.service
sudo systemctl restart smartune.service     # 改完 config.yaml 后重启
sudo systemctl stop smartune.service
tail -f /opt/intel-xpu-smartune/logs/smartune.log
journalctl -u smartune.service -f
```

手动前台运行（排错用，`-a` 全功能 / `-m` 只监控）：

```bash
cd /opt/intel-xpu-smartune
./start_smartune.sh        # 等价于 -a
./start_smartune.sh -m     # 只跑监控
```

两种模式都监听 9001，Dashboard 会读 `/smartune/capabilities` 自适应。

### 4.2 认证方式

除 `POST /auth/login` 外，**所有接口都要带 `X-Auth-Token` 头**；唯一例外是 SSE 流 `GET /app/events`，它允许把 token 放在查询串里（因为浏览器 `EventSource` 无法自定义请求头）。

```bash
TOKEN=$(sudo cat /opt/intel-xpu-smartune/key/api_token)

# 登录握手，验证令牌是否有效
curl -sk -X POST https://127.0.0.1:9001/auth/login \
  -H 'Content-Type: application/json' \
  -d "{\"pwd\":\"$TOKEN\"}"
```

令牌来源优先级：环境变量 `BALANCER_API_TOKEN` > 已存在的 `key/api_token` > 首次启动时随机生成并落盘。

### 4.3 常用查询

```bash
# 动态指标（CPU/内存/IO/GPU/NPU 快照），可按 section 过滤
curl -sk -H "X-Auth-Token: $TOKEN" https://127.0.0.1:9001/monitor/dynamic_info
curl -sk -H "X-Auth-Token: $TOKEN" https://127.0.0.1:9001/monitor/dynamic_info/gpu

# 硬件与软件静态信息（CPU 拓扑、GPU 静态配置、NPU 固件、驱动版本等）
curl -sk -H "X-Auth-Token: $TOKEN" https://127.0.0.1:9001/monitor/static_info

# 类 top 的进程列表
curl -sk -H "X-Auth-Token: $TOKEN" https://127.0.0.1:9001/monitor/processes

# CPU/内存/GPU 占用 Top N 应用（后台每 2 秒刷新缓存）
curl -sk -H "X-Auth-Token: $TOKEN" 'https://127.0.0.1:9001/monitor/app_resource_stats?n=10'

# 当前受控应用清单
curl -sk -H "X-Auth-Token: $TOKEN" -X POST https://127.0.0.1:9001/app/get_controlled_app \
  -H 'Content-Type: application/json' -d '{}'
```

所有响应统一格式：`{"retcode":0,"retmsg":"success","data":{...}}`。`retcode` 常用值：`0` 成功、`101` 参数错、`103` 操作失败、`104` 已存在、`108` 权限不足、`401` 未授权。

### 4.4 常用操作

```bash
# 设置优先级（会同步调整 OOM 分数）
curl -sk -H "X-Auth-Token: $TOKEN" -X POST https://127.0.0.1:9001/app/set_priority \
  -H 'Content-Type: application/json' \
  -d '{"app_id":"com.example.app","priority":5}'

# 手动下发资源限制（cgroup 层），limits 可覆盖默认档位
curl -sk -H "X-Auth-Token: $TOKEN" -X POST https://127.0.0.1:9001/app/resource_limit \
  -H 'Content-Type: application/json' \
  -d '{"app_id":"com.example.app","app_name":"example","priority":"3",
       "limit_overrides":{"cpu_quota":50,"memory_max_mb":2048}}'

# 查询该应用的默认限制档位与可调范围（给 UI/脚本用）
curl -sk -H "X-Auth-Token: $TOKEN" -X POST https://127.0.0.1:9001/app/resource_limit_profile \
  -H 'Content-Type: application/json' -d '{"app_id":"com.example.app"}'

# 恢复资源限制
curl -sk -H "X-Auth-Token: $TOKEN" -X POST https://127.0.0.1:9001/app/resource_restore \
  -H 'Content-Type: application/json' -d '{"app_id":"com.example.app"}'

# 实时事件流（应用状态变化 + 心跳）
curl -sk -N -H "X-Auth-Token: $TOKEN" https://127.0.0.1:9001/app/events
```

首次把某个应用纳入管控，用向导式三连：`/app/discover_search`（按关键字扫 `/proc`）→ `/app/discover_extract`（从 PID 推导 bpf_name/id）→ `/app/new_controlled_app`（一次写入配置 + 数据库 + eBPF）。

完整接口清单见 `/opt/intel-xpu-smartune/docs/API_ENDPOINTS.md`。

### 4.5 配置调优

主配置是 `/opt/intel-xpu-smartune/config/config.yaml`，改动后 `systemctl restart smartune.service` 生效。日常要动的主要是这几项：

- `vendor`: 默认 `generic`；以管理员权限运行时按上游说明改成 `admin`。
- `enable_network_control`: 默认 `false`，网络整形（cgroup + iptables + tc/HTB）要显式打开。
- `limit_policy`: 各优先级档位的 CPU 比例、`memory.high`、每盘读写带宽与 IOPS。`policy: separated` 表示 CPU/内存与磁盘 IO 独立判定。
- `media_scale`: 按介质类型（nvme / sata_ssd / mmc / hdd / usb）缩放磁盘额度，默认表是按 NVMe 标定的。
- 压力阈值、EWMA、冷却时间等，见上游 `docs/pressure_algorithm.md`。

### 4.6 图形化 Dashboard（可选）

源码目录里带一个 React Dashboard（6 个页签：Performance / App Resources / Process Resources / Balancer / History / About），但它**不随 ISO 安装**，需要机器上有 Node.js ≥ 20.19：

```bash
cd /opt/intel-xpu-smartune/dashboard
bash start_dashboard.sh          # 首次会尝试自动装 Node.js
# 浏览器打开 http://localhost:39527
```

Dashboard 通过 Vite 代理把 `/api/*` 转发到 `https://127.0.0.1:9001`，所以自签证书不会触发浏览器告警；登录时填 `key/api_token` 里的令牌。

### 4.7 它在 CWNAS 里的边界

CWNAS 服务端（`cw`）目前**不调用** SmarTune 的业务接口，也没有对应配置项。SmarTune 是独立运行的宿主机治理服务，安装流程只在 bootstrap 里确认它「已就绪」。因此：

- 关掉 SmarTune 不会影响 CWNAS 的网页管理、HeliconSearch 或 EdgeCraftRAG。
- 反过来，SmarTune 也不会去限制 Docker 容器，容器侧的资源竞争要靠 vLLM 自己的 `GPU_MEMORY_UTIL` / `ZE_AFFINITY_MASK` 等参数控制。

## 5. 日常运维

- **日志**：`logs/smartune.log` 是应用日志，异常栈多在 journal 里（`journalctl -u smartune.service`）。
- **证书**：首次启动自动在 `key/` 生成 `b_server.crt` / `b_server.key`（CN=localhost，SAN IP:127.0.0.1，有效期 365 天）。到期后停服务、删除这两个文件、再启动即可重签。
- **令牌轮换**：删除 `key/api_token` 后重启服务会生成新令牌。注意 `/var/lib/cwnas/smartune/bootstrap.env` 里记录的是路径而非令牌本体，轮换不影响该文件，但所有拿旧令牌的调用方都要更新。
- **数据库**：运行数据落在仓库根目录的 `my_database.db`（peewee/SQLite），历史与保留策略通过 `/monitor/history/retention` 调整。
- **升级**：RootFS 里带的是固定上游 commit，升级等于换 ISO；若只是替换代码，保持 `/opt/intel-xpu-smartune` 路径不变即可，单元文件不用改。

## 6. 排错

| 现象 | 原因与处理 |
| --- | --- |
| bootstrap 报 `missing SmartTune entry point` | `/opt/intel-xpu-smartune/start_smartune_service.sh` 不存在或不可执行，RootFS 不完整，需重刷镜像 |
| bootstrap 报 `missing systemd unit: smartune.service` | 单元文件未安装，检查 `/etc/systemd/system/smartune.service` |
| 4 分钟后报 `was not ready within 4 minutes` | 先看 `systemctl status` 与 `logs/smartune.log`；常见是 `modprobe kheaders` 失败、bcc 缺失、端口 9001 被占 |
| `capabilities` 一直不是 1 | 服务跑成了监控模式（`-m`）或被降级；检查启动参数与日志 |
| `curl` 报证书错误 | 自签证书，本地调用加 `-k`；不要试图去掉 `-k` |
| 接口返回 `401`/`109` | 漏了 `X-Auth-Token` 头，或令牌已轮换 |
| 磁盘/网络限额不生效 | 磁盘受 `media_scale` 与 `candidate_floor` 双重门限约束；网络控制默认关闭，需 `enable_network_control: true` |

## 7. 脚本用法

### 7.1 系统侧脚本

| 脚本 | 参数 | 行为 |
| --- | --- | --- |
| `~/setup-ai.sh` | 无 | AI 总装入口，**第 2 阶段**调用 `cwnas-smartune-bootstrap`。仅 root 可跑；用 `flock -n /run/cwnas-ai-setup.lock` 防并发（重复执行会被拒绝）；中途失败打印「stage N/6 + 阶段名」并提示重跑，**可以反复执行** |
| `/usr/local/sbin/cwnas-smartune-bootstrap` | 无 | 启服务 + 等就绪。成功打印 `Intel XPU SmarTune is ready on https://127.0.0.1:9001` 并 `exit 0`；超过 4 分钟先打印 `systemctl status` 再 `exit 1` |
| `/usr/local/sbin/cwnas-ai-configure` | 无（环境变量见教程二 7.2） | 只负责 CWNAS 的 AI 端点与 HeliconSearch 口令，不涉及 SmarTune |

两点使用限制：

- `cwnas-smartune-bootstrap` 依赖 `systemctl` 操作真实 systemd，**不能进 chroot 或在容器里执行**。
- 它**幂等**：服务已 active、`key/api_token` 已存在、capabilities 已为 1 时立刻返回 0 并重写状态文件，所以任何时候重跑都安全。

### 7.2 SmarTune 自带脚本

| 脚本 | 参数 | 说明 |
| --- | --- | --- |
| `start_smartune.sh` | `-a` / `-m` / `-h` | `-a`（默认）balancer + monitor 同进程；`-m` 只跑监控；`-h` 看帮助。脚本内部用 `sudo -E` 拉起 `smartune.py`，退出时 `pkill -f smartune.py` |
| `smartune.py` | `-a` / `--all` / `-m` / `--monitor` | 真正的 Python 入口，`start_smartune.sh` 的底层；也可直接调用 |
| `start_smartune_service.sh` | 无 | systemd 单元的 `ExecStart`：按需生成 `key/b_server.crt`、`key/b_server.key` 后 `exec smartune.py`。只给 systemd 用，不建议手工执行 |
| `dashboard/start_dashboard.sh` | `-h` | 拉起 Dashboard 开发服务器（默认 `http://localhost:39527`）；缺 Node.js 时会按 apt/dnf/brew/nvm 顺序尝试自动安装 20 LTS |

Dashboard 的后端地址可覆盖，指向别的机器时用它：

```bash
cd /opt/intel-xpu-smartune/dashboard
VITE_PROXY_TARGET=https://192.168.1.10:9001 bash start_dashboard.sh
```

### 7.3 组合命令

```bash
# 只重装/重启 SmarTune，不动其它 AI 部件
sudo /usr/local/sbin/cwnas-smartune-bootstrap

# 改完 config.yaml
sudo systemctl restart smartune.service
tail -n 50 /opt/intel-xpu-smartune/logs/smartune.log

# 前台排错：先停服务，再单跑监控模式（脚本会 pkill 已有实例）
sudo systemctl stop smartune.service
cd /opt/intel-xpu-smartune && ./start_smartune.sh -m

# 状态三件套：服务状态、就绪文件、接口探测
systemctl is-active smartune.service
sudo cat /var/lib/cwnas/smartune/bootstrap.env
sudo bash -c 'curl -fsSk -H "X-Auth-Token: $(cat /opt/intel-xpu-smartune/key/api_token)" \
  https://127.0.0.1:9001/smartune/capabilities'
```

## 8. 卸载与重装

```bash
sudo systemctl disable --now smartune.service
sudo rm -f /etc/systemd/system/smartune.service
sudo systemctl daemon-reload
sudo rm -rf /var/lib/cwnas/smartune          # 状态文件，按需删
sudo rm -rf /opt/intel-xpu-smartune          # 如需彻底清掉
```

重装 = 重新解压源码归档到 `/opt/intel-xpu-smartune`（保持路径一致）、重装 `requirements.txt`、放回单元文件、再跑一次 `cwnas-smartune-bootstrap`。

在非 CWNAS 的 Ubuntu/Debian 上手工装的标准流程（上游 README 摘要）：`pip install psutil>=5.5.1 peewee==3.17.8 flask --break-system-packages`，补齐 `bcc`、`cpupower`，把 `smartune.service` 里的 `__SMARTUNE_ROOT__` 占位替换成仓库根目录后 `systemctl enable --now smartune.service`。
