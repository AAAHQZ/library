---
title: "Memory Store"
date: 2026-05-06
tags:
  - AI Agent
  - 记忆
  - 并发控制
type: entity
confidence: EXTRACTED
---

# Memory Store

> 跨 Session 的持久化记忆存储，采用乐观并发控制（OCC）。

## 特性

- 单条上限 100KB
- 一个 Session 最多挂 8 个 Memory Store
- 通过内置 memory_read / memory_write 等工具自动读写

## 并发控制机制

采用数据库界的 OCC（乐观并发控制）范式：
- 创建时要求 precondition={"type": "not_exists"} 防误覆盖
- 编辑时要求传入基于原内容的 SHA-256 哈希值
- 远端哈希与本地一致时更新才生效，否则抛 409 memory_precondition_failed

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
