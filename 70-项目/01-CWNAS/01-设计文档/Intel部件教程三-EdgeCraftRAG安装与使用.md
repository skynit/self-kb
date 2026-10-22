---
title: Intel 部件教程（三）：EdgeCraftRAG 本地 RAG 问答
created: 2026-09-20
updated: 2026-09-20
type: project/tutorial
tags:
  - CWNAS
  - Intel
  - EdgeCraftRAG
  - vLLM
  - Milvus
  - XPU
  - RAG
---

# Intel 部件教程（三）：EdgeCraftRAG 本地 RAG 问答

同系列：
[[Intel部件教程一-SmarTune安装与使用|（一）SmarTune 资源平衡与监控]]、
[[Intel部件教程二-HeliconSearch安装与使用|（二）HeliconSearch 多模态检索]]。

## 1. 这个部件是干什么的

EdgeCraftRAG（文中简称 ECRAG）是 CWNAS Intel 版内置的**本地知识库问答栈**，全部推理在本机 Intel XPU 上完成，不依赖云端。它由两套 Compose 文件组成：

第一套 `vllm-compose.yaml` —— 推理引擎：

| 服务 | 镜像 | 端口 | 说明 |
| --- | --- | --- | --- |
| `vllm` | `intel/llm-scaler-vllm:0.14.0-b8.3.2` | 8086 | 容器名 `langchain_agent`，host 网络 + privileged，跑 `Qwen/Qwen3-Coder-30B-A3B-Instruct` |

第二套 `compose.yaml` —— 检索与界面：

| 服务 | 镜像 | 端口 | 说明 |
| --- | --- | --- | --- |
| `etcd` | `quay.io/coreos/etcd:v3.5.5` | 内网 | Milvus 元数据，容器名 `milvus-etcd` |
| `minio` | `minio/minio:RELEASE.2023-03-20T20-16-18Z` | 5043（S3）/ 5044（控制台） | 对象存储，容器名 `milvus-minio` |
| `milvus-standalone` | `milvusdb/milvus:v2.4.6` | 19530 / 9091（healthz） | 向量库 |
| `edgecraftrag-server` | `opea/edgecraftrag-server:latest` | 16010 | 索引与知识库服务，`/dev/dri` 透传 |
| `ecrag` | `opea/edgecraftrag:latest` | 16011 | mega service（编排问答链路） |
| `edgecraftrag-ui` | `opea/edgecraftrag-ui:latest` | 8082 | Web 界面 |

关键坐标：

| 项目 | 值 |
| --- | --- |
| 编排目录 | `/opt/cwnas/edgecraftrag/`（`compose.yaml`、`vllm-compose.yaml`、`milvus-config.yaml`、`.env`、`templates/`） |
| 镜像离线包 | `/var/lib/cwnas/bootstrap/edgecraftrag/edgecraftrag-images.tar`（约 15.3 GB） |
| 模型目录 | `/var/lib/cwnas/edgecraftrag/models`（`BAAI/` 约 2.9 GB，`Qwen/` 约 61 GB） |
| 数据目录 | `/var/lib/cwnas/edgecraftrag/data/{etcd,minio,milvus}`、`workspace/` |
| 就绪状态 | `/var/lib/cwnas/edgecraftrag/bootstrap.env` |
| 使用入口 | `http://<NAS-IP>:8082/` |

## 2. 安装形态：离线三件套

ECRAG 的载荷分三个归档，安装系统时就已复制/解压到目标盘，联网不是必要条件：

1. `edgecraftrag-images.tar` → 复制到 `/var/lib/cwnas/bootstrap/edgecraftrag/`（同目录写 `.sha256`），由 bootstrap 导入 Docker。
2. `edgecraftrag-baai-models.tar` → 解压 `models/BAAI` 到 `/var/lib/cwnas/edgecraftrag/models/BAAI`（embedding `bge-m3-int8`、rerank `bge-reranker-large-int8` 的 OpenVINO 模型）。
3. `edgecraftrag-qwen-model.tar` → 解压 `models/Qwen` 到 `/var/lib/cwnas/edgecraftrag/models/Qwen`（Qwen3-Coder-30B-A3B-Instruct 权重）。

镜像清单（导入后按 ID 打 tag，缺一个就失败）：

| tag | 镜像 ID |
| --- | --- |
| `opea/edgecraftrag-ui:latest` | `sha256:c48dbc4808e7053f0b5cd7c07d45e62878abaead35e878a8a2950c9b281586bf` |
| `opea/edgecraftrag-server:latest` | `sha256:343f119c17fef9e9324eeeb1f31109ce0e1a97c980f28587bd0bfb82bac85196` |
| `opea/edgecraftrag:latest` | `sha256:86b105676bca1da94d8c30465e6078f9c10c682b6194edc61bfcc0b85c4aa37d` |
| `milvusdb/milvus:v2.4.6` | `sha256:1658525cd8858683f6ba98458f4ad46e409ee75b25d609c983194ec509175267` |
| `minio/minio:RELEASE.2023-03-20T20-16-18Z` | `sha256:6d770d7f255cda1f18d841ffc4365cb7e0d237f6af6a15fcdb587480cd7c3b93` |
| `quay.io/coreos/etcd:v3.5.5` | `sha256:89b6debd43502d1088f3e02f39442fd3e951aa52bee846ed601cf4477114b89e` |
| `intel/llm-scaler-vllm:0.14.0-b8.3.2` | `sha256:4454b316ba834d8d45c20c1b4b64c71285f31efff17b8c61d672376e7d25f802` |

## 3. 前置条件

- **Intel XPU**：iGPU 或独立显卡；若检测到 Intel Arc Pro **B60**（PCI ID `0xe211`），脚本会优先把它作为推理设备，并把 `GPU_MEMORY_UTIL` 提到 `0.85`（否则 `0.60`）。B60 存在时还会打开它的电源管理（写 `power/control = on`）。
- **Docker 可用**，`/var/lib/docker` 至少 35 GiB 空闲（镜像导入的硬性检查）。
- **模型盘空间约 64 GB**（BAAI 2.9 GB + Qwen 61 GB），安装器在装系统阶段就占用了这部分空间。
- **端口空闲**：`8082`、`16010`、`16011`、`19530`、`9091`、`5043`、`5044`、`8086`。注意 `8086` 是 vLLM 的 host 网络端口，冲突会导致推理起不来。
- 宿主机有 `video`、`render` 用户组（用于容器内访问 `/dev/dri`）。
- 以 root 执行。

## 4. 安装步骤

### 4.1 随首次配置自动安装（推荐）

```bash
sudo ~/setup-ai.sh
```

对应的三个阶段：

| 阶段 | 命令 | 作用 |
| --- | --- | --- |
| 4/6 | `cwnas-edgecraftrag-bootstrap images` | 校验并导入 7 个镜像，写 `.env`，**不起服务** |
| 5/6 | `cwnas-edgecraftrag-model qwen-verify` | 校验 Qwen 权重是否完整 |
| 6/6 | `cwnas-edgecraftrag-bootstrap start` | 起 vLLM，等就绪后再起 ECRAG 全栈 |

### 4.2 分步手工安装

```bash
# 第一步：导入镜像
sudo /usr/local/sbin/cwnas-edgecraftrag-bootstrap images

# 第二步：核对模型
/usr/local/sbin/cwnas-edgecraftrag-model status        # 三个模型逐个报 ready / missing
/usr/local/sbin/cwnas-edgecraftrag-model qwen-verify   # 只校验 Qwen

# 第三步：启动全栈（等价于 all）
sudo /usr/local/sbin/cwnas-edgecraftrag-bootstrap start
```

`cwnas-edgecraftrag-bootstrap` 只接受 `all | images | start` 三个参数，默认 `all`（= 导入 + 启动）。

### 4.3 脚本内部做了什么

**镜像阶段**

1. 对 `edgecraftrag-images.tar` 做 sha256 校验，检查 `/var/lib/docker` 空间，`docker load` 导入，然后把 7 个镜像 ID 打上可读 tag 并逐个核对 ID，最后删除 15.3 GB 归档。

**环境文件阶段**（`images` 与 `start` 都会执行）

2. 生成 `/opt/cwnas/edgecraftrag/.env`：

```text
HOST_IP=<默认路由出口的局域网地址>
VIDEO_GID=<video 组 gid>
RENDER_GID=<render 组 gid>
ZE_AFFINITY_MASK=<选中的 XPU device_id>
GPU_MEMORY_UTIL=0.85          # 有 B60 时；否则 0.60
```

   XPU 清单通过 `docker run --rm --privileged --entrypoint /usr/bin/xpu-smi intel/llm-scaler-vllm:... discovery -j` 获取；B60 在 Level Zero 里不可见时只打警告，退化为「第一个可见 XPU」。
3. 准备 `/var/lib/cwnas/edgecraftrag/data/etcd`（权限 0700）。

**启动阶段**

4. 先确认 Qwen 权重存在（缺 `model.safetensors.index.json` 直接失败，提示先跑 `~/setup-ai.sh`）。
5. 检查 8 个端口是否被占（若 ECRAG 自己的容器已存在则跳过检查，便于重启）。
6. 起 vLLM，轮询 `http://127.0.0.1:8086/v1/models`，**超时 90 分钟**（首次加载 30B 模型很慢，这个时长是刻意留的）。
7. 起 ECRAG 全栈，最多 10 分钟等 6 个容器 running + 7 个端口监听 + `8082`、`9091/healthz`、`5043/minio/health/live` 三个探活都通过。
8. 写 `/var/lib/cwnas/edgecraftrag/bootstrap.env`：

```text
STATUS=ready
HOST_IP=<NAS 局域网地址>
UI_URL=http://<NAS 局域网地址>:8082/
```

只跑 `images` 时写的是 `STATUS=images_ready`，CWNAS 设备控制台会把这种状态判为「本地 AI 服务尚未完成安装」。

### 4.4 安装结果核对

```bash
# vLLM 是否就绪（应返回模型列表）
curl -sS http://127.0.0.1:8086/v1/models | head -c 400

# 全栈容器
docker compose --env-file /opt/cwnas/edgecraftrag/.env -f /opt/cwnas/edgecraftrag/compose.yaml ps
curl -fsS http://127.0.0.1:9091/healthz >/dev/null && echo milvus-ok
curl -fsS http://127.0.0.1:5043/minio/health/live >/dev/null && echo minio-ok
curl -fsS http://127.0.0.1:8082/ >/dev/null && echo ui-ok

cat /var/lib/cwnas/edgecraftrag/bootstrap.env
```

## 5. 使用

### 5.1 Web 界面

浏览器打开 `http://<NAS-IP>:8082/`，可以：

- 新建知识库（名字必须以字母或下划线开头，只能含字母、数字、下划线）；
- 上传文件或**引用 NAS 上的本地路径**入库，查看切分与索引进度；
- 基于知识库提问，观察检索到的上下文与模型回答；
- 调整检索参数（返回条数、重排、是否启用 RAG 检索）、查看系统信息。

入库用的文件目录是宿主机的 `/var/lib/cwnas/edgecraftrag/workspace`，它同时被挂载进 `edgecraftrag-server`（`/home/user/docs`、`/home/user/ui_cache`）和 UI 容器，所以页面上传与按路径引用的文件都落在这里，**清空或迁移知识库时不要漏掉它**。

提示词模板放在 `/opt/cwnas/edgecraftrag/templates/edgecraftrag/prompt_template/`（`default_prompt.txt`、`default_prompt_ds.txt`、`raw_anwser_prompt.txt`），容器内只读挂载为 `/templates/custom`，改完重启容器生效。

### 5.2 HTTP API

对外入口是 `8082`（Web 界面与 `/v1/...` 接口同端口），`16010` / `16011` 是栈内索引服务与 mega service 的端口，脚本和排错时会用到。

```bash
BASE=http://127.0.0.1:8082

# RAG 问答（走检索增强）
curl -sS -H 'Content-Type: application/json' -d '{
  "messages": "这台 NAS 的备份策略是什么？",
  "max_tokens": 512,
  "top_n": 5,
  "temperature": 0.7
}' $BASE/v1/ragqna

# ChatQnA（可流式，可选是否启用检索与思考）
curl -sS -H 'Content-Type: application/json' -d '{
  "messages": "用一句话总结知识库内容",
  "stream": false,
  "k": 5,
  "chat_template_kwargs": {"enable_rag_retrieval": true, "enable_thinking": false}
}' $BASE/v1/chatqna

# 知识库：列表 / 新建
curl -sS $BASE/v1/knowledge
curl -sS -H 'Content-Type: application/json' -d '{
  "name": "manual",
  "description": "产品手册",
  "active": true
}' $BASE/v1/knowledge

# 上传文件到某个知识库（multipart）
curl -sS -X POST -F 'file=@/path/to/manual.pdf' $BASE/v1/data/file/manual

# 按本地路径入库 / 直接写入文本
curl -sS -H 'Content-Type: application/json' -d '{
  "local_path": "/home/user/docs/manual.pdf"
}' $BASE/v1/data
```

其余常用接口：`PATCH /v1/knowledge/patch`、`DELETE /v1/knowledge/<name>`、`DELETE /v1/data/<kb>`（删除知识库里的文件）、`POST /v1/data` 触发索引。字段名按 snake_case 传递。

### 5.3 在 CWNAS 里使用

`cwnas-ai-configure` 会把 CWNAS 主配置改成：

```json
{
  "ecrag_config": {
    "base_url": "http://127.0.0.1:8082",
    "knowledge_base_root": "/var/lib/cwnas/edgecraftrag/workspace"
  },
  "vllm_config": {
    "base_url": "http://127.0.0.1:8086"
  }
}
```

含义：RAG 问答与知识库管理走 8082，纯模型对话直连 8086 的 OpenAI 兼容接口。CWNAS 网页端的知识库/问答功能就是在这些接口上做封装和权限控制，用户在网页里操作即可，不需要单独打开 ECRAG 界面。`knowledge_base_root` 必须与容器挂载的 workspace 路径一致，否则「按路径上传」会失败。

## 6. 日常运维

```bash
cd /opt/cwnas/edgecraftrag
docker compose --env-file .env -f vllm-compose.yaml ps
docker compose --env-file .env -f compose.yaml ps
docker logs -f langchain_agent          # vLLM
docker logs -f edgecraftrag-server
```

- **重启顺序**：先 `vllm-compose.yaml` 再 `compose.yaml`。`edgecraftrag-server` 依赖 `http://host.docker.internal:8086`，vLLM 没起来问答会直接失败。
- **显存与调度参数**：`GPU_MEMORY_UTIL`、`ZE_AFFINITY_MASK` 由 `.env` 提供，改完要重建容器；vLLM 侧还固定了 `sym_int4` 量化、`MAX_MODEL_LEN=8192`、`MAX_NUM_SEQS=1`、`BLOCK_SIZE=64`、`shm_size=32g`。
- **模型目录**：`/var/lib/cwnas/edgecraftrag/models` 以只读方式挂进容器（`/models`、`/home/user/models`）。换模型要同时改 `compose.yaml` / `vllm-compose.yaml` 里的 `LLM_MODEL`。
- **数据备份**：`data/`（Milvus、etcd、MinIO）和 `workspace/` 是真正的业务数据，备份时一起备；镜像和模型不需要备份，重装即可恢复。
- **模型补下载**：`sudo /usr/local/sbin/cwnas-edgecraftrag-model qwen-download` 会在 vLLM 镜像里调用 `hf download` 拉取 Qwen 权重（需要外网），离线环境不要用，直接用 ISO 自带的权重。

## 7. 排错

| 现象 | 原因与处理 |
| --- | --- |
| `Qwen is missing; run ~/setup-ai.sh` | 权重目录缺 `model.safetensors.index.json`，确认安装器已解压 Qwen 归档，或重跑 `~/setup-ai.sh` |
| `TCP port 8086 is already in use` | vLLM 用 host 网络，同端口被占；先停掉占用进程或已有容器 |
| 日志出现 `no Intel XPU is visible yet` | Level Zero 看不到 XPU；检查驱动、`/dev/dri`、BIOS 里是否启用显卡 |
| `vLLM was not ready within 90 minutes` | 首次加载 30B 模型确实很慢；看 `docker logs langchain_agent`，常见是显存不足或 XPU 亲和掩码指到了不存在的设备 |
| `EdgeCraftRAG services were not ready within 10 minutes` | `docker compose ps` 看谁没起；`milvus-standalone` 常见于数据目录权限或磁盘空间问题（首次启动带 90 秒 `start_period`） |
| Milvus 反复重启 | 看 `docker logs milvus-standalone`；etcd 数据目录权限必须是 0700，且不能被其它进程占用 |
| 网页能开但问答报错 | 先确认 `curl http://127.0.0.1:8086/v1/models` 正常，再确认 `docker logs edgecraftrag-server` 里的 vLLM 地址 |
| 上传路径预览失败 | 路径不在 `/var/lib/cwnas/edgecraftrag/workspace` 之下，或 CWNAS 的 `knowledge_base_root` 与实际挂载不一致 |
| MinIO 控制台登录失败 | 默认凭据是 `minioadmin` / `minioadmin`，仅内网使用，未暴露到外网 |

## 8. 脚本用法

### 8.1 cwnas-edgecraftrag-bootstrap

```text
usage: cwnas-edgecraftrag-bootstrap {all|images|start}
```

最多带一个参数，缺省为 `all`；多给参数或不认识的参数会打印上面的 usage 并退出 1。

| 参数 | 行为 |
| --- | --- |
| `images` | 校验并导入 7 个镜像、按固定 ID 打 tag、删除 15.3 GB 归档；同时生成/刷新 `.env`；**不启动任何服务**，状态写为 `STATUS=images_ready` |
| `start` | 刷新 `.env` → 校验 Qwen → 检查端口 → 起 vLLM（最长等 90 分钟）→ 起全栈（最长等 10 分钟）→ 写 `STATUS=ready` |
| `all`（默认） | 上述两段依次执行，即完整的 `images` + `start` |

关于重复执行：

- 镜像 ID 一致时跳过导入；归档已被删则整个导入段跳过，但会校验镜像是否仍在。
- `start` 会无条件重新 `docker compose up -d`，是**推荐的恢复手段**：容器异常退出、改了 compose 或 `.env` 后直接重跑。
- 端口占用检查只在「ECRAG 自己的容器都不存在」时执行，所以服务已在运行时重启不会被自己的端口判定为冲突。
- 依赖 `docker`、`ss`、`ip`、`getent`、`jq` 等命令，必须在真实宿主系统里跑，不要在容器内执行。

### 8.2 cwnas-edgecraftrag-model

```text
usage: cwnas-edgecraftrag-model {status|qwen-verify|qwen-download}
```

必须且只能带一个子命令，否则打印 usage 并 `exit 2`。

| 子命令 | 行为 |
| --- | --- |
| `status` | 逐个检查 `BAAI/bge-m3-int8`、`BAAI/bge-reranker-large-int8`、`Qwen/Qwen3-Coder-30B-A3B-Instruct`，输出 `ready` 或 `missing` |
| `qwen-verify` | 只校验 Qwen，要求 `config.json` 与 `model.safetensors.index.json` 同时存在，缺任一即 `exit 1` |
| `qwen-download` | 用 `intel/llm-scaler-vllm` 镜像里的 `hf` 工具下载 Qwen 权重到模型目录。**需要外网**，离线环境不要用；ECRAG 镜像尚未导入时会直接报错退出 |

### 8.3 `.env` 变量

`/opt/cwnas/edgecraftrag/.env` 由 bootstrap 自动生成，compose 通过 `--env-file` 读取：

| 变量 | 来源/取值 | 影响 |
| --- | --- | --- |
| `HOST_IP` | 默认路由出口地址（`ip route get 1.1.1.1`，回退 `hostname -I`） | 写进 compose 的 `MEGA_SERVICE_HOST_IP`、`PIPELINE_SERVICE_HOST_IP`，以及状态文件里的 `UI_URL` |
| `VIDEO_GID` / `RENDER_GID` | `getent group video` / `render` | 容器 `group_add`，决定容器内能否访问 `/dev/dri` |
| `ZE_AFFINITY_MASK` | 优先 B60（PCI `0xe211`）对应的 Level Zero `device_id`，否则第一个可见 XPU | 决定 vLLM 用哪块 XPU 推理 |
| `GPU_MEMORY_UTIL` | 检测到 B60 为 `0.85`，否则 `0.60` | vLLM 的显存占用比例 |

改这些值后要重建容器（`docker compose --env-file .env -f vllm-compose.yaml up -d` 等）才生效；只改 `.env` 不会影响已在运行的容器。

另外，`cwnas-ai-configure` 负责把 CWNAS 主配置里的 `ecrag_config`（`base_url`、`knowledge_base_root`）与 `vllm_config.base_url` 指向本机服务，环境变量用法见教程二 7.2。

### 8.4 设备控制台脚本

`/usr/local/sbin/cwnas-console-status`（由 `cwnas-console.service` 在 `tty2` 上运行，封装脚本是 `/usr/local/sbin/cwnas-console`，有 `/dev/fb0` 时用 fbterm 渲染中文）每 5 秒刷新，内容来自：

- `systemctl is-system-running`、`systemctl is-active cwnas.service`；
- `/var/lib/cwnas/edgecraftrag/bootstrap.env` 的 `STATUS`：**不是 `ready`** 就显示「本地 AI 服务尚未完成安装 / 联网后以 root 运行：`~/setup-ai.sh`」，也就是 ECRAG 的就绪状态是整机 AI 是否装好的唯一判据；
- 网口状态、网页管理地址 `https://<IP>:9081/web`。

机身屏幕（或 `Alt+F2`）看到的提示直接对应这套脚本，排查「客户说 AI 没装好」时先看这个状态文件。

### 8.5 组合命令

```bash
# 只导入镜像（不起服务），适合先腾出导入时间
sudo /usr/local/sbin/cwnas-edgecraftrag-bootstrap images

# 模型体检
/usr/local/sbin/cwnas-edgecraftrag-model status

# 起服务 / 恢复服务
sudo /usr/local/sbin/cwnas-edgecraftrag-bootstrap start

# 看状态与日志（注意 compose 需要 --env-file）
cd /opt/cwnas/edgecraftrag
docker compose --env-file .env -f vllm-compose.yaml ps
docker compose --env-file .env -f compose.yaml ps
docker logs --tail 100 -f langchain_agent

# 确认推理端真的可用
curl -sS http://127.0.0.1:8086/v1/models | head -c 300
```

## 9. 卸载与重装

```bash
cd /opt/cwnas/edgecraftrag
docker compose --env-file .env -f compose.yaml down
docker compose --env-file .env -f vllm-compose.yaml down
sudo rm -rf /var/lib/cwnas/edgecraftrag/data      # 删知识库数据，按需
# 保留 /var/lib/cwnas/edgecraftrag/models 可省去重新解压 64 GB 模型
```

重装镜像需要把 `edgecraftrag-images.tar`（含 `.sha256`）放回 `/var/lib/cwnas/bootstrap/edgecraftrag/`，再执行 `cwnas-edgecraftrag-bootstrap images` 与 `start`。
