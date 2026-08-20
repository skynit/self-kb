---
date: 2026-07-14
tags: [opencode, docker, 容器]
category: docker
---

# Docker

> 来源：OpenCode 历史会话 | ~5 条

## Docker 基础使用

```bash
# 安装后把用户加入 docker 组，免 sudo
sudo usermod -aG docker $USER
newgrp docker

# 常用命令
docker run <image>
docker ps
docker stop <container>
docker compose up -d
```

## docker compose vs docker-compose

| 版本 | 命令 | 说明 |
|------|------|------|
| V1 (旧) | `docker-compose` | 独立 Python 二进制 |
| V2 (新) | `docker compose` | Docker CLI 的内嵌子命令（插件） |

报错 `docker compose: unknown command` → 版本太旧，升级 Docker 或回退用 `docker-compose`。

## TLS connect error

```
TLS connect error downloading docker-compose.yml
```

原因：
- 网络代理拦截
- 防火墙阻挡
- 自签名证书问题

解决：
```bash
# 检查代理
echo $HTTPS_PROXY
echo $HTTP_PROXY

# 直接 curl 测试
curl -v https://raw.githubusercontent.com/.../docker-compose.yml
```

## Docker CPK

容器打包格式，将应用及依赖打包为可移植镜像。类似于 Docker Image 的概念。

## debian 容器大小

基础镜像 `debian:latest` 约 100MB（压缩后约 40MB）。
`slim` 版本更小（约 30MB 压缩后）。

## 在 Linux 上使用 Docker

```bash
# Arch
pacman -S docker docker-compose
systemctl enable docker
systemctl start docker
sudo usermod -aG docker $USER
```
