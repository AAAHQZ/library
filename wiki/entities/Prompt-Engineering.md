---
title: "Prompt-Engineering"
date: 2026-05-08
tags:
  - 概念
  - 工程实践
  - LLM
  - AI Agent
type: entity
confidence: INFERRED
source: 自然语言不适合编程Dijkstra半个世纪前的判断今天依然成立
aliases: ["提示工程", "Prompt Engineering"]
---

# Prompt-Engineering（提示工程）

> 通过精心设计输入文本引导 LLM 产生预期输出的工程实践。

## 本质

本文的核心洞察：Prompt Engineering 并非真正「用母语与机器对话」，而是在**设计一种临时性的、私有的形式化语言**。

所有成熟的 Prompt 实践都在做同一件事——把自然语言「变回」形式化的东西：

- XML 标签包裹输入 → 重建语法边界
- JSON Schema 约束输出 → 重建形式结构
- Few-shot examples → 压缩歧义空间
- System Prompt → 隐性形式化契约
- Chain-of-Thought → 推理路径结构化

## 常见技术

- **System Prompt** — 系统级指令
- **Few-shot Learning** — 示例引导
- **Chain-of-Thought (CoT)** — 思维链提示
- **XML Tagging** — XML 标签结构化
- **Schema / Format Binding** — 格式约束
- **Role Prompting** — 角色设定

## 所需核心能力

- **形式化思维** — 将需求转化为精确指令（比文采更重要）
- **界面意识** — 理解 Prompt 是窄接口，需要收窄歧义
- **迭代调试** — 通过 eval 持续优化

## 局限与批评

- 复杂的 Prompt 动辄数千 token，本质是设计临时性的形式化语言
- 「自然语言」的幻觉让初学者低估了写 Prompt 的难度
- 写 Prompt 的核心能力不是文采，是形式化思维

## 相关实体

- [[形式化思维]] — Prompt Engineering 所需的核心能力
- [[形式符号]] — 提示工程中使用的形式化工具
- [[界面宽度]] — 解释为什么需要 Prompt Engineering
- [[Structured-Output]] — 输出端形式化
- [[Harness-Engineering]] — 从 Prompt 到 Harness 的演进
- [[Context-Engineering]] — 上下文工程，与 Prompt Engineering 紧密相关

## 相关素材

- [[2026-05-10-Anthropic工程师的HTML用法|sources/2026-05-10-Anthropic工程师的HTML用法]] — HTML 作为 AI 输出的 Use Case 提示词模板
