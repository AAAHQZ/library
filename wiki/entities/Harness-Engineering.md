---
title: "Harness Engineering"
date: 2026-05-06
tags:
  - AI Agent
  - 工程实践
  - 架构
type: entity
confidence: EXTRACTED
source: 深度解析HarnessEngineering
---

# Harness Engineering

> 让模型能够作为 Agent 行动起来的外循环系统工程实践——包含计划分解、持久状态、工具编排、验证门控、反馈回路、回退机制、人机交接点和审计日志。

## 概念定位

### 与 [[Harness]] 的关系

- **[[Harness]]**（狭义）：CMA 架构中的调度内核，负责调用模型推理并将工具调用意图路由到沙盒执行。是一个具体的**组件名词**。
- **Harness Engineering**（广义）：围绕 Agent 构建完整外循环系统工程的一门**工程实践学科**，包含状态管理、任务分解、验证、熵控等全部构件。

> 评估一个 Agent 的效果时，评估的不是模型本身，而是 **model + harness** 的组合。

### 三层抽象递进

```
Prompt Engineering  →  Context Engineering  →  Harness Engineering
单次调用如何问好      每一步上下文如何喂        整条流水线如何运转
```

- **Context Engineering** 是"每步喂什么"，**Harness Engineering** 是"整条流水线怎么运转"。前者是后者的子集。

### 与 Evaluation Harness 的区别

| 概念 | 职责 |
|------|------|
| **Agent Harness / Scaffold** | 让 Agent 运行起来——处理输入、编排工具、管理状态 |
| **Evaluation Harness** | 批量运行任务、记录轨迹、执行 grader、汇总评分 |

## 五大根本挑战（ Harness 要回答的问题）

1. **状态持久性**：Agent 跨 session 记住做过什么——Context window 有上限，模型无状态
2. **目标一致性**：长任务中不漂移、不自嗨、不提前宣布完成
3. **行动可验证性**：区分"做了"和"做对了"——模型自我评价存在自我表扬倾向
4. **熵增抑制**：持续产出累积冗余、漂移和不一致
5. **人机边界**：何时自主、何时交还人类，需要工程化定义

## 六大自然演化阶段

| 阶段 | 时间 | 核心问题 | 工程产物 |
|------|------|----------|----------|
| 第一幕：生成 | 2022.11—2023 | 能生成不能行动 | Prompt Engineering |
| 第二幕：连接 | 2023—2024 | 能说不能做 | Function Calling / Plugins / LangChain |
| 第三幕：推理 | 2024 | 单步质量 | 推理模型（o1/o3）+ [[MCP]] + Context Engineering |
| 第四幕：行动 | 2025 | 能跑但易崩 | Agent 循环（Claude Code / Codex） |
| 第五幕：治理 | 2026— | 能跑不等于能治 | **Harness Engineering** |

关键转折：2026 年 2 月 Mitchell Hashimoto 明确写出"Engineer the Harness"，同年 2 月 OpenAI 发布《Harness Engineering: Leveraging Codex in an Agent-First World》，4 月 Thoughtworks/Fowler 体系将其系统化为 guides+sensors 的 2×2 控制矩阵。

## 六大工程构件

### 1. Durable State Surfaces（持久状态面）

**问题**：Context window 有限，复杂项目无法在单窗口内完成，换 session 后完全失忆。

**解法**（Anthropic）：
- 用外化结构化工件代替"无限长上下文"
- `init.sh`（启动脚本）+ `claude-progress.txt`（进度日志）+ `feature list`（200+ 条 feature，标记 pass/fail）
- Agent 冷启动后 30 秒内通过读取这些文件续航

**设计原理**：状态 ≠ 保存聊天记录。真正的 durable state 是 Agent 可以在无上下文历史的情况下读取、理解并续航的结构工件。

### 2. Decomposition & Plans（分解与计划）

**问题**：Agent 试图一把梭，长任务中途上下文爆或宣布"完成"。

**演进**：
- 2025.11：initializer + coding 二角色
- 2026.03：升级为 **planner / generator / evaluator** 三角色系统
  - Planner：把高层描述扩展成完整 product spec 和分步 feature list
  - Generator：逐 feature 落地，每完成一个 commit
  - Evaluator：独立评估产出，标记 pass/fail，给出改进建议

**设计原理**：计划必须被提升为一等工件——写入文件系统、被版本管理、被后续 Agent 可读取、被验证门引用。

### 3. Feedback Loops：Guides 与 Sensors

**问题**：Agent 倾向于热情地自我表扬，需要不依赖自我评价的反馈系统。

| 维度 | Guides（前馈控制） | Sensors（反馈控制） |
|------|-------------------|-------------------|
| 时机 | 行动之前约束 | 行动之后给信号 |
| Computational | 规则检查、linter、类型检查 | 测试套件执行 |
| Inferential | 架构约束提示 | 主观判断（UI 美观度等）|

**关键洞察**：只有 guides 没有 sensors → Agent 编码了规则但永不知道是否生效；只有 sensors 没有 guides → Agent 不断重复同样的错误。

### 4. Legibility（可感知性）

**问题**：Agent 能写代码，但能否"看到"代码跑起来什么样？能否读懂错误日志？

**核心原则**：凡不在 Agent 运行时可见范围内的知识，等于不存在。

**具体做法**：
- 每个 git worktree 启动独立浏览器实例，通过 CDP 让 Agent "看到" UI
- logs / metrics / traces 全部暴露给 Agent 查询
- 架构决策记录（ADR）、设计原则、技术债全部进入 repo 并用 lint/CI 维持一致性
- AGENTS.md 作为目录索引而非知识容器（避免上下文腐烂）

### 5. Tool Mediation（工具中介）

**问题**：连接数十个 MCP 服务器、上百个工具后，直接暴露给 Agent 会导致 token 暴增和决策迷失。

**核心思路**：不要让模型直接调用工具，让模型写代码来调用工具。

| 模式 | 描述 |
|------|------|
| 直接工具调用 | 所有工具定义进上下文 → 模型选工具 → 调用 → 结果回传 |
| **代码执行模式** | 模型写代码 → 代码在沙箱按需调用 MCP → 只回传最终结果 |

本质：把工具使用从模型内循环挪到外部执行回路。这是系统级设计——决定工具如何被发现、何时暴露、以什么粒度暴露、结果是否进上下文。

### 6. Entropy Control（熵增控制）

**问题**：全自动 Agent 代码库会不断复制既有模式——即使这些模式不均匀、次优甚至糟糕。

**解法**（OpenAI）：
- Documentation consistency agents：定期验证文档与代码是否一致
- Refactor agents：按计划清理技术债
- Architectural enforcement：通过 CI 机械化维护模块边界

最初靠人每周 20% 时间清理 AI slop，后来把清理逻辑系统化。

## 五个典型症状

1. **框架丛林**：LangChain / CrewAI / AutoGen / Agno 各解决一小块，缺乏完整生命周期
2. **Chatbot 皮 + Agent 芯**：缺状态管理、任务分解、验证门，demo 惊艳但生产翻车
3. **工具注册 ≠ 工具治理**：MCP 让连接变容易，但"能连"不等于"会用"
4. **一次性规则 vs. 可演进约束**：巨大 AGENTS.md 必然失败——当一切都重要，什么都不重要
5. **缺乏 on-the-loop 思维**：停留在 in the loop（手改产物），而不是 on the loop（改 harness 让系统下次自动更好）

## 被忽视的关键问题

| 问题 | 说明 |
|------|------|
| Harness 本身的可测试性 | 用 LLM 做 evaluator 可能建造"用不可靠系统验证不可靠系统"的循环 |
| 多 Agent 涌现行为 | 10 个 Agent 并行时系统行为涌现出无法预测的模式，类似分布式并发 bug |
| 成本与延迟的权衡 | 每层 harness 都消耗 token 和延迟，存在过度工程的边界问题 |
| 安全新维度 | 攻击目标从数据变成 agency（Tool Poisoning Attacks 等） |

## 意图驱动范式迁移

人机交互四次断裂：**CLI（指令）→ GUI（隐喻）→ App（预设路径）→ Agent（意图驱动）**

在 Agent 时代，应用正在被"CLI 化"——不是给用户返回终端，而是从 Agent 视角把一切变成可编程接口。MCP 是这一趋势的协议层实现。**Harness 成为新的"操作系统层"**：

- GUI 时代 OS 管理窗口、文件、进程
- Agent 时代 Harness 管理：Agent 生命周期、工具发现与授权、上下文调度与回收、多 Agent 协作与隔离、人类审批介入点

### AgentOS 定位

| 层级 | 职责 |
|------|------|
| AgentOS（内核） | 调度、隔离、资源管理 |
| Harness（用户态 shell/daemon） | 任务分解、状态续航、验证反馈、人机交接 |

## 判断与展望

1. **Harness Engineering 将成为 AI 工程时代的基础学科之一**——模型越强，harness 越重要而非越被替代
2. **护城河重心从模型质量上移到 Harness 与系统设计**——LangChain 仅通过修改 harness 将 Terminal Bench 2.0 得分从 52.8% 提升到 66.5%
3. **从 Chatbot 到 AgentOS 不会一步到位**——中间经历 2-3 年"AI Browser + 轻量 Harness"阶段
4. **工程师角色从代码生产者变为自治系统设计者**——从 in the loop 到 on the loop

## 实践者自检三问

- 冷启动后 Agent 能否在 **30 秒内续航**？还是每次都从头开始？
- "完成"的定义是 Agent **自我感觉**还是**外部结构化验证面**（feature list / 测试套件 / pass/fail 状态）？
- repo、工具、日志、指标、策略是否对 Agent **legible and enforceable**？

## 相关实体

- [[Harness]] — CMA 架构中的调度内核组件（狭义）
- [[MCP]] — 工具连接的开放标准协议
- [[Context-Engineering]] — 每步上下文的工程实践（Harness Engineering 子集）
- [[Agent-Native]] — Agent 原生化趋势
- [[Agentic-CI-CD]] — Agent 在 CI/CD 场景的治理实践

## 参考文献

- [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)（Anthropic, 2024.12）
- [Harness Engineering: Leveraging Codex in an Agent-First World](https://openai.com/index/harness-engineering)（OpenAI, 2026.02）
- [Harness Engineering - first thoughts](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering-memo.html)（Thoughtworks/Fowler, 2026.04）
- [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
