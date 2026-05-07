---
tags: [素材摘要]
created: 2026-05-07
updated: 2026-05-07
source_type: 微信公众号
source_path: raw/wechat/2026-05-07-告别"氛围编程"：基于_Harness_治理和_SDD_的团队级_AI_研发范式演进与实践.md
original_url: https://mp.weixin.qq.com/s/-_IBJFuXpvoqMJxL9oaEJQ
---

# 告别"氛围编程"：基于 Harness 治理和 SDD 的团队级 AI 研发范式演进与实践

> 高德大模型应用平台王树新分享团队级 AI 研发范式从"氛围编程"向"规范驱动、工程治理"演进的实践，提出出码率提升不等于真正提效的三个核心原因，并给出 SDD + Harness 的系统解法。

## 基本信息

- **来源类型**：微信公众号（阿里妹导读）
- **原文位置**：https://mp.weixin.qq.com/s/-_IBJFuXpvoqMJxL9oaEJQ
- **作者**：王树新（高德大模型应用平台）
- **消化日期**：2026-05-07

## 核心观点

1. **出码率提升不等于真正提效**：团队出码率从 53% 提升到 80-90%，但项目交付周期没有明显缩短。AI 写了更多代码，开发者的工作量并没有减少。原因有三：研发是全链路过程（编码只占 30%）、存量应用 Vibe Coding 风险极高、大型需求超出单次 AI 对话能力边界。

2. **从 Vibe Coding 走向 SDD（规范驱动开发）**：SDD 的核心理念是规范不再是写给人类看的散文，而是结构化的、可被 AI Agent 精确理解和执行的"意图代码"。SDD 四阶段：Specify（定义规范）→ Plan（制定计划）→ Implement（执行落地）→ Validate（验证闭环）。验收标准必须是可测试的、无歧义的，这是 SDD 的核心。

3. **Harness Engineering 的四大支柱**：上下文工程（结构化信息投喂）、架构约束（物理手段强制 AI 遵守规则）、反馈回路与熵管理（自动测试沙箱 + 自我修正 + 规则固化）、人类监督（从"写代码的人"变"审核员和环境设计师"）。

4. **专家团模式（Experts Mode）**：大型任务拆解为可管理的小任务，按任务类型分配给不同角色的 Agent。系统内置五类专家，每类有独立工具集。人类角色从开发者变为与 Experts Leader 澄清意图、对齐方向、审核计划、验收结果。

5. **知识库三层结构**：项目层（概述、架构、技术选型）、技术层（编码规范、中间件、最佳实践）、资产层（可复用代码片段、组件、模板）。维护一个顶层 README.md 作为"单一事实来源"。

## 关键概念

- [[Qoder]] — 阿里巴巴 AI 编程平台/工具，本文是 Qoder 在高德团队的实践
- [[Harness-Engineering]] — 四大支柱框架：上下文工程、架构约束、反馈回路、人类监督
- [[Spec驱动开发]] — SDD 四阶段：Specify→Plan→Implement→Validate，强调 HITL（人在回路）
- [[VibeCoding]] — 本文系统性地批判了"氛围编程"的局限性
- [[Context-Engineering]] — Harness 四大支柱之一，强调结构化信息投喂
- [[MCP]] — 用于 CI/CD 集成部署（通过 Aone 平台）
- [[AI-Agent]] — 专家团模式的多 Agent 协作
- [[AI生码]] — Qoder 出码率实践
- [[研发团队管理]] — 出码率与提效的关系分析

## 与其他素材的关联

- 与 [[sources/2026-05-06-基于Harness+SDD+多仓管理模式的AI全栈开发实践|基于 Harness + SDD + 多仓管理模式的 AI 全栈开发实践（得物技术）]] 的关联：两篇都系统性地结合 SDD 和 Harness Engineering 进行实践。得物侧重多仓管理和 Cursor 工具链，本文侧重 Qoder 平台和高德团队的 HITL 实践。互为补充。
- 与 [[sources/2026-05-05-从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering|从玩具到生产力用真实项目讲透 AI Agent 的 Harness Engineering]] 的关联：该文侧重 Harness Engineering 的基础概念，本文则是在 Qoder 平台上的工程化落地。
- 与 [[sources/2026-04-15-深度解析HarnessEngineering|深度解析 Harness Engineering]] 的关联：本文的 Harness 四大支柱与该文的理论框架高度吻合，是具体实践案例。
- 与 [[sources/2026-05-05-VibeCoding是中年男人的钓鱼|Vibe Coding 是中年男人的钓鱼]] 的关联：本文对 Vibe Coding 的批判更加系统化，指出了存量应用中 Vibe Coding 的三大风险。

## 原文精彩摘录

> 出码率提升了，但项目交付周期没有明显缩短；AI 写了更多代码，但开发者的工作量并没有减少。

> SDD 的核心思想是：在 AI 写代码之前，必须先将人类模糊的想法转化为清晰、无歧义的结构化规范，让 AI 在可控的轨道上运行。

> 从提示词工程到上下文工程，再到 Harness Engineering，这是一个范式转移：从"怎么跟 AI 说话"，到"AI 应该看到什么"，再到"AI 如何在受控环境中运行"。

> 开发者不再是被动的代码编写者，而是主动的需求定义者、规范审核者、结果验证者。AI 不再是不可控的黑盒，而是在 Harness 约束下的可靠工具。

## 相关页面

- [[Qoder]]
- [[Harness-Engineering]]
- [[Spec驱动开发]]
- [[VibeCoding]]
- [[Context-Engineering]]
