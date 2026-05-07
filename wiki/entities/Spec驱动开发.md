---
title: "Spec 驱动开发 (SDD)"
date: 2026-05-06
tags:
  - 软件工程
  - AI编程
  - 方法论
type: entity
confidence: EXTRACTED
source: 你以为在写Spec其实在绕路写代码, 告别氛围编程基于Harness治理和SDD的团队级AI研发范式演进与实践
---

# Spec 驱动开发 (Spec Driven Development, SDD)

## 概述

Spec 驱动开发是一种将规格说明（Spec）作为核心前置产物的开发方法论，核心理念是：先把规格写够细，再交给 Coding Agent（如 [[Cursor]]、Claude Code、Codex）执行，认为这样能同时提升质量与效率。

## 核心论点

### 足够精确的 Spec，本质上就是代码

Gabriella Gonzalez 在《A sufficiently detailed spec is code》中指出，当规格精确到能让 Agent 稳定生成可运行实现时，其本质已接近代码——包含数据结构定义、类型约束、并发控制策略、重试公式与阈值。这种"伪代码化"的 Spec 实际上是用另一种语法编程。

引用 Dijkstra 的观点：复杂系统协作依赖窄接口，口语化、宽松化表达并不会减少劳动，只会把劳动转移成更多沟通成本和歧义。

### SDD 经常把团队带回瀑布流

STRRL 在《我对 Spec Driven Development 的看法》中指出，SpecKit 用久之后，开发流程会退化为：
- 提前设计 + 脑内推演 + 一次性交付大块产出
- 巨大 PR 堆着不敢合

软件不是一次性设计出来的，而是在持续变化中演进的。SDD 实践中容易将"变化中的探索"提前冻结成"文档中的确定性"，最终导致：
1. 花大量时间写 Spec
2. 一次性生成大量代码
3. 在后期集中爆雷
4. 人工返工收拾残局

这本质上是瀑布流的现代外观版。

### Spec 的边界，就是系统的盲区

SDD 的根本困境：
- 覆盖到的地方执行还行
- 覆盖不到的地方"一坨大便"

Gabriella 实测：按 Symphony 的 Spec 让 Claude Code 生成 Haskell 版本，结果并不可靠。即使是 YAML 这种规格已经非常细、还有一致性测试套件的标准，现实里仍有大量实现不完全符合规范。

核心问题不是"有没有 Spec"，而是：
1. 是否承认 Spec 永远不可能完备
2. 是否为"Spec 之外"预留了快速反馈和修正机制
3. 是否把工程质量建立在可验证循环上，而不是文档完整性的幻觉上

### 当 Spec 追求速度时，会退化成"文档形状的 Slop"

在"快速交付"压力下，Spec 经常变成：
- 句子很多、术语很全、结构看起来很专业
- 但缺乏真正的设计判断和一致性

Gabriella 批评某些章节像"specification-shaped sentences"——像规格说明，但不是规格说明该有的思考密度。

双重信息压缩陷阱：
1. 先用 AI 快速生成"像样的 Spec"
2. 再让 AI 按这份 Spec 生成代码

实际上是两次信息压缩和两次语义漂移，每层看着都在"提炼"，但总信息量很可能在流失。

## 相关概念

- **[[Harness-Engineering]]** — 给 AI 约束而非自由发挥的工程实践，SDD 是其中的计划与验证环节
- **[[Cursor]]** — AI 原生 IDE，SDD 全栈开发中的核心执行工具
- **技术写作 (Technical Writing)** — 与 SDD 密切相关，但真正的技术写作追求的是关键决策和关键约束的记录，而非前置完整性
- **瀑布流开发 (Waterfall)** — SDD 在实践中的潜在退化的目标模式
- **Coding Agent** — SDD 依赖的 AI 编程工具（Cursor、Claude Code、Codex 等）
- **Spec 作为代码** — 当 Spec 写到足够精确时，本质上已是另一种语法的编程
- **信息漂移 (Semantic Drift)** — 多层抽象传递中总信息量的流失
- **隐性功能陷阱** — AI 在模仿参考代码时会自动复刻隐性功能（如变量/表单清除、数据格式转换、默认值补齐），这些功能可能未写入文档，开发者需在 Review 中识别

## 相关人物与文章

- **Gabriella Gonzalez**：《A sufficiently detailed spec is code》作者
- **STRRL**：《我对 Spec Driven Development 的看法》作者
- **Edsger W. Dijkstra**：关于窄接口与表达精度的观点引用

## 务实做法

原文作者提倡的保守而务实的方法：
1. 重要系统照样写设计文档，但只写关键决策和关键约束
2. 不追求前置完整性，追求小步可验证
3. 让 Agent 快速产出，但把质量建立在测试、回归和审查闭环上
4. 文档服务于沟通和决策，不服务于"幻想一次性正确"

## 实践版 SDD：三级文档结构

在 Agent 系统中，SDD 的实践形态可以简化为三个文件：

| 文件 | 用途 | 产出物 |
|------|------|--------|
| **spec.md** | 规格说明 | 目标、验收标准、约束条件 |
| **plan.md** | 技术方案 | 涉及文件、实现步骤、架构决策 |
| **tasks.md** | 任务清单 | 每一步的描述、状态、责任人 |

这种"三级文档结构"在 [[24h-打工人]] 系统中被验证为有效：每个需求处理完会留下一组完整文档，使得失败可复盘、成功可固化、Agent 自己修自己的 bug 成为可能。

### Qoder 实践版：SDD 四阶段（Specify→Plan→Implement→Validate）

高德团队在 [[Qoder]] 平台上的 SDD 实践将流程标准化为四个阶段：

1. **Specify（定义规范）**：开发者与 AI 探讨，输出结构化规范，定义用户故事、验收标准和系统约束。AI 会主动提问，引导开发者澄清隐性知识（如登录方式、密码强度等）。输出包含：数据模型、接口规范、验收标准。

2. **Plan（制定计划）**：AI 像编译器一样，将规范"编译"成详细的技术方案和任务拆解列表。这是"技术文档"阶段。

3. **Implement（执行落地）**：AI Agent 逐个执行任务列表，自动生成高质量代码。在 [[Qoder]] 中通过专家团模式（Experts Mode）按任务类型分配给不同角色的 Agent。

4. **Validate（验证闭环）**：根据规范自动生成测试用例并执行，确保生成的代码与规范完全契合。验收标准必须是可测试的、无歧义的。

**核心区别**：与已有 SDD 实践（如得物技术的 [[24h-打工人]] 系统）相比，Qoder 的 SDD 更强调 **HITL（Human-In-The-Loop，人在回路）**——需求中有大量"隐性知识"，AI 通过主动提问来引导澄清，而非全自动运转。这实际上是对 SDD 常见批评（"退化成瀑布流"）的回应：通过 HITL 保留人类判断在关键节点上的介入。

### 关键实践原则

- **spec → plan → tasks 的线形分解**：把模糊需求逐步转成可执行单元
- **constitution.md**：架构约束文件，定义代码组织规范、命名规则、模块边界。AI 在生成方案时会自动遵循这些约束
- **留痕即是进化**：每一步的 prompt、输出、错误全记录——没有这些记录，系统只能不断"重来一次"；有了这些记录，系统才可能真正"学会下次做得更好"

## 关键洞见

> 把 Spec 当成可以替代工程判断、替代迭代反馈、替代长期维护经验的"银弹"，太天真了。软件工程最贵的成本从来不是"敲代码"这件事，而是理解问题、做出取舍、承担后果。

## 相关实体

- [[Harness-Engineering]] — SDD 是 Harness Engineering 中计划与分解环节的实践
- [[Cursor]] — SDD 全栈开发中的核心 AI IDE 工具
- [[VibeCoding]] — 与 SDD 形成对照：一种无规范、凭感觉的编程方式
- [[Agentic-CI-CD]] — SDD 驱动的多 Agent 并行开发的 CI/CD 实践

## 参考文献

- [你以为在写 Spec，其实是在绕路写代码](https://mp.weixin.qq.com/s/3Nae-CPATJudXgNrJR0vig)（原文，翻译自 Gabriella Gonzalez 原作）
- [基于 Harness + SDD + 多仓管理模式的 AI 全栈开发实践](https://mp.weixin.qq.com/s/ygQGSH5c7GHYDvkqWoQTXQ)（得物技术, 2026.05）
- [[多仓管理]]
- [十年老技术开发的 AI Agent 探索之路](https://mp.weixin.qq.com/s/r__2l_u6oXwzWHyD3wu_ZQ)（zhiyuanfu, 2026.05）— 实践版 SDD 完整实现
- [告别"氛围编程"：基于 Harness 治理和 SDD 的团队级 AI 研发范式演进与实践](https://mp.weixin.qq.com/s/-_IBJFuXpvoqMJxL9oaEJQ)（高德大模型应用平台·王树新, 2026.05）— SDD 四阶段（Specify/Plan/Implement/Validate）+ HITL 实践