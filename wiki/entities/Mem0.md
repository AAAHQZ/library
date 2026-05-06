---
title: "Mem0"
tags:
  - AI Agent
  - 记忆
  - 向量检索
type: entity
confidence: EXTRACTED
---

# Mem0

Mem0 是一个 AI Agent 记忆框架，采用向量检索实现长期记忆，在 LOCOMO benchmark 上达到 66.9% 准确率。

## 核心特性

- **语义相似度检索** — 通过向量嵌入实现记忆检索
- **选择性记忆方案** — 比 Full-context 节省 90% token（每次查询 ~1,800 token vs ~26,000）
- **低延迟** — p95 延迟 1.44 秒（vs Full-context 17.12 秒）
- **图增强版本（Mem0g）** — 引入图结构后准确率提升到 68.4%

## 性能数据

| 方案 | 准确率 | p95 延迟 | Token/查询 |
|------|:-----:|:--------:|:----------:|
| Mem0g（图增强）| 68.4% | 2.59s | ~1,800 |
| Mem0（向量）| 66.9% | 1.44s | ~1,800 |

## 定位

属于"数据库套壳 SDK"类记忆框架——在 PostgreSQL+pgvector 或 SQLite 上封装 API，技术上壁垒较低。<!-- confidence: EXTRACTED -->

## 相关素材

- [[2026-05-06-三大Agent平台收敛到Markdown方案]] — 详细 benchmark 对比

## 标签

#AI-Agent #记忆 #向量检索 #语义搜索
