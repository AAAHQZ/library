---
title: "三大 Agent 平台独立收敛到同一个 Markdown 方案"
date: 2026-05-06
tags:
  - Agent 记忆
  - Markdown
  - Karpathy
  - Mem0
  - Letta
  - Agent-Memory框架
type: source
confidence: EXTRACTED
---

# 三大 Agent 平台独立收敛到同一个 Markdown 方案——这不是最优解，只是最容易走通的路

> 原文链接：https://mp.weixin.qq.com/s/XA6OTtbsnfCbEr0Jbn0Uhw

## 基本信息

- **作者**：微信公众号
- **日期**：2026-05-06
- **字数**：~10,000 字
- **主题**：Agent 记忆系统架构分析

## 核心观点

### 一、Karpathy LLM Wiki 的成功原因

Andrej Karpathy 的 LLM Wiki 工作流在 2026 年 4 月获得 1600 万浏览、5000+ Star。其核心架构只有三层：

```
project/
├── raw/      ← 原始材料，只读
├── wiki/     ← LLM 编译产物（概念页、实体页、索引、日志）
└── outputs/  ← 查询和分析输出
```

成功原因：**Markdown 的几个特性踩中了当前技术节点**——可移植、可检视、可版本化、本地优先、**缓存友好**（稳定的文本前缀对 KV cache 命中率极高，缓存 vs 未缓存成本差 10 倍）。

Karpathy 的心智模型：**"Obsidian 是 IDE，LLM 是程序员，wiki 是 codebase。"**

### 二、三个平台的趋同进化

Manus（todo.md）、Claude Code（分层的 CLAUDE.md + MEMORY.md）、OpenClaw（MEMORY.md + 日期命名的记忆文件）——三个独立平台收敛到同一个 Markdown 文件记忆方案。

但这只是当前约束下最容易走通的路，不是最优解。<!-- confidence: INFERRED -->

### 三、"人的知识"和"Agent 的记忆"是两个不同问题

| 维度 | 人类知识系统 | Agent 记忆系统 |
|------|------------|---------------|
| 优化目标 | 可读性、可浏览、可反思 | 检索速度、token 效率、状态一致性 |
| 交互频率 | 每天几次到几十次 | 每天数百到数千次 |
| 检索粒度 | 页面级 | 事实级 |
| 更新节奏 | 偶尔手动触发 | 每次任务后自动更新 |
| 冲突处理 | 人类判断 | 需要自动化规则 |

### 四、Markdown 在 Agent 规模下的五个断裂点

1. **Agent 需要的是事实，不是页面** — 检索单元是页面不是三元组
2. **Token 预算是硬约束** — Full-context 每次 ~26,000 token，p95 延迟 17.12 秒
3. **记忆漂移是可靠性问题** — Markdown 没有内置时间维度，过期信息与最新信息共存
4. **排序比存储难** — 平面文件没有置信度、重要性、新鲜度等元数据
5. **高频写入改变了一切** — Agent 每次任务、对话、工具调用后都更新记忆

Benchmark 数据（LOCOMO）：

| 方案 | 准确率 (LLM Score) | p95 延迟 | Token/查询 |
|------|----:|------:|------:|
| Full-context | 72.9% | 17.12s | ~26,000 |
| Mem0g（图增强）| 68.4% | 2.59s | ~1,800 |
| Mem0（向量）| 66.9% | 1.44s | ~1,800 |
| RAG | 61.0% | - | - |
| OpenAI Memory | 52.9% | - | - |

### 五、Agent 记忆的分层架构

行业共识的三种记忆类型：
- **情景记忆（Episodic）** — 带时间锚定的事件记录
- **语义记忆（Semantic）** — 累积的知识和事实
- **程序记忆（Procedural）** — 操作规则和工作流

Letta（MemGPT）的三层模型：
- **Core Memory（类 RAM）** — 始终在上下文中的小块内存（<50K 字符）
- **Recall Memory（类 Cache）** — 数据库中的对话历史，按需检索
- **Archival Memory（类 Disk）** — 长期存储，容量不受限

Letta 已在生产环境中部署超过 100 万个有状态 Agent。<!-- confidence: EXTRACTED -->

### 六、混合架构：Markdown 作为界面，结构化存储作为底层

- **给人的层用 Markdown** — CLAUDE.md、AGENTS.md、笔记、报告
- **给 Agent 的层用结构化存储** — SQLite/向量库/图库

## 与其他素材的关联

- 与 [[Agent-Memory框架]] 高度相关——本文提供了完整的 benchmark 数据和分层架构分析
- 与 [[Hermes-Agent]] 相关——Hermes 的记忆系统也是同类方案
Karpathy 的 LLM Wiki 方法论是 [[AI-Agent]] 知识库的核心构建方式。详见 [[Karpathy-LLM-Wiki]]。
- [[MemOS]] 和 [[Memory-Store]] 是这篇文章提到的 Markdown 记忆方案的实例

## 原文精彩摘录

> "Karpathy 的真正贡献不是发明了一种工具，而是帮整个社区把注意力从'生成'转向了'积累'。第一代 AI 帮我们生成答案；下一代必须维持上下文。"

> "2026 年最被低估的技术瓶颈不是模型能力，是记忆基础设施。"

> "Markdown 作为界面，结构化记忆作为底层。人通过 Markdown 文件审阅和编辑 agent 的知识；agent 通过结构化存储执行高效的选择性检索。"

## 关键概念

- [[Agent-Memory框架]] — 本文提供了 Mem0 / Zep / Letta 的详细 benchmark 对比
- [[Mem0]] — 向量记忆方案，LOCOMO 准确率 66.9%
- [[Letta-MemGPT]] — Agent 自主管理记忆，Core/Recall/Archival 三层
- [[Agent记忆分层]] — Episodic / Semantic / Procedural 三层分类
