---
title: "Agent 记忆分层"
tags:
  - AI Agent
  - 记忆
  - 分层架构
type: entity
confidence: EXTRACTED
---

# Agent 记忆分层

行业共识的 Agent 记忆三层分类法，源自认知科学。

## 三种记忆类型

### 情景记忆（Episodic Memory）

记录具体发生过的事件——对话日志、工具调用轨迹、任务执行记录。特征是**带时间锚定**。回答"上次我们讨论这个话题时发生了什么"这类问题。

代表实现：[[Letta-MemGPT]] 的 Recall Memory。<!-- confidence: EXTRACTED -->

### 语义记忆（Semantic Memory）

存储累积的知识和事实——用户偏好、项目架构、技术决策。不关心事实在哪次对话中产生，只关心事实本身是否仍然为真。

[[Karpathy-LLM-Wiki]] 的 wiki 本质上是一个手工维护的语义记忆系统。<!-- confidence: INFERRED -->

### 程序记忆（Procedural Memory）

存储操作规则和工作流——代码规范、部署流程、审批策略。CLAUDE.md 和 AGENTS.md 就是典型的程序记忆。

## 存储架构对比

| 记忆类型 | 推荐存储 | 检索模式 |
|----------|---------|---------|
| Episodic | 数据库 + 语义索引 | 按时间/事件检索 |
| Semantic | 结构化存储（向量/图） | 精确查询 + 语义搜索 |
| Procedural | Markdown 文件 | 上下文注入 |

## 相关素材

- [[2026-05-06-三大Agent平台收敛到Markdown方案]] — 详细阐述三层分类法

## 标签

#AI-Agent #记忆 #分层架构
