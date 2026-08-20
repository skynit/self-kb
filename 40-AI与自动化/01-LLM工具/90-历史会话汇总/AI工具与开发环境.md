---
date: 2026-07-14
tags: [opencode, ai, llm, opencode, codex, claude]
category: ai-tools
---

# AI / LLM 工具与开发环境

> 来源：OpenCode 历史会话 | ~10 条

## OpenCode 配置

- 上下文指令文件路径：可在 `opencode` CLI 参数或项目级配置文件指定
- 会话数据：`~/.local/state/opencode/` 和 `~/.local/share/opencode/`
- prompt 历史：`~/.local/state/opencode/prompt-history.jsonl`

## Codex

- **502 Bad Gateway**：后端服务异常（upstream request failed），URL `http://192.168.100.66:8080/responses`
- **desktop-linux 更新**：
  ```bash
  yay -S codex-desktop-linux
  # 或手动下载最新 release
  ```
- 源码探索：codex-desktop-linux 项目结构

## Claude.ai 安装脚本

安装脚本报错：
- 网络/代理问题导致下载失败
- 检查 `HTTPS_PROXY` 设置
- `api.botcf.com/setup-codex-code.sh` 返回 HTML → 服务端配置错误

## DeepSeek / mimo-v2.5-pro

- DeepSeek 模型查询
- mimo-v2.5-pro 模型访问权限问题

## SKILL.md 403 Forbidden

下载 SKILL.md 文件 HTTP 403：
- 认证问题
- 权限不足
- 尝试带 token 下载

## Yaak API 客户端

锁屏密码设置位置：应用的偏好设置/安全设置（Preferences → Security）。

## 其他 AI 工具

- Sub2API：添加 GPT-5.6-SOL 信道配置
- ChatView chat-body 50% 尺寸自适应
