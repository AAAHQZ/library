# OpenClaw

## 概述

OpenClaw 是一个开源的 AI Agent 框架，采用极简设计哲学。其底层 Agent Core（代号 Pi Agent）仅提供 4 个核心工具：Read、Write、Edit、Shell，体现了"代码即工具"的理念。

## 核心架构

### Pi Agent

Pi 是 OpenClaw 的底层引擎，采用 RPC 模式运行：

- **4 个核心工具**：Read、Write、Edit、Shell
- **极简主义**：把能砍掉的全砍掉，把剩下的做扎实
- **Unix 哲学**：通过 RPC 模式保持轻量

### OpenClaw 与 Pi 的关系

OpenClaw 是 Pi 的"上层建筑"：

- OpenClaw 负责多渠道接入、多 Agent 调度
- OpenClaw 负责"从哪里来、到哪里去"的路由逻辑
- Pi 专注于执行层的极简能力

## 记忆系统

OpenClaw 使用文件系统作为上下文，通过以下文件管理状态：

| 文件 | 用途 |
|------|------|
| `SOUL.md` | Agent 个性/角色定义 |
| `TOOLS.md` | 可用工具描述 |
| `MEMORY.md` | 长期记忆 |
| `USER.md` | 用户信息 |

数据存储路径：`~/.openclaw/workspace/`

## 与 Hermes Agent 的关系

OpenClaw 与 Hermes Agent 同为 AI Agent 框架，存在迁移路径：

- Hermes Agent 提供了 `openclaw-migration` 技能，支持从 OpenClaw 迁移到 Hermes
- 迁移内容包括：SOUL.md、记忆文件、命令白名单、用户技能、工作区资源等

### 迁移命令

```bash
hermes claw migrate              # 完整交互式迁移
hermes claw migrate --dry-run    # 预览迁移内容
hermes claw migrate --preset user-data   # 仅迁移用户数据
```

### 迁移详情

迁移脚本 `openclaw_to_hermes.py` 执行以下操作：

- 将 `SOUL.md` 导入 Hermes 主目录
- 将 OpenClaw `MEMORY.md` 和 `USER.md` 转换为 Hermes 记忆条目
- 合并 OpenClaw 命令审批模式到 Hermes `command_allowlist`
- 迁移兼容的消息设置（如 `TELEGRAM_ALLOWED_USERS`）
- 将 OpenClaw 技能复制到 `~/.hermes/skills/openclaw-imports/`

## 安全问题

### 2026 年安全事件

2026 年初 OpenClaw 等自主智能体框架爆发式增长，但"上帝模式"带来巨大安全风险：

- OpenClaw 可运行 Shell 命令、读写文件、执行脚本
- 近千个 OpenClaw 实例公开可访问，无任何身份认证
- 传统 IAM 在动态智能体面前失效

### Agent IAM 四大核心要素

针对 OpenClaw 安全事件的教训，Agent IAM 需要具备：

1. **身份传播**：跨渠道、跨会话的身份追踪
2. **无秘钥验证**：避免静态凭证泄露
3. **上下文感知**：基于当前上下文动态授权
4. **意图感知授权**：理解 Agent 意图后授权

## 技术集成

### llmfit 集成

OpenClaw 可作为 llmfit 的技能使用，提供硬件感知模型推荐：

```bash
./scripts/install-openclaw-skill.sh
cp -r skills/llmfit-advisor ~/.openclaw/skills/
```

### 支持的模型提供商

- Ollama
- vLLM
- LM Studio

## 相关页面

- [[2026-04-OpenClaw身份权限管理|sources/2026-04-OpenClaw身份权限管理]]
- [[2026-04-22-AI-Agent框架选型与工程实践|sources/2026-04-22-AI-Agent框架选型与工程实践]]
- [[2026-05-05-深度拆解HermesAgent的记忆系统|sources/2026-05-05-深度拆解HermesAgent的记忆系统]]
- [[2026-05-05-什么是pi下一代Agent架构|sources/2026-05-05-什么是pi下一代Agent架构]]
