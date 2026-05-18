---
title: "状态机视角下的Agent Runtime"
type: source
tags:
  - [Agent Runtime][状态机][系统架构][控制流]
source: "[[raw/wechat/2026-05-14-状态机视角下的_Agent_Runtime.md]]"
date: 2026-05-14
confidence: EXTRACTED
---

# 状态机视角下的Agent Runtime

> 原文链接：[[raw/wechat/2026-05-14-状态机视角下的_Agent_Runtime.md]]

## 基本信息

- **来源**：微信公众号
- **日期**：2026-05-14
- **主题**：Agent Runtime
- **重要性**：high

## 核心观点

从状态机角度分析Agent Runtime的控制流。核心观点：Agent runtime的本质不是单次LLM调用，而是不断接收事件、更新状态、决定下一步动作的循环系统。状态机四要素：状态、事件、状态转移、行动。核心循环：WaitingLLM -> WaitingTool -> WaitingLLM（tool-calling Agent的核心控制流）。状态机视角价值：更容易实现、更容易调试、更容易加功能（权限确认、中断、重试、上下文压缩）。

## 关键实体

[[baby-agent]], [[蒋炎岩]], [[Agent Runtime]]

## 相关素材

- [[Harness-Engineering]] — Harness工程核心概念
- [[Agentic-CI-CD]] — Agent驱动CI/CD变革
