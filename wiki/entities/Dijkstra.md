---
title: "Dijkstra"
date: 2026-05-08
tags:
  - 人物
  - 计算机科学
  - 编程语言
  - 形式化
type: entity
confidence: EXTRACTED
source: 自然语言不适合编程Dijkstra半个世纪前的判断今天依然成立
aliases: ["Edsger Dijkstra", "Edsger W. Dijkstra"]
---

# Dijkstra

> Edsger W. Dijkstra（1930-2002），荷兰计算机科学家，1972 年图灵奖得主，结构化编程先驱，EWD 系列备忘录作者。

## 核心贡献

- **结构化编程**（Structured Programming）—— 与 Hoare、Dahl 共同奠定结构化编程理论
- **Dijkstra 算法** —— 最短路径算法的发明者
- **EWD 系列备忘录** —— 撰写逾 1300 篇 EWD 备忘录，涵盖编程方法学、形式化验证、教育等
- **「GOTO 有害论」** —— 1968 年发表《Go To Statement Considered Harmful》

## 与 AI 时代的关系

### EWD 667：自然语言编程批判

1978 年，Dijkstra 在 EWD 667《On the foolishness of "natural language programming"》提出三层反驳：

1. **界面选择非零和**：自然语言是宽界面，两侧都要付出额外代价
2. **数学史经验**：形式符号替换自然语言推动了现代科学腾飞
3. **形式符号是特权**：拒绝让人说出表面通顺实则无意义的话

### 2016 年的现代回响

在 LLM 与 AI Coding Agent 时代，Dijkstra 的判断被重新审视：

- **战术上输了**：机器确实能听懂人话，LLM 实现了自然语言交互
- **战略上赢了**：本质难题没有消失，只是从编译器与程序员之间迁徙到 Prompt 与 Harness 之间

所有认真做事的团队都在想方设法把自然语言「变回」形式化结构——XML 标签、JSON Schema、Spec、Tool Schema 等。

### 三条预言级判断

| 预言 | 对应今天的现象 |
|------|---------------|
| 跨界面协作产生额外工作量，界面越宽两侧越忙 | Prompt Engineering 产业链 |
| 母语的自然本质是容易说出无意义的话 | LLM 幻觉问题 |
| 新文盲症 | 以为会打字就会写 Prompt 的认知偏差 |

## 相关实体

- [[EWD-667]] — 本文引用的核心文献
- [[自然语言编程]] — 被批判的核心概念
- [[形式符号]] — 形式化符号系统
- [[形式化思维]] — 本文提炼的关键能力
- [[界面宽度]] — 界面理论核心概念
- [[Harness-Engineering]] — 界面收窄的现代工程实践
- [[Spec驱动开发]] — 窄接口理论在 AI 时代的应用

## 参考文献

- E.W. Dijkstra, "On the foolishness of 'natural language programming'", EWD 667, 1978
- 原文：https://www.cs.utexas.edu/~EWD/transcriptions/EWD06xx/EWD667.html
- E.W. Dijkstra, "Go To Statement Considered Harmful", 1968
