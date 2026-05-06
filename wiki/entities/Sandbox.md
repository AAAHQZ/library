---
title: "Sandbox"
date: 2026-05-06
tags:
  - AI Agent
  - 安全
  - 隔离
type: entity
confidence: EXTRACTED
---

# Sandbox

> 隔离的代码执行域，Agent 在其中运行动态生成的代码、读写文件、执行 bash。

## 在 CMA 中的角色

Sandbox 是 CMA 中的纯执行域，与 Harness 解耦：
- 只负责跑代码、读写文件、执行计算
- 与 Harness 独立，崩溃后可通过 provision({resources}) 重建
- Vault/Proxy 架构确保凭证永不进入 Sandbox

## 安全设计

Sandbox 与真实凭证之间没有物理连通路径。所有外部调用通过专用 Proxy 发起，Proxy 从独立 Vault 取出凭证。

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
