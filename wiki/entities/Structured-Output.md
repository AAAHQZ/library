---
title: "Structured-Output"
date: 2026-05-08
tags:
  - 概念
  - 技术实践
  - Prompt Engineering
  - LLM
type: entity
confidence: INFERRED
source: 自然语言不适合编程Dijkstra半个世纪前的判断今天依然成立
aliases: ["结构化输出", "JSON Mode", "Structured Output"]
---

# Structured-Output（结构化输出）

> 让 LLM 输出 JSON 等结构化格式而非自由文本的技术实践，是 Prompt Engineering 中「在输出端重建形式结构」的关键手段。

## 核心思想

让 LLM 输出结构化的 JSON、XML 或按 Schema 约束的格式，而非自由文本。表面上是限制模型，实际上是在自然语言编程的伪装下重建形式化约束。

## 与 Dijkstra 界面宽度理论的关系

Structured Output 是 Dijkstra「窄接口」理论在 LLM 时代的直接应用：

- **输出端收窄**：约束模型输出格式，减少歧义
- **可编程性**：结构化输出可直接被下游系统消费
- **错误边界**：通过格式校验提前捕获异常输出

## 常见技术

- JSON Mode / JSON Schema 约束
- 正则表达式约束
- Enum 枚举限制
- 类型系统（TypeScript interfaces）
- Constrained Decoding（受限解码）

## 相关实体

- [[形式符号]] — 结构化输出的理论基础
- [[Prompt-Engineering]] — 所属领域
- [[Harness-Engineering]] — 工程化应用
- [[界面宽度]] — 理论解释
