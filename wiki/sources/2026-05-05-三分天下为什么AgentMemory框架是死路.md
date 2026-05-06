---
tags: [Agent Memory, PostgreSQL, AI Agent, 三分天下, 数据库, Harness]
created: 2026-05-05
updated: 2026-05-05
sources: [../raw/wechat/三分天下：为什么Agent Memory框架是死路.md]
---

# 三分天下：为什么Agent Memory框架是死路

> Agent 需要记忆，但不需要今天这种被称作"Memory 框架"的东西。这两句话差两个字，差一条赛道的生死。

## 核心论点

**Agent 记忆的终局是"三分天下"**：
- **模型层**：由模型厂商（Anthropic、OpenAI 等）提供智力
- **Harness 层**：由 Claude Code、Cursor、Devin 等产品承担执行驾驭
- **数据库层**：由 PostgreSQL 等数据库厂商提供记忆持久化

关于 [[MemOS]] 在 Agent 记忆架构中的定位，请参阅实体页面。

## Memory 框架的四类分类

| 类型 | 代表 | 命运 |
|------|------|------|
| 数据库套壳 SDK | Mem0、LangMem、MemoryScope | 被模型自己写 SQL 替代 |
| 知识图谱/时序图谱构建器 | Graphiti、Cognee、Hindsight | 策略层被模型吸收，存储层归回数据库 |
| Agent Runtime / Agent OS | Letta/MemGPT | 应归入 Harness 赛道 |
| 端侧轻量方案 | SQLite + 向量扩展 | 端侧场景继续存在 |

## 为什么没有壁垒

1. **数据库套壳 SDK 本质是"建表和 SQL"**：任何懂 PG 的工程师周末就能写出基础版
2. **教会 Agent 用这套东西只需一个 Skill**：一张 markdown 描述清楚"记忆怎么存、怎么查"
3. **Claude Skills 机制已走通这条路**：用户写 memory-skill.md，Anthropic 可能发官方最佳实践

> **你以为的护城河，其实是一张写着几百字的 markdown。**

## The Bitter Lesson 视角

Memory 框架硬编码的"领域理解"——什么信息值得记、记在哪一层、什么时候触发反思——都是"替 Agent 做认知决策"的意见。

等模型强到能自己判断，这些手工认知策略会像 SIFT 遇见 AlexNet 那样一夜之间变成废铁。

## 真正的壁垒在数据库

**数据库不在 AI 冲击的范围内**：
- 数据库干的事是**物理世界的可靠性保证**（磁盘操作、断电保护、两阶段提交）
- LLM 再聪明变不出一块磁盘，保证不了 fsync 的语义
- Agent 越强大，越需要可靠的物理世界锚点

## 为什么是 PostgreSQL

1. **事实已在发生**：Letta、Hindsight、Tiger Data 等项目都在向 PG + pgvector 收敛
2. **线缆协议是事实标准**：PG 协议够老、够稳、够通用，没有哪家厂商能拥有或替换它
3. **扩展生态完整**：pgvector（向量）、tsvector（全文）、AGE（图）、TimescaleDB（时序）、PostGIS（地理）、Citus（水平扩展）

## 终局判断

> 那些号称"给 Agent 设计认知"的框架，最后真正留下来的代码，是它们最朴素的那一部分：**把数据老老实实塞进 PostgreSQL 的那几行 SQL**。其他的，都会被端到端学习吃掉。

## 相关链接

- [把 Agent 的状态放进数据库](https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491654&idx=1&sn=410bf56b4013e8ee6cdad065e6e8874b)
- [AI 时代，PostgreSQL 凭什么赢了？](https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491866&idx=1&sn=65c7ff42c4a295dcb835ac770aa1f089)
- [别争了，AI时代数据库已经尘埃落定](https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247489865&idx=1&sn=e09b423e8b64094df85b6ea6a0acede2)
