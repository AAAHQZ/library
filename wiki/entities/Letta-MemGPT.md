---
title: "Letta / MemGPT"
tags:
  - AI Agent
  - 记忆
  - Agent Runtime
  - 有状态 Agent
type: entity
confidence: EXTRACTED
---

# Letta（原名 MemGPT）

Letta（原 MemGPT）是一个有状态 Agent 运行时系统，代表"Agent 自主管理记忆"路线的最远探索。已在生产环境部署超过 100 万个有状态 Agent。

## 三层记忆模型

Letta 借用操作系统的隐喻，将 Agent 记忆分为三层：

| 层次 | 类比 | 说明 |
|------|------|------|
| **Core Memory** | RAM | 始终在上下文中的小块内存，存放 persona、用户事实、agent 配置。建议上限 50K 字符、20 个 block。每次推理都消耗 token |
| **Recall Memory** | Cache | 数据库中存储的完整对话历史，agent 通过语义搜索或时间索引检索。不消耗固定 token，按需加载 |
| **Archival Memory** | Disk | 向量数据库中的长期存储，容量不受限。agent 通过工具调用写入和检索 |

## 核心创新

Agent 通过工具调用自主管理自己的记忆：
- `core_memory_append` / `core_memory_replace` — 修改核心记忆
- `memory_rethink` — 重新思考记忆策略
- `archival_memory_search` — 检索归档记忆

这是"记忆作为操作系统层"的思路——agent 是自己的内存控制器。<!-- confidence: EXTRACTED -->

## 三层分类法

Letta 的架构体现了行业共识的 Agent 记忆三层分类：
- **情景记忆（Episodic）** — 对话日志、工具调用轨迹
- **语义记忆（Semantic）** — 累积的事实和知识
- **程序记忆（Procedural）** — 操作规则和工作流

## 相关素材

- [[2026-05-06-三大Agent平台收敛到Markdown方案]] — 详细架构分析
- [[Agent-Memory框架]] — 归类为"Agent Runtime / Agent OS"

## 标签

#AI-Agent #记忆 #Agent-Runtime #有状态-Agent
