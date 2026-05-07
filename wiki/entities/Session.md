---
title: "Session（CMA）"
date: 2026-05-06
tags:
  - AI Agent
  - Claude
  - 持久化
type: entity
confidence: EXTRACTED
---

# Session（CMA）

> CMA 中带持久化状态的长周期载体，底层是只追加（append-only）的事件日志。

## 定义

Session 是 CMA 四件套抽象之一，相当于 Agent 与环境的"持续对话"。它的底层是无限制的只追加事件日志，独立于模型上下文窗口之外持久化存储。

## 核心特性

- **持久化**：Session 不会因为 Sandbox 崩溃或模型上下文窗口满而丢失状态
- **只追加（append-only）**：所有事件按顺序追加，不可修改，类似内核日志
- **独立于模型窗口**：Session 的持久化存储不在模型上下文窗口内，不受 token 限制
- **Sandbox 崩溃恢复**：Sandbox 崩溃后可通过 provision({resources}) 重建，Session 无缝续跑

## 意义

Session 实现了"将 Agent 的经验虚拟化"——它让 Agent 的工作记忆从临时的模型上下文窗口，升级为可持久化、可恢复的长期载体。

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
