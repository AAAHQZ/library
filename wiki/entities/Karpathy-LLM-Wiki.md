---
title: "Karpathy LLM Wiki"
tags:
  - Karpathy
  - 知识管理
  - Markdown
  - llm-wiki
type: entity
confidence: EXTRACTED
---

# Karpathy LLM Wiki

Andrej Karpathy 在 2026 年 4 月分享的 LLM 知识管理工作流。获得 1600 万次浏览、5000+ GitHub Star。

## 核心架构

```
project/
├── raw/      ← 原始材料，只读，永不修改
├── wiki/     ← LLM 编译产物：概念页、实体页、索引、日志
│   ├── index.md  ← 所有文章的目录和摘要
│   └── log.md    ← 变更时间线
└── outputs/  ← 查询和分析的输出
```

## 设计哲学

- **Obsidian 是 IDE，LLM 是程序员，wiki 是 codebase** — 人类负责投喂高质量源材料和提出好问题；LLM 负责写作、交叉引用、分类和一致性检查
- **知识复利** — 回答 query 时综合多篇文章，产生的综合洞察写回 wiki 成为新页面
- **定期 lint** — LLM 扫描整个 wiki，找出矛盾、补缺失页面、发现有趣连接
- **绕开 RAG 的 chunking 缺陷** — LLM 在 ingest 阶段读完原文，写结构化的百科式页面；查询时加载已综合的知识，不是原始碎片

## 成功要素

Markdown 的几个特性踩中了当前技术节点：<!-- confidence: EXTRACTED -->

| 属性 | 重要性 |
|------|--------|
| 可移植 | 纯文本在任何编辑器里打开 |
| 可检视 | cat/grep/diff 直接操作，不需要查询语言 |
| 可版本化 | Git 原生支持 |
| 本地优先 | 数据在用户手里，无供应商锁定 |
| 缓存友好 | 稳定的文本前缀对 KV cache 命中率极高，缓存 vs 未缓存成本差 10 倍 |

## 规模验证

Karpathy 自己的研究 wiki 在没有手动写一个字的情况下积累到约 100 篇文章、40 万字——超过大多数博士论文的篇幅。<!-- confidence: EXTRACTED -->

## 与 Agent 记忆的差异

Karpathy 的系统优化目标是人，不是 Agent。当消费者变成全天候运行的自主 Agent 时，会出现五个断裂点：事实级检索缺失、token 预算约束、记忆漂移、排序困难、高频写入。<!-- confidence: INFERRED -->

详见 [[2026-05-06-三大Agent平台收敛到Markdown方案]]。

## 相关素材

- [[2026-05-06-三大Agent平台收敛到Markdown方案]] — 对 Karpathy 方案的深度分析
- [[2026-04-20-借LLMwiki聊聊知识管理]] — llm-wiki 方法论讨论

## 标签

#Karpathy #知识管理 #Markdown #llm-wiki #Obsidian
