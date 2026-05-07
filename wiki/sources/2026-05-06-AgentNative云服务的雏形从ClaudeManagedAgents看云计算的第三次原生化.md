---
title: "Agent Native云服务的雏形：从ClaudeManagedAgents看云计算的第三次原生化"
slug: agent-native-cloud-claude-managed-agents
tags: [Agent-Native, Claude-Managed-Agents, 云计算, 云原生, Anthropic]
created: 2026-05-06
updated: 2026-05-06
source: ../raw/wechat/AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化.md
references:
  - https://mp.weixin.qq.com/s/cMgOmPiGdxGUmWf1R5PKYA
---

# Agent Native 云服务的雏形

> 从 Claude Managed Agents（CMA）分析云计算的第三次原生化跃迁。论证 Cloud Native（CPU/VM）和 AI Native（GPU/NeoCloud）都无法原生承载 Agent Workload，Agent workload 独有的三个特征催生 Agent Native 云服务。CMA 四件套抽象和五大关键技术分析，产业价值链重分配预测。

## 核心观点

1. **第三代云形态**：Agent Native 云服务是继 Cloud Native、AI Native 之后的第三次原生化
2. **虚拟化对象跃迁**：从"硬件虚拟化"到"认知工作流虚拟化"
3. **CMA 是融合编排层**：建立在经典云 CPU 算力与 NeoCloud GPU 算力之上，统一暴露 Agent 原语

## 为什么前两代云无法承载 Agent Workload

**Agent workload 三个独有特征**：
1. **不可信代码的动态生成与执行**：LLM 即兴生成代码，行为空间无界
2. **长周期异步执行**：数分钟到数小时，需容忍断连、崩溃、上下文溢出
3. **LLM 推理与通用计算高频交错**：Reason-Act 循环每轮都 GPU 推理 + CPU 工具调用

## CMA 四件套领域抽象

| 概念 | 作用 |
|------|------|
| **Agent** | 可复用、版本化的配置实体（模型、系统提示词、工具集、MCP、Skills） |
| **Environment** | 云端容器模板（预装运行时、网络规则、挂载文件系统） |
| **Session** | 带持久化状态的长周期载体，底层是 append-only 事件日志 |
| **Events** | 应用与 Agent 双向通信，通过 SSE 推送 |

## CMA 五项关键技术

1. **Harness 与 Sandbox 解耦**：Session 是 append-only 日志，Sandbox 崩溃可无缝续跑
2. **凭证物理隔离（Vault/Proxy）**：Sandbox 永远不接触真实凭证
3. **Memory Store OCC**：SHA-256 哈希防并发覆盖
4. **Outcomes 目标闭环**：独立评估器 + Rubric + 自主迭代（默认 3 次，上限 20 次）
5. **多 Agent 单层委派**：子 Agent 无权继续裂变，防止雪崩

## 产业冲击

- **Agent Native 云厂商**：Anthropic 握先手，OpenAI 生态领先，Google 硬件整合最深
- **模型中立云厂商**：长期风险是规模效应利好垂直整合玩家
- **NeoCloud**：被迫向上延伸或与模型厂商深度绑定
- **SaaS**：席位模式受冲击，合规类几乎不受影响

## 相关页面

- [[Agent-Native]]
- [[Claude-Managed-Agents]]
- [[Harness-Engineering]]
- [[Sandbox]]
