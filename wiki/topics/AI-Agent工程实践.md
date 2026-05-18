---
title: AI-Agent工程实践
tags: [AI-Agent, Agent工程, Harness, SDLC, 平台工程]
description: 关于AI-Agent的架构设计、工程化实践、Harness工程方法论
sources: []
date: 2026-05-06
---

# AI-Agent工程实践

> AI-Agent 从原型到生产的工程化方法论，涉及架构设计、Harness工程、技能系统、上下文工程等核心主题。

## 核心概念

- [[Harness-Engineering]] — Anthropic 提出的 Agent 工程方法论，强调将 Agent 视为"牲口而非宠物"进行管理
- [[MCP]] — Model Context Protocol，Anthropic 提出的 Agent 工具调用标准化协议
- [[Skills]] — Agent 技能系统，Claude Code 的可扩展技能机制
- Agentic CI/CD — Agent 驱动的持续集成/持续交付流程变革
- [[分级处置]] — 基于风险分级的 Agent 任务处置策略
- [[混合流水线]] — 传统流水线与 AI Agent 流水线的混合编排

## 主要观点

### Harness 哲学
Anthropic 将运维领域的"宠物 vs 牲口"哲学引入 AI Agent 设计，通过 [[Session]]/[[Harness]]/[[Sandbox]] 三层解耦，让 Agent 基础设施从应用层沉入基础设施层，实现可替代、可扩展、安全的托管服务。

### SDLC 全链路覆盖
AI-Agent 工程实践覆盖需求到部署的完整软件生命周期：结构化需求采集、技能系统设计、格式检查、上下文分层拆解、自动化执行。

### 工程挑战
- [[Agent隔离]] — 多 Agent 场景下的安全隔离策略
- 上下文工程 — 上下文的组织、管理与注入方式
- [[Agent-Memory框架]] — Agent 记忆系统的设计与实现
- [[身份权限管理]] — Agent 时代的 IAM 问题

## 相关素材

- [[2026-05-11-小红书PMO-Agentic探索之路|sources/2026-05-11-小红书PMO-Agentic探索之路]] — 小红书PMO团队4轮Agentic项目管理迭代完整记录，含PMOBP Agent架构、项目主数据、评测集实践
- [[2026-05-11-Claude-Code_Harness失控_规则遵循与上下文退化|sources/2026-05-11-Claude-Code_Harness失控_规则遵循与上下文退化]] — Claude Code 规则遵循失败与 200k 上下文退化问题
- [[2026-05-11-天猫新品营销技术团队AI编码实战指南（上）|sources/2026-05-11-天猫新品营销技术团队AI编码实战指南（上）]] — 天猫团队的AI编码实战指南，含需求驱动型/工程主导型分类与视图分离实践
- [[2026-05-05-Anthropic的Harness哲学把Agent当牲口而非宠物|sources/2026-05-05-Anthropic的Harness哲学把Agent当牲口而非宠物]]
- [[2026-05-05-从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering|sources/2026-05-05-从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering]]
- [[2026-05-05-123个结构化需求20个技能5种格式检查器3层上下文拆解Warp|sources/2026-05-05-123个结构化需求20个技能5种格式检查器3层上下文拆解Warp]]
- [[2026-04-15-深度解析HarnessEngineering|sources/2026-04-15-深度解析HarnessEngineering]]
- [[2026-04-22-AI-Agent框架选型与工程实践|sources/2026-04-22-AI-Agent框架选型与工程实践]]
- [[2026-05-11-技术预测：从智能体的Harness驾驭工程到Evolver进化工程|sources/2026-05-11-技术预测：从智能体的Harness驾驭工程到Evolver进化工程]] — Evolver Engineering 新概念，与 Harness Engineering 互补
- [[2026-05-10-Anthropic工程师的HTML用法|sources/2026-05-10-Anthropic工程师的HTML用法]] — HTML 作为 AI 输出格式的人机协作新范式

## 关联实体

- [[Harness-Engineering]]
- [[MCP]]
- [[Skills]]
- [[Agentic-CI-CD]]
- [[分级处置]]
- [[混合流水线]]
