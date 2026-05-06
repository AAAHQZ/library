---
title: "Harness"
date: 2026-05-06
tags:
  - AI Agent
  - 架构
  - Anthropic
type: entity
confidence: EXTRACTED
---

# Harness

> Agent 运行时的调度内核，负责调用模型进行推理并将工具调用意图路由到沙盒执行。

## 在 CMA 中的角色

Harness 是 CMA 四件套之一，作为调度内核存在。它与 Sandbox 解耦：
- Harness 负责调模型推理、路由工具调用意图
- Sandbox 是纯执行域，只负责跑代码、读写文件、执行计算
- Sandbox 崩溃被捕获为普通"工具调用错误"，模型可决定重试并无缝续跑

## 与 Sandbox 解耦的意义

就像 CPU、内存、磁盘分离成可独立替换的部件。Session 被定义为只追加的事件日志，类似内核日志，独立于模型物理上下文窗口之外持久化存储。

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
- [[2026-05-05-Anthropic的Harness哲学把Agent当牲口而非宠物|sources/2026-05-05-Anthropic的Harness哲学把Agent当牲口而非宠物]]
