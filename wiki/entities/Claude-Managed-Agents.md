---
title: "Claude Managed Agents (CMA)"
date: 2026-05-06
tags:
  - Anthropic
  - AI Agent
  - 托管服务
type: entity
confidence: EXTRACTED
---

# Claude Managed Agents (CMA)

> Anthropic 推出的托管 Agent 运行时服务，打包了 Agent 循环 + 工具执行 + 沙盒容器 + 状态持久化。

## 产品定位

CMA 既不是新模型，也不是新 API endpoint，而是一套打包了 Agent 循环 + 工具执行 + 沙盒容器 + 状态持久化的托管运行时。与 Messages API（无状态直接模型访问）的边界清晰：CMA 提供有状态的托管基础设施。

## 四件套抽象

1. **Agent**：可复用的、版本化的配置实体，包含底层模型、系统提示词、工具集、MCP 服务器、Skills
2. **Environment**：云端容器模板，定义 Agent 运行的虚拟边界
3. **Session**：带持久化状态的长周期载体，底层是只追加（append-only）的事件日志
4. **Events**：应用与 Agent 之间双向通信，通过 SSE 长连接推送

## 五项底层技术

1. **Harness/Sandbox 解耦**：Session 独立于模型上下文窗口持久化，Sandbox 崩溃后可无缝续跑
2. **Vault/Proxy 凭证隔离**：OAuth token 存放在独立 Vault，通过专用 Proxy 转发，真实凭证永不进入 Sandbox
3. **Memory Store OCC**：SHA-256 乐观并发控制，单条上限 100KB，Session 最多挂 8 个
4. **Outcomes 目标闭环**：独立评估器按 Rubric 评分，主 Agent 自主迭代，默认最多 3 次
5. **单层委派控制**：子 Agent 无权继续向下裂变，防止递归死循环

## 计费模型

按 Session 计费，同时调度 CPU 侧沙箱与 GPU 侧推理：
- Claude Token 费率（标准）
- 活跃运行时：$0.08/会话小时（精确到毫秒）

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
