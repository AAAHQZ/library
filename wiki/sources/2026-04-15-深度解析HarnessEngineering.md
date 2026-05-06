---
title: "深度解析Harness Engineering"
slug: harness-engineering-deep-dive
summary: 当AI Agent从"能跑"走向"能治"，Harness Engineering作为一门新基础学科成形。核心是设计让模型作为Agent行动起来的外循环系统，包含计划分解、持久状态、工具编排、验证门控、反馈回路等构件。
description: 本文系统解析Harness Engineering的起源、定义、六大工程构件，以及从指令驱动到意图驱动的范式迁移，探讨AgentOS作为未来方向。
tags:
  - Harness Engineering
  - AI Agent
  - 系统架构
  - 脚手架工程
  - Agentic AI
  - Context Engineering
created: 2026-04
source: https://mp.weixin.qq.com/s/-mgf8K7XZrTKoD0pMOIn3w
---

# 深度解析Harness Engineering

## 核心观点

> **模型不是瓶颈，系统才是。**

当 AI Agent 从"能跑"走向"能治"，一门新的基础学科正在成形。

## AI工程四年演进

### 第一幕：生成（2022.11 — 2023）
- ChatGPT上线，LLM从API变成对话界面
- **核心矛盾**：模型能生成，但不能行动
- 工程产物：Prompt Engineering

### 第二幕：连接（2023 — 2024）
- GPT-4发布，ChatGPT Plugins让模型"长出手"
- Function Calling API标准化工具调用
- **教训一**：连接不等于编排，编排不等于治理

### 第三幕：推理（2024）
- OpenAI o1系列、Claude 3.5 Sonnet、DeepSeek-R1
- Anthropic发布MCP协议和Building Effective Agents指南
- **教训二**：单步质量不能解决长任务可靠性

### 第四幕：行动（2025）
- Claude Code、GitHub Copilot Agent Mode、Cursor自主编码
- MCP生态爆发（数千个MCP服务器）
- **教训三**：Agent能力到了"可自主工作数小时"，但工程基础设施还停留在"单次对话"时代

### 第五幕：治理（2026 — 现在）
- "Harness Engineering"术语正式进入主流讨论
- 核心转变：从"怎么让agent更能干"转向"怎么让agent不翻车"

## Harness到底是什么？

### Agent的五个根本性挑战

1. **状态持久性**：Agent需要跨session记住做过什么
2. **目标一致性**：长任务中agent容易漂移、自嗨
3. **行动可验证性**：需要区分"做了"和"做对了"
4. **熵增抑制**：持续产出累积冗余和漂移
5. **人机边界**：何时自主、何时交还人类（参见 [[UX-设计]] 中的 Agent 边界体验设计）

### 精确释义

> **Harness = 让模型能够作为Agent行动起来的外循环系统**

包含：计划分解、持久状态、工具编排、验证门控、反馈回路、回退机制、人机交接点、审计日志。

**关键洞察**：评估一个Agent时，评估的是 **model + harness** 的组合，而不是模型单独的能力。

### 三层工程抽象

```
Prompt Engineering → Context Engineering → Harness Engineering
（单次调用）      →（每步上下文）      →（整条流水线运转）
```

## Harness六大工程构件

### 1. Durable State Surfaces
**问题**：长时Agent像换班工程师完全失忆

**解法**：把状态外化成可续航工件
- init.sh启动脚本
- claude-progress.txt进度日志
- feature list：把高层需求展开成200+条具体feature，标记pass/fail状态

**设计原理**：状态 ≠ 保存聊天记录。真正的durable state是agent可以在冷启动后没有任何上下文历史的情况下读取、理解、续航的结构化工件。

### 2. Decomposition & Plans
**问题**：Agent试图一把梭，上下文爆或中途宣布完成

**演进**：
- 2025.11：initializer + coding 二角色结构
- 2026.3：升级为 planner/generator/evaluator 三角色系统

**设计原理**：计划必须被提升为一等工件，写入文件系统、被版本管理、被后续agent可读取。

### 3. Feedback Loops：Guides 与 Sensors

| 类型 | 作用时机 | 特点 |
|------|----------|------|
| Guides | 行动之前约束 | 提高"一次做对"概率 |
| Sensors | 行动之后给信号 | 支持自纠错 |

- **Computational控制**：便宜、快、确定性
- **Inferential控制**：贵、慢、非确定性，但处理主观判断

**关键洞察**：
- 只有Guides没有Sensors → agent编码了规则但永远不知道是否生效
- 只有Sensors没有Guides → agent不断重复同样错误

### 4. Legibility：为Agent建造感知面

**核心判断**：凡是不在agent运行时可见范围内的知识，就等于不存在。

**具体做法**：
- 给每个git worktree启动独立浏览器实例，通过CDP让agent"看到"UI
- 把logs、metrics、traces全部暴露给agent查询
- Repository knowledge作为system of record

### 5. Tool Mediation：工具越多越需要Harness

**关键思路**：不要让模型直接调用工具，让模型写代码来调用工具。

- **直接工具调用**：所有工具定义加载进上下文 → 消耗大
- **代码执行模式**：模型写代码 → 代码在沙箱里按需调用MCP工具 → 只回传最终结果

### 6. Entropy Control：Agent的持续垃圾回收

**问题**：Agent代码库会不断复制既有模式（即使不均匀、次优甚至糟糕）

**解法**：
- Documentation consistency agents：定期验证文档与代码一致
- Refactor agents：按计划清理技术债
- Architectural enforcement：通过CI机械化维护模块边界

## 范式迁移：指令驱动 → 意图驱动

### 人机交互四次断裂

1. **CLI**：人必须精确掌握机器的语言
2. **GUI**：机器通过视觉隐喻降低门槛
3. **App**：逻辑被凝固成固定界面
4. **Agent**：人只表达目标，系统自主规划执行路径

| ### AgentOS演进三阶段

|| Level | 阶段 | 核心工程抽象 |
||-------|------|-------------|
|| 1 | Chatbot（2022-2023） | Prompt Engineering |
|| 2 | AI Browser/Agent IDE（2024-2025） | Context Engineering + 轻量Harness |
|| 3 | AgentOS（2026+，萌芽期） | Harness Engineering → 系统层 |

使用 [[Wardley-Map]] 可以更好地分析各组件在技术战略中的演进位置。

**Harness是AgentOS的用户态层**，AgentOS是内核——管调度、管隔离、管资源。

## 五个典型症状

1. **框架丛林**：各框架解决一小块，缺乏完整生命周期
2. **Chatbot皮+Agent芯**：缺乏真正的状态管理、任务分解、验证门
3. **工具注册≠工具治理**：能连不等于会用
4. **一次性规则vs可演进约束**：巨大AGENTS.md必然失败
5. **缺乏on-the-loop思维**：停留在in the loop修复错误，而不是系统性改进控制回路

## 相关主题

- [[AI-Agent框架选型与工程实践|sources/2026-04-22-AI-Agent框架选型与工程实践]] - AI-Agent工程实践
- [[为什么你的AI优先战略可能大错特错|sources/2026-04-15-为什么你的AI优先战略可能大错特错]] - AI First与软件工程基础
- [[MCP没死|sources/2026-04-23-MCP没死]] - MCP协议深度解析
- [[从第一性原理思考AgenticEngineering|sources/2026-05-05-从第一性原理思考AgenticEngineering]] - Agentic Engineering原理
