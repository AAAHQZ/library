---
title: "Vault / Proxy 架构"
date: 2026-05-06
tags:
  - AI Agent
  - 安全
  - 凭证管理
type: entity
confidence: EXTRACTED
---

# Vault / Proxy 架构

> CMA 的凭证隔离方案，OAuth token 存放在独立 Vault，Sandbox 通过专用 Proxy 发起外部调用。

## 架构设计

- **Vault**：独立于 Sandbox 的凭证保险库
- **Proxy**：专用代理，接收与当前 Session 绑定的令牌，从 Vault 取出对应凭证，调用外部服务
- Sandbox 自始至终对任何凭证一无所知

## 安全意义

如果把 OAuth 令牌、数据库凭证直接塞进沙箱的环境变量，一次提示注入就能让模型写出 os.environ 把令牌吐出来。Vault/Proxy 架构确保沙箱与真实凭证之间没有物理连通路径。

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
