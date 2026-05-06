---
tags: [Anthropic, Claude-Code, Claude, Harness, System-Prompt, Thinking, Postmortem]
created: 2026-04-23
updated: 2026-05-05
sources: [../raw/Anthropic 工程博客自爆：一条 25 词的 system prompt 限制如何摧毁 Claude Code.md]
---

# Anthropic 工程博客自爆：一条 25 词的 system prompt 限制如何摧毁 Claude Code

> 2026年3-4月，Claude Code 用户报告模型"变笨了"。AMD 高级总监 Stella Laurenzo 对 6,852 个会话的量化分析显示 thinking 深度暴跌 67%，盲编辑率从 6.2% 飙升到 33.7%。Anthropic 最终追踪到三个独立的产品层变更：默认 effort 从 high 降到 medium、缓存优化 bug 导致 thinking 历史被持续清除、以及一条限制输出字数的 system prompt 指令。这证明了"模型没变，harness 变了"可以制造出与模型退化完全相同的用户体验。

## 事件时间线

| 时间 | 事件 | 影响 |
|------|------|------|
| 2月5日 | Opus 4.6 发布，默认 effort 为 high | Claude Code 全量用户 |
| 3月4日 | 默认 effort 从 high 改为 medium | Sonnet 4.6 + Opus 4.6 用户 |
| 3月26日 | 上线缓存优化，意图清除空闲 >1h 的会话 thinking | 空闲过的会话 |
| 4月16日 | 上线 Opus 4.7 + 新 system prompt 限制输出字数 | Sonnet 4.6 + Opus 4.6 + Opus 4.7 |
| 4月20日 | 回滚 system prompt 变更（v2.1.116） | 修复完成 |

## 三个问题的根因分析

### 问题一：默认推理努力从 high 降到 medium

**表面原因**：部分用户在 high effort 下遇到极端长尾延迟，团队看到 eval 显示 medium 只损失略微智力但显著降低延迟。

**根本问题**：
- test-time compute 曲线是非线性的，medium 到 high 区间有陡峭上升段
- Claude Code 核心用例是复杂任务，5% 智力损失比 50% 延迟增加更不可接受
- 90%+ 用户不会主动切换默认值

**教训**：对于以智力为核心卖点的产品，默认值选择等同于产品定位选择。

### 问题二：缓存优化 bug 导致 thinking 历史被持续清除

**设计意图**：会话空闲超过 1 小时后缓存已驱逐，清除旧 thinking 可减少 uncached token。

**bug 本质**：状态标志没有被正确重置。flag 一旦设置就在整个会话剩余生命周期中持续生效，每个后续 turn 都只保留最近一个 thinking block。

**级联效应**：
1. 模型的"记忆"被逐 turn 擦除
2. 每次请求前缀变化导致连续 cache miss，反而让 token 消耗更快

**为什么两周才发现**：
- 触发条件需要会话空闲超过 1 小时
- 内部消息队列实验和 thinking 显示方式变更干扰了复现
- Opus 4.7 能找到这个 bug，Opus 4.6 找不到

**教训**：当你对 LLM 的 thinking 做优化时，你实际上是在修改模型的"工作记忆"。任何对 thinking 的自动化修剪策略都应被视为与"修改模型参数"同等级别的变更来审查。

### 问题三：System prompt 限制输出字数

**问题指令**：
> "Length limits: keep text between tool calls to ≤25 words. Keep final responses to ≤100 words unless the task requires more detail."

**为什么影响编码质量**：
- 输出长度和推理深度不是独立变量
- 25 词限制本质上是要求模型跳过中间推理步骤
- 工具调用之间的 visible reasoning 是维持执行计划一致性的关键机制
- 这与 Chain-of-Thought 的原理直接矛盾

**为什么内部测试没有捕获**：eval 集偏重短任务和标准化任务，字数限制对这类任务影响微小。

**教训**：对 LLM 来说，output token 不仅是"输出"，也是"推理的一部分"。"让模型少说话"和"让模型少思考"在技术上是同一件事。

## 三个 Bug 的共性模式

| 优化目标 | 优化手段 | 被削减的推理维度 |
|----------|----------|------------------|
| Effort 降档 | 减少延迟、节省用量限额 | test-time compute / thinking 深度 |
| Caching bug | 减少 uncached input token | thinking 记忆（跨 turn 推理连续性） |
| 字数限制 | 减少 output token | 可见推理链条（inter-tool CoT） |

**三者攻击了推理能力的三个不同切面：深度、记忆、链条。**

## 核心启示

### 对 AI 编码工具行业

用户感知到的"模型智力"不等于模型本身的能力。它是模型能力经过 effort 参数、thinking 管理策略、system prompt、context window 管理等多层 harness 过滤后的最终产出。

**你不能只 eval 模型，你必须 eval 整个 harness + 模型的组合。**

System prompt 的每一行、thinking 管理的每一个策略、默认参数的每一次调整，都需要被视为"可能改变模型有效智力"的变更来审查和测试。

### 关键量化指标

- thinking 深度下降 67%
- 盲编辑率从 6.2% 升到 33.7%
- 文件读取/编辑比从 6.6 降到 2.0

## 相关页面

- [[深度解析HarnessEngineering|sources/2026-04-15-深度解析HarnessEngineering]]
- [[AI-Agent框架选型与工程实践|sources/2026-04-22-AI-Agent框架选型与工程实践]]
- [[MCP没死|sources/2026-04-23-MCP没死]]
