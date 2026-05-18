---
tags: [Agent Runtime, 状态机, 系统架构]
created: 2026-05-18
updated: 2026-05-18
sources: ["2026-05-14-状态机视角下的_Agent_Runtime.md"]
---

# Agent-Runtime

> Agent运行时的控制流系统，本质是不断接收事件、更新状态、决定下一步动作的循环

## 简介

状态机视角：四要素（状态、事件、状态转移、行动）。最小状态机：Initializing→Idle→WaitingLLM→WaitingTool。核心循环：WaitingLLM→WaitingTool→WaitingLLM（tool-calling Agent核心控制流）。扩展状态：WaitingApproval、Paused。价值：更容易实现、调试、加功能。

## 相关页面

- [[Harness-Engineering]]
