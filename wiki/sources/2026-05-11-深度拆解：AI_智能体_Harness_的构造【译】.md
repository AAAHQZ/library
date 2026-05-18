---
title: "深度拆解：AI 智能体 Harness 的构造【译】"
type: source
tags:
  - AI Agent
  - Harness Engineering
  - Context Engineering
  - Agent架构
  - Anthropic
  - OpenAI
  - LangChain
  - Claude Code
source: "[[raw/wechat/2026-05-11-深度拆解：AI_智能体_Harness_的构造【译】.md]]"
date: 2026-05-11
confidence: EXTRACTED
---

# 深度拆解：AI 智能体 Harness 的构造【译】

> 原文链接：https://mp.weixin.qq.com/s/yOX1QZTHs54J-CBg-rKUig  
> 原文：https://x.com/akshay_pachaar/status/2041146899319971922

## 基本信息

- **来源**：微信公众号翻译文章
- **字数**：5933 字
- **主题**：AI 智能体 Harness 的系统性拆解——12 个核心组件
- **标签**：Harness Engineering、Context Engineering、Agent架构、框架对比

## 核心观点

### 1. 什么是 AI Agent Harness？

Harness 是包裹在大语言模型之外的完整软件架构，包括：编排循环、工具、记忆、上下文管理、状态持久化、错误处理和护栏（Guardrails）。

**关键区分**：
- "AI 智能体"（Agent）是用户感知到的行为体现——有目标、会用工具、能自我纠错的实体
- "Harness"是产生这种行为的底层机器

**定义公式**（Vivek Trivedy，LangChain）：> "如果你不是模型本身，那你就是 Harness。"

**类比**（Beren Millidge）：
- 原生 LLM = 没有内存、没有硬盘、也没有输入输出设备的 CPU
- 上下文窗口 → 内存（快但容量有限）
- 外部数据库 → 硬盘（大但速度慢）
- 工具集成 → 设备驱动程序
- **Harness = 操作系统**

### 2. 工程化的三个层次

围绕模型，工程化分为三个同心圆层次：

| 层次 | 描述 |
|------|------|
| 提示词工程 | 精心设计模型接收到的指令 |
| 上下文工程 | 管理模型在什么时间点能看到什么内容 |
| Harness 工程 | 涵盖上述两者 + 整个应用架构（工具编排、状态持久化、错误恢复、验证循环、安全执行、生命周期管理） |

### 3. 生产级 Harness 的 12 个核心组件

| # | 组件 | 关键描述 |
|---|------|----------|
| 1 | **编排循环**（The Orchestration Loop） | TAO（Thought-Action-Observation）循环，即 ReAct 循环。Anthropic 称之为"笨循环"，智慧全在模型里 |
| 2 | **工具**（Tools） | Claude Code 六大类：文件操作、搜索、执行、网页访问、代码分析、子智能体创建。OpenAI 支持 MCP 服务器工具 |
| 3 | **记忆**（Memory） | Claude Code 三层：轻量级索引（150字符始终加载）、按需主题文件、仅搜索访问的原始对话 |
| 4 | **上下文管理**（Context Management） | 核心问题：上下文腐烂。策略：压缩、观察掩码、即时检索、子智能体委托 |
| 5 | **提示词构建**（Prompt Construction） | 层级化：系统提示词 → 工具定义 → 记忆文件 → 对话历史 → 用户消息 |
| 6 | **输出解析**（Output Parsing） | 原生工具调用（tool_calls 对象），通过 Pydantic 模型进行模式约束 |
| 7 | **状态管理**（State Management） | LangGraph：状态图 + Checkpointing；OpenAI：四种策略；Claude Code：Git 提交作为存档点 |
| 8 | **错误处理**（Error Handling） | LangGraph 四类：临时性（延迟重试）、模型可恢复、用户可修复、意外错误 |
| 9 | **护栏与安全**（Guardrails and Safety） | OpenAI 三层级：输入护栏 → 输出护栏 → 工具护栏。"绊网"机制立即停止 |
| 10 | **验证循环**（Verification Loops） | 三种方法：基于规则的反馈（测试/代码检查）、视觉反馈（Playwright 截图）、LLM-as-judge |
| 11 | **子智能体编排**（Subagent Orchestration） | Claude Code 三模式：克隆(Fork)、队友(Teammate)、工作树(Worktree) |
| 12 | **（未明确编号）** | Harness 的厚度：多少逻辑写死在系统里，多少留给模型发挥 |

### 4. 循环运作步骤（7步）

1. 提示词组装 → 2. 模型推理 → 3. 输出分类（有工具调用？）→ 4. 工具执行 → 5. 结果打包 → 6. 上下文更新 → 7. 循环

### 5. 框架实现对比

| 框架 | 实现思路 |
|------|----------|
| **Anthropic** (Claude Agent SDK) | query() 函数暴露 Harness，"笨循环"运行 |
| **OpenAI** (Agents SDK) | "代码优先"，工作流逻辑直接用 Python 表达 |
| **LangGraph** | Harness 建模为显式状态图，精细控制流程 |
| **CrewAI** | 基于角色的多智能体协作，"流程层"管理确定性骨干逻辑 |
| **AutoGen**（微软） | 多种编排模式：顺序执行、群聊、移交、动态任务管理 |

### 6. 脚手架比喻与协同进化原则

> 房子盖好后，脚手架是要拆除的。

随着模型能力提升，Harness 的复杂程度应逐渐降低。**协同进化原则**：如果 Harness 设计得好，当模型升级时，不需要增加复杂度，性能就会自动提升。

### 7. 定义 Harness 的 7 个关键决策

1. 单智能体 vs. 多智能体
2. ReAct vs. 先规划后执行
3. 上下文管理策略（总结 vs. 动态加载）
4. 验证循环设计（代码测试 vs. LLM 打分）
5. 权限与安全架构（速度优先 vs. 安全步步确认）
6. 工具范围管理（最小工具集原则）
7. Harness 的厚度（写死 vs. 留给模型）

### 8. 关键洞察：Harness 即产品

- TerminalBench 证据：改变 Harness，排名变动 20 多位（LangChain 从 30 名外升到第 5）
- 另一研究：让 LLM 自己优化架构，达到 76.4% 通过率，超过人类精心设计的系统
- **Harness 是硬核工程能力的体现**：上下文稀缺资源管理、验证循环防止错误累积、不产生幻觉的记忆系统

## 关键概念关联

- [[Harness]] — 核心概念，本文系统性拆解
- [[Harness-Engineering]] — Harness 的设计与工程实践
- [[Context-Engineering]] — 12 个组件中的第 4 项
- [[Claude-Code]] — Anthropic 的 Harness 实现代表
- [[ReAct]] — 编排循环的核心模式（Thought-Action-Observation）
- [[Memory-Store]] / [[Agent记忆分层]] — Harness 的第 3 个组件
- [[MCP]] — 开放工具接入标准，OpenAI Agents SDK 支持

## 原文精彩摘录

> 问题其实并不在模型本身，而在模型外围的基础设施。
>
> LangChain 证明了这一点：他们仅仅通过改变包裹大语言模型的底层架构——模型没变，参数没变——就让系统在 TerminalBench 2.0 上的排名从 30 名开外飙升到了第 5 名。
>
> 正如 Millidge 所写："我们重新发明了冯·诺依曼架构（Von Neumann architecture）"，因为这是任何计算系统最自然的抽象方式。
>
> 随着模型越来越强，Harness 会变薄，但它永远不会消失。
>
> 下次当你的智能体表现不佳时，别光顾着抱怨模型，去检查一下你的 Harness 吧。

## 相关页面

- [[Harness]] — Harness 概念页
- [[Harness-Engineering]] — Harness 工程实践主题页
- [[Claude-Code]] — Anthropic 的 Agent Harness 产品
- [[Context-Engineering]] — 上下文管理工程
