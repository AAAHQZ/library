---
title: "AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化"
date: 2026-05-06
source: 微信公众号
url: https://mp.weixin.qq.com/s/cMgOmPiGdxGUmWf1R5PKYA
tags:
  - AI Agent
  - Cloud Native
  - Claude
  - Anthropic
  - Agent Native
type: source
confidence: EXTRACTED
---

# AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化

> 从 Claude Managed Agents 看云计算的第三次原生化

## 基本信息

- **来源**：微信公众号
- **原文链接**：https://mp.weixin.qq.com/s/cMgOmPiGdxGUmWf1R5PKYA
- **消化日期**：2026-05-06

## 核心观点

本文提出"Agent Native"作为云计算的第三代原生化形态：

1. **背景**：云计算经历了两代——Cloud Native（CPU算力+VM/容器）和 AI Native/NeoCloud（GPU算力+裸金属），都无法原生承载 Agent workload
2. **Agent workload 三大独有特征**：不可信代码动态生成、长周期异步执行、LLM推理与CPU工具调用高频交错
3. **CMA 四件套抽象**：Agent（配置实体）、Environment（容器模板）、Session（持久化事件日志）、Events（双向通信）
4. **底层技术**：Harness/Sandbox 解耦、Vau lt凭证隔离、Memory Store OCC、Outcomes目标闭环、单层委派控制
5. **产业冲击**：重分配 Agent Native 厂商、模型中立云厂商、NeoCloud、SaaS、iPaaS 的价值链

## 关键概念
— 云计算第三代原生化形态，为 Agent workload 原生设计
- [[Claude-Managed-Agents]] — Anthropic 的托管 Agent 运行时服务
- [[Harness]] — Agent 运行时的调度内核，负责模型推理和工具路由
- [[Sandbox]] — 隔离的代码执行域，与 Harness 解耦
- Session — 只追加的事件日志，持久化存储，独立于模型上下文窗口
- [[MCP]] — Model Context Protocol，统一接口协议
- [[Memory-Store]] — 跨 Session 的持久化记忆，OCC 并发控制
- [[Vault-Proxy]] — 凭证隔离架构，OAuth token 不进入 Sandbox
- [[NeoCloud]] — 面向 LLM 训练与推理的 GPU 专用云

## 精彩摘录

> Claude Managed Agents 不是新模型也不是新 API endpoint，而是打包了 Agent 循环、工具执行、沙盒容器、状态持久化的托管运行时，继以 CPU 算力为核心的 Cloud Native、以 GPU 算力为核心的 AI Native 之后，开启 Agent Native 的第三代云计算形态。

> Agent Native 的虚拟化对象，从硬件跃迁到了业务工作流逻辑：Session 虚拟化"经验"、Harness 虚拟化"调度"、Sandbox 虚拟化"执行"、Vault 虚拟化"身份"、Memory Store 虚拟化"记忆"。

## 与其他素材的关联

- 与 [[Anthropic的Harness哲学把Agent当牲口而非宠物|sources/2026-05-05-Anthropic的Harness哲学把Agent当牲口而非宠物]] 在 Harness 架构哲学上直接相关
- 与 [[从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering|sources/2026-05-05-从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering]] 在 Harness Engineering 框架上互相印证
