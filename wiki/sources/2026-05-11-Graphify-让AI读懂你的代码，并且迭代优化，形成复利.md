---
title: "Graphify-让AI读懂你的代码，并且迭代优化，形成复利"
type: source
tags:
  - Graphify
  - 知识图谱
  - Codebase-Indexing
  - Tree-sitter
  - Claude-Code
  - Skill
  - Leiden算法
  - AST分析
source: "[[raw/wechat/2026-05-11-Graphify-让AI读懂你的代码，并且迭代优化，形成复利.md]]"
date: 2026-05-11
confidence: EXTRACTED
---

# Graphify - 让 AI 读懂你的代码，并且迭代优化，形成复利

> 原文链接：https://mp.weixin.qq.com/s/loeB5ocZhq715jbjU_VLTA
> 作者：小松鼠（AI 时代学习者，第 70 篇 AIGC 文章）

## 基本信息

- **字数**：6180 字
- **图片**：6 张
- **主题**：Graphify 工具——用知识图谱让 AI 一次性理解整个代码库，支持迭代优化和复利效应
- **标签**：Graphify、知识图谱、Codebase-Indexing、Tree-sitter、Claude-Code、Skill、Leiden算法

## 核心内容

### 痛点：AI 怎么理解代码？

传统 RAG 的问题：
1. **每次问都要全部读一遍**——浪费时间
2. **文档和代码之间的关联 AI 无法积累**——每次重新建立
3. **文件多了上下文塞不下**——60 文件、600 文件时无法处理

### 解决思路：知识图谱

**知识图谱 = 节点 + 边**
- 节点：任何概念（类、函数、文档、名词）
- 边：关系（"A 调用 B"、"A 继承了 B"、"A 和 B 讨论同一件事"）

就像地铁图——从 A 站到 B 站不需要记住整张图，只需查哪条线路直接相通。

### Graphify 工作流

**安装**：
```bash
pip install graphifyy
graphify claude install  # 作为 Skill 插件安装到 Claude Code
```

**构建图谱** `/graphify .`：
1. 检测文件（代码、文档、图片）
2. 提取关系
   - 代码：AST 分析（零 LLM 消耗）
   - 文档/图片：大模型分析
3. 构建图谱（节点 + 边）
4. 社区检测（Leiden 算法）
5. 生成报告（graph.html、GRAPH_REPORT.md、graph.json）

**查询**：
```bash
/graphify query "我的数据处理流程是怎样的？"    # 宽泛问题（BFS）
/graphify query "..." --dfs                    # 深度优先
/graphify path "Processor" "Storage"            # 最短路径
/graphify explain "Attention"                   # 解释节点
```

### 核心机制：双通道提取

| 通道 | 处理对象 | 工具 | LLM 消耗 |
|------|----------|------|----------|
| 通道 A：AST 确定性提取 | .py/.ts/.go 等代码 | tree-sitter 静态分析 | 零消耗 |
| 通道 B：语义提取 | .md/.txt/.pdf/.png | 大模型子代理并行 | 有消耗 |

**三级置信度**（Graphify 核心设计哲学）：
- `EXTRACTED`：源码直接有的，置信度 1.0
- `INFERRED`：合理推断的，置信度 0.6-0.9
- `AMBIGUOUS`：不确定的，标记留待人工审查

> "对自己'找到了什么'和'猜测了什么'保持诚实"

### 项目结构

```
graphify/
├── skill.md       # 技能插件定义（Claude Code 读取的入口）
├── extract.py     # 双通道提取（AST + 语义）
├── build.py       # 图构建 + 节点去重
├── cluster.py     # Leiden 社区检测
├── analyze.py     # 上帝节点分析、惊人连接
├── report.py      # GRAPH_REPORT 生成
└── export.py      # 多格式导出（HTML、JSON、Obsidian）
```

### 关键设计决策

**1. 诚实的三级置信度**
图谱是长期积累的查询工具，如果一开始把"猜的"当成"确定的"，后面所有查询结果都会跑偏。

**2. 无副作用的模块化设计**
每个阶段只做一件事，阶段之间通过字典传递数据，没有全局状态。支持增量更新：`--cluster-only` 从已有 graph.json 只跑社区检测。

**3. 反馈回路**
`/graphify query` 的问答结果自动保存到 `graphify-out/memory/`，下次 `--update` 时这些 Q&A 会被当作图谱节点提取出来——用得越多，图谱越"聪明"。

### 上帝节点与惊人连接

**上帝节点**：社区内连接数最多的节点，通常是核心抽象
- 示例：Node（92 条边）、Cursor（57 条边）、mk()（47 条边）

**惊人连接**：跨社区的边，揭示不明显的关联
- 示例：`onChangeVerbose() → saveGlobalConfig()`（跨目录）、`_resetPolicyLimitsForTesting() → stopBackgroundPolling()`（跨服务）

## 与知识库的关联

- [[Codebase-Indexing]] — Graphify 是代码索引/理解的具体工具实现
- [[Claude-Code]] — Graphify 作为 Claude Code 的 Skill 插件运行
- [[Skills]] — Graphify 本质是一个 skill.md 配置文件
- [[Tree-sitter]] — AST 确定性提取的核心工具
- [[知识图谱]] — Graphify 的核心抽象

## 原文精彩摘录

> AI 时代，信息不是不够，是太够了，要学会利用 AI 工具来思考，把散乱的信息，变成有用的知识。

> Graphify 最大的特点就是它不装。大模型推断出来的东西，它明确标出来是 INFERRED 还是 AMBIGUOUS，不包装成确定性答案。
