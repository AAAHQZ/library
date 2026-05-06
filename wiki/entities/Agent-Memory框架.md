# Agent Memory 框架

## 定义

Agent Memory 框架是近年来兴起的一类 AI Agent 基础设施项目，旨在为 LLM Agent 提供记忆能力——解决"Agent 该如何记住东西"的问题。这类框架通常在底层数据库之上封装一套 `extract / store / retrieve / update` API，声称通过 episodic（情景记忆）、semantic（语义记忆）、reflection/procedural（反思/程序记忆）等分层架构来实现类人的记忆系统。

## 核心主张

- 通过多层记忆架构（episodic / semantic / procedural）模拟人类记忆
- 提供向量检索 + 全文检索 + 重要性打分 + 时间衰减等认知策略
- 声称是 Agent 革命的核心基础设施和终局壁垒

## 主要类别

### 第一类：数据库套壳 SDK

代表项目：Mem0、LangMem、MemoryScope、SuperMemory

核心能力是在 PostgreSQL+pgvector 或 SQLite 上封装一套 API，把 episodic 和 semantic 分两张表，加重要性打分和时间衰减规则。技术上没有壁垒，产品上有一点用户心智。本质是**建表和 SQL 的封装**。

### 第二类：知识图谱 / 时序图谱构建器

代表项目：Graphiti、Cognee、Hindsight

比第一类更重——bi-temporal knowledge graph、增量实体消歧、冲突检测与失效、hybrid 检索。但这些项目的策略层会被模型吸收，存储层会归回数据库，独立赛道不成立。

### 第三类：Agent Runtime / Agent OS

代表项目：Letta（MemGPT 改名）

将 context window 当 RAM、外部存储当 Disk，让模型通过 tool call 在两者之间 swap。这是操作系统意义上的虚拟内存管理，本质是 **Agent Runtime**，应归入 Harness 赛道而非 Memory 赛道。

## 作者立场（核心观点）

文章认为 Agent Memory 框架是**死路**，核心论据有两条：

### 1. 产业结构上没有位置

终局是"三分天下"：
- **智力层** → 模型厂商（OpenAI、Anthropic 等）
- **驾驭层** → Harness（Claude Code、Cursor、Devin 等）
- **记忆层** → 数据库厂商（PostgreSQL）

Memory 框架是介于数据库和应用之间的中间件。终局一旦形成（模型厂商提供智力、Harness 管执行、数据库管记忆），中间商无处可去。

### 2. 方法论上站不住（The Bitter Lesson）

The Bitter Lesson（Sutton, 2019）指出：过去七十年 AI 领域反复上演——研究者把"对领域的精心理解"编码进系统，短期有效，长期必输给"让模型自己学"的通用方法。

Memory 框架硬编码的那些认知策略（什么信息值得记、记在哪一层、什么时候触发反思、怎么组合检索）都是在"替 Agent 做决策"。等模型足够强能自己判断，这些手工认知策略会像 SIFT 遇见 AlexNet 一样一夜淘汰。

真正有壁垒的是：
- **模型层**：算力与算法，但长期会被开源追平
- **Harness 层**：执行驾驭，仍在快速演化
- **数据库层**：物理世界持久化，完全不受 AI 冲击

## 代表项目列表

| 项目 | 类型 | 状态 |
|------|------|------|
| Mem0 | 数据库套壳 SDK | 活跃，融资中 |
| Letta（MemGPT） | Agent Runtime | 改名继续融资 |
| Zep | 数据库套壳 SDK | 活跃 |
| Cognee | 知识图谱构建器 | 活跃 |
| Hindsight | 时序图谱构建器 | 活跃 |
| MemoryScope | 数据库套壳 SDK | 开源 |
| Memobase | 数据库套壳 SDK | 活跃 |
| SuperMemory | 数据库套壳 SDK | 活跃 |
| Graphiti | 时序图谱构建器 | 开源 |
| LangMem | 数据库套壳 SDK | 活跃 |

## 记忆层的终局

作者认为"通用可迁移的记忆层"的终局是 **PostgreSQL**：

1. **事实已在发生**：Letta、Hindsight 等严肃项目都在向 PG+pgvector 靠拢
2. **Wire Protocol 是事实标准**：PG 协议如同 HTTP，模型天然会说 SQL
3. **扩展生态覆盖所有检索原语**：pgvector（向量）、tsvector（全文）、AGE（图）、TimescaleDB（时序）

"十年后再回望 2026 年这场 Memory 框架喧哗，会看到那些号称'给 Agent 设计认知'的框架，最后真正留下来的代码，是把数据老老实实塞进 PostgreSQL 的那几行 SQL。其他的，都会被端到端学习吃掉。"

## 参考来源

- 原文：[三分天下：为什么Agent Memory框架是死路](https://mp.weixin.qq.com/s/bWpka2OzrJFyrbmrecIQUw)
- 作者：老冯（数据库老司机）
