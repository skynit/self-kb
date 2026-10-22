---
title: Intel 部件教程（二）：HeliconSearch 多模态检索
created: 2026-09-20
updated: 2026-09-20
type: project/tutorial
tags:
  - CWNAS
  - Intel
  - HeliconSearch
  - Qdrant
  - 向量检索
  - RAG
---

# Intel 部件教程（二）：HeliconSearch 多模态检索

同系列：
[[Intel部件教程一-SmarTune安装与使用|（一）SmarTune 资源平衡与监控]]、
[[Intel部件教程三-EdgeCraftRAG安装与使用|（三）EdgeCraftRAG 本地 RAG 问答]]。

## 1. 这个部件是干什么的

HeliconSearch 是 CWNAS Intel 版内置的**多模态语义检索与知识库服务**（版本 v2.3.7）。它能同时处理文档、图片、视频、音频：用 VLM 生成多模态摘要、用 CLIP 抽特征、用 OCR/ASR 提取文字、用 embedding + 重排做召回，最后按文本或图片发起检索。

它是 **3 个容器**的编排，跑在 bridge 网络 `helicon-net` 上：

| 服务 | 镜像 | 端口 | 说明 |
| --- | --- | --- | --- |
| `heliconsearch` | `heliconsearch_ubuntu2404:v2.3.7` | 5001 / 5003 | 合并镜像：容器内由 supervisor 同时拉起 agent（5001）与 VLMService（5003），VLM 走本地回环 |
| `hs-ui` | `heliconsearch_ui_ubuntu2404:v2.3.7` | 3000 | Next.js 前端，nginx 把 `/v1` 反代到 `heliconsearch:5001` |
| `qdrant` | `qdrant/qdrant:v1.12.6` | 仅内网 | 向量库，固定容器 IP `172.28.0.10`（应用侧会把 `qdrant.host` 当 `host:port` 解析，纯字母服务名会被误判成 URL scheme） |

关键坐标：

| 项目 | 值 |
| --- | --- |
| 离线归档 | `/var/lib/cwnas/bootstrap/heliconsearch/heliconsearch2.3.7-docker.tar`（约 13.8 GB） |
| 编排文件 | `/opt/cwnas/heliconsearch/docker-compose.yaml` |
| 就绪状态 | `/var/lib/cwnas/heliconsearch/bootstrap.env` |
| 账号口令 | `/var/lib/cwnas/credentials/heliconsearch.env` |
| 管理入口 | `http://<NAS-IP>:3000`（UI）、`http://<NAS-IP>:5001`（API） |

## 2. 前置条件

- **Docker 已安装并可用**，bootstrap 会最多等 120 秒直到 `docker info` 成功。
- **`/var/lib/docker` 至少 50 GiB 空闲**：脚本硬性检查，因为要 `docker load` 三个镜像（归档本身 13.8 GB，解压后的镜像更大）。
- **有 `/dev/dri`**：VLM 需要 Intel GPU，compose 已把 `/dev/dri` 透传给 `heliconsearch` 容器。
- 下列命令存在：`awk basename cat chmod curl df docker grep jq mkdir mv pv rm sha256sum sleep stat tar`（`pv` 用于显示导入进度，镜像里自带）。
- 端口 `3000`、`5001`、`5003` 空闲。
- 以 root 执行。

## 3. 安装步骤

### 3.1 随首次配置自动安装（推荐）

```bash
sudo ~/setup-ai.sh
```

总体脚本的第 3 阶段就是 `cwnas-heliconsearch-bootstrap`。也可以单独执行：

```bash
sudo /usr/local/sbin/cwnas-heliconsearch-bootstrap
```

### 3.2 脚本内部做了什么

1. **校验离线包**：对 13.8 GB 归档做 sha256，和 `.sha256` 文件比对；不匹配直接退出。
2. **检查空间**：`/var/lib/docker` 不小于 50 GiB。
3. **导入镜像**：从 tar 归档里按成员逐个 `tar -xOf | pv | docker load`，导入后逐个核对镜像 ID：

   | 镜像 | 镜像 ID |
   | --- | --- |
   | `heliconsearch_ubuntu2404:v2.3.7` | `sha256:9ec8f47c54e9d5f2d44c8d7179cd297a980528801494766517e624ae7386d8b1` |
   | `heliconsearch_ui_ubuntu2404:v2.3.7` | `sha256:d17ac3bdc9eb153e2b89b0ecc73b17a6d04fa26b5505856def824d66ea903fb1` |
   | `qdrant/qdrant:v1.12.6` | `sha256:89d9d35757eb57c62973c193c96b5b50ca73bbaff7fa282ef81f7fd155a3747d` |

   已存在且 ID 一致的镜像会跳过导入，所以重复执行是安全的。
4. **启动编排**：`docker compose -f /opt/cwnas/heliconsearch/docker-compose.yaml up -d`。
5. **等健康**：最多 10 分钟，要求 `qdrant`、`heliconsearch`、`hs-ui` 三个服务都在 running，且 `http://127.0.0.1:3000/` 可访问、`5001` 与 `5003` 有 HTTP 响应。
6. **建 CWNAS 账号与默认知识库**：用 `/var/lib/cwnas/credentials/heliconsearch.env` 里的账号口令调 `POST /v1/user/register`（返回码 `0` 或 `104`「已存在」都算成功），再 `POST /v1/user/login` 拿 `access_token`；随后 `POST /v1/kb/list`，若知识库数量为 0 就创建一个名为 `test` 的默认知识库。
7. **写状态并删归档**：生成 `/var/lib/cwnas/heliconsearch/bootstrap.env`（版本、归档 sha256、三个镜像 ID、可用 DRM 渲染节点、`CWNAS_ACCOUNT=ready`），然后删除 13.8 GB 的归档与校验文件释放磁盘。

> 第 7 步很关键：**归档只导入一次**。之后重跑脚本时走的是「归档已不存在」分支，此时只校验三个镜像 ID 是否仍在，不会重新导入。

### 3.3 账号与口令从哪里来

口令由 `cwnas-ai-configure` 在 AI 配置阶段生成：用户名固定 `admin`，口令是 24 字节随机数的十六进制（48 个字符），写到 `/var/lib/cwnas/credentials/heliconsearch.env`（0600），同时被写进 CWNAS 主配置的 `helicon_search_config.password`。查看方法：

```bash
sudo cat /var/lib/cwnas/credentials/heliconsearch.env
```

### 3.4 安装结果核对

```bash
cd /opt/cwnas/heliconsearch
docker compose ps                         # 三个服务都应是 running
curl -fsS http://127.0.0.1:3000/ >/dev/null && echo "ui ok"
curl -sS -o /dev/null -w '%{http_code}\n' http://127.0.0.1:5001/
curl -sS -o /dev/null -w '%{http_code}\n' http://127.0.0.1:5003/
cat /var/lib/cwnas/heliconsearch/bootstrap.env
```

`/var/lib/cwnas/heliconsearch/bootstrap.env` 正常内容形如：

```text
VERSION=v2.3.7
ARCHIVE_SHA256=60e1688cb6cfb28e72dcba344c7657fb76de8207f1473ad27c7cc8d8a5b63339
MAIN_IMAGE_ID=sha256:9ec8f47c...
UI_IMAGE_ID=sha256:d17ac3bd...
QDRANT_IMAGE_ID=sha256:89d9d357...
DRM_RENDER_NODES=/dev/dri/renderD128
CWNAS_ACCOUNT=ready
```

## 4. 使用

### 4.1 Web 界面

浏览器打开 `http://<NAS-IP>:3000`，用 `admin` + 上面的口令登录。界面里可以：

- 新建/删除/清空知识库，查看知识库统计；
- 上传文档、图片、视频、音频并入库，观察「VLM 摘要 → embedding → 可查询」的处理进度；
- 用文本或图片发起跨模态检索，看命中片段与相似度；
- 维护人物别名与人物注册等辅助信息。

入库方式有两类：**页面上传**（文件经接口进入知识库）和**引用 NAS 上的本地路径**。后者要求容器能看到宿主机的同一路径：

```yaml
# /opt/cwnas/heliconsearch/docker-compose.yaml 中对应的两处挂载（默认被注释）
- ${MEDIA_DIR:-/home/intel/workspace/dataset}:${MEDIA_DIR:-/home/intel/workspace/dataset}
```

也就是说，agent 收到宿主侧路径后会在容器内**按同一路径**打开文件，所以必须「宿主机路径:容器同路径」挂载媒体库根目录，并给 `heliconsearch`、`hs-ui` 两个服务都加上，改完 `docker compose up -d` 重建容器。

### 4.2 HTTP API

API 与 UI 同一个后端，入口 `http://<NAS-IP>:5001`，除注册/登录外都需要 `access_token`。

```bash
BASE=http://127.0.0.1:5001
USER=admin
PASS='<heliconsearch.env 里的 HELICON_PASSWORD>'

# 登录拿令牌
curl -sS -H 'Content-Type: application/json' \
  -d "{\"userid\":\"$USER\",\"password\":\"$PASS\"}" \
  $BASE/v1/user/login
# data.access_token 即后续要带的令牌

# 知识库列表
curl -sS -H 'Content-Type: application/json' \
  -d "{\"access_token\":\"$ACCESS_TOKEN\"}" \
  $BASE/v1/kb/list

# 新建知识库（与 CWNAS 建默认库时使用的字段一致）
curl -sS -H 'Content-Type: application/json' -d '{
  "access_token": "'"$ACCESS_TOKEN"'",
  "name": "test",
  "description": "CWNAS default knowledge base",
  "indexing_mode": 2,
  "embed_id": "BGE_M3",
  "vlm_id": "minicpmv26",
  "video_clip_id": "clip_pro",
  "audio_clip_id": "clip_pro",
  "ocr_id": "paddle",
  "asr_id": "whisper",
  "rerank_model": "BGE",
  "language": "zh"
}' $BASE/v1/kb/create

# 文本检索（字段为 snake_case）
curl -sS -H 'Content-Type: application/json' -d '{
  "access_token": "'"$ACCESS_TOKEN"'",
  "kb_id": "<知识库ID>",
  "message": "要检索的内容",
  "label": ["document", "image", "video"]
}' $BASE/v1/document/search
```

主要接口分组：

| 分组 | 接口 |
| --- | --- |
| 账号 | `/v1/user/register`、`/v1/user/login`、`/v1/user/logout` |
| 知识库 | `/v1/kb/list`、`/v1/kb/create`、`/v1/kb/update`、`/v1/kb/delete`、`/v1/kb/clear` |
| 文档 | `/v1/document/upload`、`/v1/document/search`、`/v1/document/searchbyimage`、`/v1/document/enumerate`、`/v1/document/statistics`、`/v1/document/delete` |
| 设置 | `/v1/settings/configuration`、`/v1/settings/autocheck`、`/v1/models/list` |
| 人物 | `/v1/person/register`、`/v1/person/alias` |

### 4.3 在 CWNAS 里使用

CWNAS 服务端通过主配置的 `helicon_search_config` 接入，`cwnas-ai-configure` 会在首次配置时写成：

```json
{
  "base_url": "http://127.0.0.1:5001",
  "address": "<NAS 的局域网地址>",
  "port": 5001,
  "user_name": "admin",
  "password": "<自动生成的口令>",
  "default_knowledge_base_name": "test"
}
```

之后在 CWNAS 网页管理端的知识库/智能检索页面里，用户不需要感知 HeliconSearch 的存在：CWNAS 用这套账号口令在后台完成登录、建库、上传、检索。`address` 字段决定了检索结果里回显的文件 URL，所以配置成局域网地址而不是 `127.0.0.1`。

## 5. 日常运维

```bash
cd /opt/cwnas/heliconsearch
docker compose ps                 # 看状态
docker compose logs -f heliconsearch
docker compose restart heliconsearch
docker compose down && docker compose up -d     # 整体重建
```

- **数据位置**：qdrant 向量数据在 Docker 命名卷 `qdrant_storage`，媒体库在宿主机被挂载的目录；`helicon_db`、`helicon_logs`、`helicon_tmp` 也都是命名卷。备份知识库时卷和媒体库要一起备。
- **密钥**：`NO_PROXY` 已显式排除 `127.0.0.1,localhost,172.28.0.10,qdrant`，避免宿主机代理环境变量把内网请求发到公司代理导致 403。加自定义代理时不要覆盖这个变量。
- **升级**：换成新版本的镜像归档后，替换 `/opt/cwnas/heliconsearch/docker-compose.yaml` 与三个镜像 tag，重新导入并 `up -d`。注意新版可能改动端口或容器名，bootstrap 脚本里的健康检查也要同步改。
- **重置账号**：删掉 `credentials/heliconsearch.env` 后重跑 `cwnas-ai-configure`（注意它带「已配置」标记文件会提前退出）与 `cwnas-heliconsearch-bootstrap`，会生成新口令并重新注册登录。

## 6. 排错

| 现象 | 原因与处理 |
| --- | --- |
| `archive checksum mismatch` | 归档被截断或被替换，重新从 ISO 介质复制；不要手工改 `.sha256` |
| `insufficient Docker space` | `/var/lib/docker` 不足 50 GiB，先清理无用镜像或扩容数据盘 |
| `image ID mismatch for ...` | 镜像导入不完整或本地已有同名旧镜像，`docker rmi` 后重跑 |
| `services did not become healthy within 10 minutes` | 看 `docker compose ps` 与 `docker compose logs heliconsearch`；常见是 `/dev/dri` 缺失导致 VLM 起不来 |
| 注册一直重试后报 `cannot register or log in` | 口令文件缺失或格式不对（必须是 48 位十六进制），检查 `credentials/heliconsearch.env` |
| UI 打不开但 5001 正常 | `hs-ui` 容器挂了，或者宿主 3000 端口被占 |
| 本地路径入库报文件不存在 | 媒体库没有按「宿主路径 = 容器路径」挂载，或只挂了一个容器 |
| 检索结果里的文件链接打不开 | `helicon_search_config.address` 不是客户端可达的地址 |

## 7. 脚本用法

### 7.1 系统侧脚本

| 脚本 | 参数 | 行为 |
| --- | --- | --- |
| `~/setup-ai.sh` | 无 | AI 总装入口，**第 3 阶段**调用 HeliconSearch bootstrap。仅 root 可跑，`flock -n /run/cwnas-ai-setup.lock` 防并发；失败打印阶段号，可反复执行 |
| `/usr/local/sbin/cwnas-ai-configure` | 无（环境变量见 7.2） | 生成 `admin` 账号与 48 位随机口令写入 `credentials/heliconsearch.env`，并把 `helicon_search_config` 写进 CWNAS 主配置 |
| `/usr/local/sbin/cwnas-heliconsearch-bootstrap` | 无 | 校验/导入镜像、起编排、等健康、建账号与默认知识库、删归档 |

`cwnas-heliconsearch-bootstrap` 有两条运行分支，理解它才能判断「重跑会发生什么」：

- **归档还在**：校验 sha256 → 检查 `/var/lib/docker` ≥ 50 GiB → 逐个导入三个镜像 → `docker compose up -d` → 等 10 分钟健康 → 注册/登录账号、按需建默认知识库 → 写状态文件 → **删除 13.8 GB 归档**。
- **归档已删**：只核对状态文件里的历史 sha256 与三个镜像 ID 是否仍然存在，不导入；任一镜像被删会直接报错 `bootstrap archive is gone and the ... image is missing`。

两条分支都会重新 `up -d` 并重新确认账号，所以**重复执行安全**；失败时退出码 1，stderr 会附带 `docker compose ps` 输出便于定位。脚本开跑前会检查这些命令是否齐全：`awk basename cat chmod curl df docker grep jq mkdir mv pv rm sha256sum sleep stat tar`，缺任何一个都会立刻报错——所以不要在精简环境或容器里执行。

### 7.2 cwnas-ai-configure 的环境变量

| 变量 | 默认值 | 说明 |
| --- | --- | --- |
| `CWNAS_CONFIG_FILE` | `/var/lib/cwnas/config/config.json` | CWNAS 主配置路径 |
| `CWNAS_AI_CONFIG_STATE_FILE` | `/var/lib/cwnas/ai-local-services-configured` | 「已配置」标记文件；存在时脚本直接退出 0 |
| `CWNAS_HELICON_CREDENTIAL_FILE` | `/var/lib/cwnas/credentials/heliconsearch.env` | HeliconSearch 账号口令文件 |
| `CWNAS_AI_HOST_IP` | 自动探测（`ip route get 1.1.1.1`） | 覆盖写进配置的对外地址 |
| `CWNAS_AI_CONFIG_RESTART` | `1` | 置 `0` 则改完配置不重启 `cwnas.service` |

```bash
# 只刷新 CWNAS 的 AI 端点配置，不动主服务
sudo CWNAS_AI_CONFIG_RESTART=0 /usr/local/sbin/cwnas-ai-configure

# 强制重新生成配置与口令（先删标记文件）
sudo rm -f /var/lib/cwnas/ai-local-services-configured
sudo rm -f /var/lib/cwnas/credentials/heliconsearch.env
sudo /usr/local/sbin/cwnas-ai-configure
```

### 7.3 组合命令

```bash
# 只重装/重启 HeliconSearch，不动其它部件
sudo /usr/local/sbin/cwnas-heliconsearch-bootstrap

cd /opt/cwnas/heliconsearch
docker compose ps                       # 三个服务
docker compose logs -f heliconsearch    # 跟日志
docker compose restart hs-ui            # 只重启前端
docker compose down && docker compose up -d   # 整体重建

# 一条命令确认服务 + 状态文件
docker compose ps --format '{{.Service}} {{.State}}'; cat /var/lib/cwnas/heliconsearch/bootstrap.env
```

## 8. 卸载与重装

```bash
cd /opt/cwnas/heliconsearch
docker compose down -v                       # -v 会一并删除知识库数据卷
docker rmi heliconsearch_ubuntu2404:v2.3.7 heliconsearch_ui_ubuntu2404:v2.3.7 qdrant/qdrant:v1.12.6
sudo rm -rf /opt/cwnas/heliconsearch /var/lib/cwnas/heliconsearch
```

重装需要重新拿到 13.8 GB 离线归档放到 `/var/lib/cwnas/bootstrap/heliconsearch/`（连同 `.sha256`，文件内容必须与安装器写入的 sha256 文件一致），再跑 `cwnas-heliconsearch-bootstrap`。
