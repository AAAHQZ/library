---
title: "LangGraph"
date: 2026-05-11
tags:
  - AI-Agent
  - Agent框架
  - 状态图
  - LangChain
type: entity
confidence: EXTRACTED
---

# LangGraph

> LangGraph 是 LangChain 推出的用于构建有状态、多演员（Multi-Actor）应用的框架，将 Harness 建模为显式状态图。

## 核心概念

- 将 Harness 建模为**显式状态图**，强调对流程的精细控制
- 状态模拟为在图形节点中流动的**类型化字典**
- 在关键步骤进行"存档"（Checkpointing），支持中断恢复，甚至"时间旅行"式调试
- 将错误分为四类：临时性（带延迟重试）、模型可恢复、用户可修复、意外错误

## 相关素材

- [[2026-05-11-深度拆解：AI_智能体_Harness_的构造【译】|深度拆解：AI 智能体 Harness 的构造【译】]]

## 关联实体

- [[Harness]] — LangGraph 是 Harness 的一种实现方式
- [[Harness-Engineering]] — LangGraph 用于 Harness 工程
