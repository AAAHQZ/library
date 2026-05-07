---
source: article
title: 深度拆解HermesAgent的记忆系统它如何修正OpenClaw的误区
date: 2026-05-05
author: 
published: 
references: []
summary: "原文链接：https://mp.weixin.qq.com/s/Oq33YStOaYOdzQ7wXHii9w"
---

# 深度拆解HermesAgent的记忆系统它如何修正OpenClaw的误区

> 原文链接：https://mp.weixin.qq.com/s/Oq33YStOaYOdzQ7wXHii9w

> 公众号：宝玉AI

# 深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区

作者：Manthan Gupta
原文：I Read Hermes Agent's Memory System, and It Fixes What OpenClaw Got Wrong[1]

如果你读过我之前关于 ChatGPT、Claude 以及 Clawdbot 记忆系统的文章，你就会知道我一直在钻研同一个问题：这些 AI 智能体（AI Agent）到底是怎么记事的？

Hermes Agent 对我来说格外有趣，因为这次我不需要只靠观察它的行为来搞“逆向工程”。Hermes 是开源的，它的代码库和文档都是公开的。所以，我没有通过提示词（Prompt）去盲测这个黑盒，而是直接翻看了它的代码路径——从它如何构建提示词状态、持久化会话，到如何清理记忆和查询历史对话。

**简而言之：Hermes 拥有的不是一套记忆系统，而是四套。**

- 1. 存储在&nbsp;`MEMORY.md`&nbsp;和&nbsp;`USER.md`&nbsp;中、经过高度浓缩的**提示词记忆**。
- 2. 通过&nbsp;`session_search`&nbsp;调用的&nbsp;**SQLite 历史会话存档**（可搜索）。
- 3. 像**程序记忆**（Procedural Memory）一样运作的**智能体技能管理**。
- 4. 可选的&nbsp;**Honcho**&nbsp;层，用于更深层的**用户建模**（User Modeling）。

把这些设计联系在一起的核心逻辑非常简单：**保持提示词稳定以便利用缓存（Caching），其他一切繁杂信息都交给工具。**
![深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_001.jpg](深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_001.jpg)
让我们深入聊聊。
![深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_002.jpg](深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_002.jpg)# Hermes 的上下文结构

在理解记忆之前，我们先看看 Hermes 到底给模型发送了什么。

系统提示词（System Prompt）大致是按以下顺序组装的：
```
[0] 默认智能体身份
[1] 工具使用行为指南
[2] Honcho 集成模块（可选）
[3] 可选系统消息
[4] 固化的 MEMORY.md 快照
[5] 固化的 USER.md 快照
[6] 技能索引
[7] 上下文文件（AGENTS.md, SOUL.md 等规则文件）
[8] 日期/时间 + 平台信息
[9] 对话历史
[10] 当前用户消息
```
这非常关键，因为 Hermes 正在针对大模型供应商的**提示词缓存（Prompt Caching）**机制进行优化。代码显示，提示词构建器的目标非常明确：**让稳定的前缀部分尽可能长时间地保持不变。**

这一个决定就解释了 Hermes 大部分的记忆架构。

如果某条信息每一轮对话都要用到，Hermes 会尽量把它缩得很小并注入进去；如果信息量很大、属于历史旧账或者偶尔才有用，Hermes 就会把它踢出提示词，改用“按需检索”的方式。
![深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_003.jpg](深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_003.jpg)## 第一层：固化的提示词记忆

其内置的记忆系统小得令人惊讶。

Hermes 将持久记忆存储在&nbsp;`~/.hermes/memories/`&nbsp;下的两个文件中：
文件用途限制`MEMORY.md`智能体笔记：环境、规范、工具怪癖、教训2,200 字符`USER.md`用户画像：偏好、沟通风格、身份信息1,375 字符
这容量真不大。加起来大约只有 1,300 个&nbsp;**Token（模型理解文本的最小单位）**。

**而这正是刻意为之。**

在会话开始时，Hermes 加载这两个文件，把它们渲染进提示词区块，然后**在整个会话期间固化这个快照**。会话中途写入的记忆会立即存入硬盘，但不会改变已经生成的系统提示词。这些改动只有在开启新会话，或者触发了“压缩（Compression）”导致的提示词重建时才会生效。

渲染后的格式如下：
```
══════════════════════════════════════════════
MEMORY (你的个人笔记) [67% — 1,474/2,200 字符]
══════════════════════════════════════════════
用户的项目是一个位于 ~/code/myapi 的 Rust Web 服务，使用 Axum + SQLx
§
这台机器运行 Ubuntu 22.04，安装了 Docker 和 Podman
§
用户喜欢简洁的回复，讨厌冗长的解释
```
这里有几个我非常欣赏的细节设计：

- 1.&nbsp;**使用字符限制而非 Token 限制**：这让记忆逻辑与模型无关。Hermes 不需要调用特定模型的计算工具就能判断记忆是否存满。
- 2.&nbsp;**简单的分隔符文件格式**：条目之间用&nbsp;`§`&nbsp;分隔。没有复杂的向量数据库（Vector DB），没有自定义二进制存储，就是纯文本。
- 3.&nbsp;**刻意保持极小的系统提示词空间**：这是整个设计的重中之重。Hermes 不想把所有历史都塞进提示词，它只想要最有价值的事实。
- 4.&nbsp;**记忆是“精选状态”，而不是“日记”**：这是 Hermes 与 OpenClaw 最大的区别。

OpenClaw 的日志更像是“流水账”。而 Hermes 则反其道而行。它的工具架构和测试逻辑强调：

- • 保存用户偏好。
- • 保存环境事实。
- • 保存反复出现的错误修正。
- • 保存稳定的规范。
- •&nbsp;**不保存**任务进度。
- •&nbsp;**不保存**会话结果。
- •&nbsp;**不保存**临时的待办事项（TODO）。

**真相是：Hermes 希望&nbsp;`MEMORY.md`&nbsp;和&nbsp;`USER.md`&nbsp;保持精简、高频且对缓存友好。**
![深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_004.jpg](深度拆解 Hermes Agent 的记忆系统：它如何修正 OpenClaw 的误区_image_004.jp
...
