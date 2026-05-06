---
title: "Agent Memory 框架"
tags:
  - AI Agent
  - 记忆
  - Embedding
  - 向量检索
type: entity
---

# Agent Memory 框架

> 为 LLM Agent 提供记忆能力的基础设施项目
>
> 相关素材：[[2026-05-06-三大Agent平台收敛到Markdown方案]]

## 定义

Agent Memory 框架是近年来兴起的一类 AI Agent 基础设施项目，旨在为 LLM Agent 提供记忆能力——解决"Agent 该如何记住东西"的问题。这类框架通常在底层数据库之上封装一套 `extract / store / retrieve / update` API，通过 episodic（情景记忆）、semantic（语义记忆）、reflection/procedural（反思/程序记忆）等分层架构来实现类人的记忆系统。

## 核心主张

- 通过多层记忆架构（episodic / semantic / procedural）模拟人类记忆
- 提供向量检索 + 全文检索 + 重要性打分 + 时间衰减等认知策略

## 2026 年 Benchmark 对比

| 方案 | 准确率 (LLM Score) | p95 延迟 | Token/查询 |
|------|:-----:|:--------:|:----------:|
| Full-context | 72.9% | 17.12s | ~26,000 |
| Mem0g（图增强）| 68.4% | 2.59s | ~1,800 |
| Mem0（向量）| 66.9% | 1.44s | ~1,800 |
| RAG | 61.0% | - | - |
| OpenAI Memory | 52.9% | - | - |

数据来源：LOCOMO benchmark。Full-context（全部记忆加载到上下文）准确率最高但 token 消耗比选择性方案高出 14 倍。<!-- confidence: EXTRACTED -->

## 主要类别

### 第一类：数据库套壳 SDK

代表项目：[[Mem0]]、LangMem、MemoryScope、SuperMemory

核心能力是在 PostgreSQL+pgvector 或 SQLite 上封装一套 API，把 episodic 和 semantic 分两张表，加重要性打分和时间衰减规则。

### 第二类：知识图谱 / 时序图谱构建器

代表项目：Graphiti、Cognee、Hindsight

bi-temporal knowledge graph、增量实体消歧、冲突检测与失效、hybrid 检索。

### 第三类：Agent Runtime / Agent OS

代表项目：[[Letta-MemGPT]]（MemGPT 改名）

最重的一类。Letta 采用 Core Memory（类 RAM）/ Recall Memory（类 Cache）/ Archival Memory（类 Disk）三层架构，Agent 通过工具调用自主管理记忆。已在生产环境中部署超过 100 万个有状态 Agent。<!-- confidence: EXTRACTED -->

## 行业共识：三层记忆分类

（来源：[[2026-05-06-三大Agent平台收敛到Markdown方案]]）

- **情景记忆（Episodic）** — 带时间锚定的事件记录
- **语义记忆（Semantic）** — 累积的知识和事实
- **程序记忆（Procedural）** — 操作规则和工作流

## 混合架构趋势

Markdown 作为给人看的界面，结构化存储（向量/图/SQLite）作为给 Agent 的底层。CLAUDE.md / AGENTS.md 负责程序记忆，结构化存储负责语义和情景记忆。<!-- confidence: INFERRED -->

## 不同素材中的观点

- [[2026-05-06-三大Agent平台收敛到Markdown方案]]：Markdown 方案在个人研究者规模（<100 篇文章）下验证有效，但在 Agent 规模下会撞到天花板——事实级检索、token 预算、记忆漂移、高频写入是五个断裂点
- [[三分天下为什么AgentMemory框架是死路]]：认为记忆框架独立赛道不存在，技术壁垒会被模型吸收、存储层归回数据库

## 标签

#AI-Agent #记忆 #向量检索 #Agent-Runtime
