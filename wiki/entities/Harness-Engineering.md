---
title: "Harness Engineering"
date: 2026-05-06
tags:
  - AI Agent
  - 工程实践
  - 架构
type: entity
confidence: EXTRACTED
source: 深度解析HarnessEngineering, 告别氛围编程基于Harness治理和SDD的团队级AI研发范式演进与实践, 自然语言不适合编程Dijkstra半个世纪前的判断今天依然成立
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
| 第四幕：行动 | 2025 | 能跑但易崩 | Agent 循环（Claude Code / Cursor / Codex） |
| 第五幕：治理 | 2026— | 能跑不等于能治 | **Harness Engineering** |

关键转折：2026 年 2 月 Mitchell Hashimoto 明确写出"Engineer the Harness"，同年 2 月 OpenAI 发布《Harness Engineering: Leveraging Codex in an Agent-First World》，4 月 Thoughtworks/Fowler 体系将其系统化为 guides+sensors 的 2×2 控制矩阵。得物技术在 2026 年 5 月的全栈开发实践中进一步用 [[Cursor]] 的 Codebase Indexing 和多 Tab 并行机制，验证了 Harness 思维在真实项目中的提效——原本 2+4 人日的需求压缩至 3 人日，提效 50% 以上。

### Qoder 实践版：Harness Engineering 的四大支柱

高德大模型应用平台王树新在 [[Qoder]] 的落地实践中，将 Harness Engineering 系统化为四大支柱：

1. **上下文工程**：不再是简单的 RAG，而是结构化信息投喂。维护"单一事实来源"（single source of truth），让 Agent 知道项目的目录结构、当前执行计划以及哪些文档最新。

2. **架构约束**：通过物理手段强制 AI 遵守规则（如 UI 代码禁止直接访问数据库层），违反架构分层的代码无法通过语法检查，在提交前即被拦截。

3. **反馈回路与熵管理**：自动化的测试沙箱——Agent 写完代码 → 自动运行测试 → 失败 → 读取错误日志 → 自我修正并重试。将人类修复错误的经验固化为新规则，确保 AI 不犯同样的错误两次。

4. **人类监督**：人类从"写代码的人"变成"审核员"和"环境设计师"，职责是定义复杂的业务边界、处理 5% AI 无法判断的模糊逻辑、优化 Harness 本身的规则。

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

## Dijkstra 界面宽度理论根基

Harness Engineering 的理论根基可以追溯到 Dijkstra 在 EWD 667（1978）中提出的界面宽度理论：

- **自然语言是宽界面**：信息量大、歧义多，两侧（人和机器）都要付出额外代价
- **形式语言是窄界面**：信息量小、歧义少，协作成本低
- **工程实践中偏好窄接口**

在 AI 时代，消除歧义这件事已从「编译器与程序员之间」迁徙到「Prompt 与 Harness 之间」：

| 时代 | 消除歧义的主体 | 手段 |
|------|--------------|------|
| 传统编程 | 编译器 + 编程语言语法规则 | Pascal、C、Haskell 等 |
| LLM 时代 | Prompt + Harness | Structured Output、Tool Schema、Eval Pipeline、Spec 文档 |

**核心洞见**：所有认真做事的团队都在想方设法把自然语言「变回」形式化的东西。Harness 设计者每天在做的事，本质上与 Dijkstra 当年做的事是同一件——在自然语言的泥潭中，一点点、审慎地收窄那个界面。

## 判断与展望

1. **Harness Engineering 将成为 AI 工程时代的基础学科之一**——模型越强，harness 越重要而非越被替代
2. **护城河重心从模型质量上移到 Harness 与系统设计**——LangChain 仅通过修改 harness 将 Terminal Bench 2.0 得分从 52.8% 提升到 66.5%
3. **从 Chatbot 到 AgentOS 不会一步到位**——中间经历 2-3 年"AI Browser + 轻量 Harness"阶段
4. **工程师角色从代码生产者变为自治系统设计者**——从 in the loop 到 on the loop

## 实践者自检三问

- 冷启动后 Agent 能否在 **30 秒内续航**？还是每次都从头开始？
- "完成"的定义是 Agent **自我感觉**还是**外部结构化验证面**（feature list / 测试套件 / pass/fail 状态）？
- repo、工具、日志、指标、策略是否对 Agent **legible and enforceable**？

## 三次工程革命（进化脉络）

Harness Engineering 并非凭空出现，而是经历了从 Prompt Engineering → Context Engineering → Harness Engineering 的三次认知跃迁：

| 阶段 | 时间段 | 核心问题 | 认知框架 |
|------|--------|----------|----------|
| Prompt Engineering | 2022-2024 | 不会跟AI说话 | 模型够聪明，你不会问而已 |
| Context Engineering | 2025 | 不知道该给什么信息 | 在有限窗口塞进最强信号，挡住噪音 |
| Harness Engineering | 2026 | 信息给对了还是不行 | 从主动指导变为被动约束，从"你应该这样做"到"这条线不能碰" |

## Harness 五模块

一个完整的 Harness 由五个核心模块构成：
1. **Tools（工具）**：文件读写、Shell 执行、网络请求等，原子化、可组合、可被模型理解
2. **Knowledge（知识）**：产品文档、API 规范、架构设计等，按需加载而非一次性塞给模型
3. **Observation（观察）**：Git 变更、错误日志、浏览器状态等，可观测性是 Agent 自我修正的前提
4. **Action Interfaces（执行接口）**：CLI 命令、API 调用、UI 交互等，接口越标准集成成本越低
5. **Permissions（权限体系）**：沙箱隔离、危险操作拦截、人工审批，权限是让模型敢于放手的护栏

## 三层架构

从工程实现角度，Harness 分为三个层次：
1. **基础驾驭层**：极简循环（模型输出→执行→结果喂回→循环），所有 Agent 的心脏
2. **约束安全层**：子Agent 机制、技能库、上下文压缩
3. **生产质量层**：后台任务、多Agent 协作、工作树隔离

## 品味负债（运行期维护成本）

Harness Engineering 不仅要应对「建对系统」的挑战，还要警惕「品味负债」——在约束设计、评估标准、权限策略、知识治理上做的隐性妥协，正以复利方式啃噬工程效能（[[品味负债]]）。

**Gartner 2026 Q1**：67% 的企业 Harness 系统存在中度以上品味负债，平均修复成本是初期设计的 4.3 倍。

四大病灶直接关联 Harness Engineering 的四大支柱：
- 约束设计不当 → [[品味负债|约束僵化]]
- 评估体系失准 → [[品味负债|评估失真]]
- 权限管理粗糙 → [[品味负债|权限摩擦]]
- 知识治理缺位 → [[品味负债|知识腐败]]

清算策略包括：定期品味审计、动态约束调优、摩擦感知评估、人在回路的品味校准。

## 人人对齐→人机对齐（团队级实践方法论）

美团 Agent 评测团队的实践验证：管理 AI Coding 与评测 Agent 的底层逻辑相同——先通过规范拉齐团队的工程标准（人人对齐），再通过 AI Rule 和 Skill 约束大模型的生成结果（人机对齐）。顺序至关重要：团队共识是 AI 约束的前提（[[sources/2026-05-10-用Agent评测思路管理AI_Coding_——_31万行代码AI重构的实践]]）。

### Pre-PR 机制
AI 编码提速后 Code Review 成为新瓶颈。解决方案：
1. **Pre-PR 预审**：提交前先用 AI 自查规范/Bug/异常/性能问题，过滤后人工 CR 只聚焦业务语义
2. **模型对抗审查**：不同厂商模型互相审核——高阶模型审低阶模型，差异化模型能力互补
3. **AI Rule as Infrastructure**：将团队规范落地为 AI 编码时 always 加载的 Rule，前置到 Pre-PR 环节

## 相关实体

- [[Harness]] — CMA 架构中的调度内核组件（狭义）
- [[MCP]] — 工具连接的开放标准协议
- [[Context-Engineering]] — 每步上下文的工程实践（Harness Engineering 子集）
- [[Agent-Native]] — Agent 原生化趋势
- [[Agentic-CI-CD]] — Agent 在 CI/CD 场景的治理实践
- [[Cursor]] — AI 原生 IDE，Harness Engineering 的重要工具载体
- [[Qoder]] — 阿里巴巴 AI 编程平台，落地 Harness Engineering 四大支柱的实践载体
- [[Spec驱动开发]] — SDD 方法论
- [[Ashby-必要多样性定律]] —熵控制的控制论理论依据，"以少御多"注定失败

## 参考文献

- [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)（Anthropic, 2024.12）
- [Harness Engineering: Leveraging Codex in an Agent-First World](https://openai.com/index/harness-engineering)（OpenAI, 2026.02）
- [Harness Engineering - first thoughts](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering-memo.html)（Thoughtworks/Fowler, 2026.04）
- [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [基于 Harness + SDD + 多仓管理模式的 AI 全栈开发实践](https://mp.weixin.qq.com/s/ygQGSH5c7GHYDvkqWoQTXQ)（得物技术, 2026.05）
- [[多仓管理]]
- [十年老技术开发的 AI Agent 探索之路](https://mp.weixin.qq.com/s/r__2l_u6oXwzWHyD3wu_ZQ)（zhiyuanfu, 2026.05）— Harness Engineering 完整实践案例：SDD + 调度层 + 并发 + 失败切换 + 自举
- [告别"氛围编程"：基于 Harness 治理和 SDD 的团队级 AI 研发范式演进与实践](https://mp.weixin.qq.com/s/-_IBJFuXpvoqMJxL9oaEJQ)（高德大模型应用平台·王树新, 2026.05）— Qoder 实践版 Harness 四大支柱：上下文工程、架构约束、反馈回路、人类监督
- [[Spec驱动开发]] — SDD 方法论