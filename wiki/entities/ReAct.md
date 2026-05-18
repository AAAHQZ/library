---
title: "ReAct"
date: 2026-05-11
tags:
  - AI-Agent
  - Agent架构
  - 推理模式
type: entity
confidence: EXTRACTED
---

# ReAct

> ReAct = **Reason** + **Act**：让模型在行动前先进行推理的模式。

## 核心概念

ReAct 是 AI Agent 编排循环的核心模式，全称 Thought-Action-Observation（TAO）循环：

1. **Thought（思考）** — 模型分析当前状态，决定下一步行动
2. **Action（行动）** — 执行工具调用
3. **Observation（观察）** — 收集结果，反馈给模型

循环运转：整合提示词 → 调用 LLM → 解析输出 → 执行工具调用 → 反馈结果 → 重复，直到任务完成。

## 与其他模式对比

| 模式 | 描述 | 特点 |
|------|------|------|
| **ReAct** | 边推理边行动，灵活 | 成本较高，每步都调用模型 |
| **Plan-then-Act** | 先规划再执行 | 速度更快，但规划可能偏离实际 |
| **Zero-shot** | 直接行动 | 最快，但缺乏推理过程 |

## 在 Harness 中的地位

- 编排循环（Orchestration Loop）是 Harness 的"心脏"
- Anthropic 将其运行时描述为"笨循环"——所有智慧都在模型里，Harness 只负责管理回合切换
- ReAct 是"灵活但成本高"，"先规划后执行"是"速度更快"的替代方案

## 相关素材

- [[2026-05-11-深度拆解：AI_智能体_Harness_的构造【译】|深度拆解：AI 智能体 Harness 的构造【译】]]

## 关联实体

- [[Harness]] — 包含 ReAct 循环作为核心组件
- [[Harness-Engineering]] — Harness 工程涵盖 ReAct 编排
