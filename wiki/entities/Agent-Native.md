---
title: "Agent Native"
date: 2026-05-06
tags:
  - AI Agent
  - Cloud Native
  - 云计算
type: entity
confidence: EXTRACTED
---

# Agent Native

> 云计算的第三代原生化形态，为 Agent workload 原生设计。

## 定义

Agent Native 是继 Cloud Native（CPU算力+VM/容器）和 AI Native/NeoCloud（GPU算力+裸金属）之后的第三代云服务形态。它不是替换前两代云，而是建立在它们之上的融合编排层（Convergence Orchestration Layer），统一暴露 Agent 原语。

## 核心特征

- **虚拟化对象跃迁**：从"硬件虚拟化"到"认知工作流虚拟化"
  - Session 虚拟化"经验"
  - Harness 虚拟化"调度"
  - Sandbox 虚拟化"执行"
  - Vault 虚拟化"身份"
  - Memory Store 虚拟化"记忆"

## Agent workload 三大独有特征

1. **不可信代码的动态生成与执行**：Agent 由 LLM 即兴生成代码，行为空间无界
2. **长周期异步执行**：deep research 大型任务动辄数十分钟到数小时
3. **LLM 推理与通用计算的高频交错**：Reason-Act 循环每轮都在 GPU 侧推理、CPU 侧执行工具

## 三代云计算对比

| 代际 | 核心算力 | 底座 | 交付形态 | 典型场景 |
|------|----------|------|----------|----------|
| Cloud Native | CPU | VM/容器 | Web/企业应用 | EC2, RDS, K8s |
| AI Native | GPU | 裸金属/RDMA | LLM训练与推理 | CoreWeave, Lambda |
| Agent Native | 异构(CPU+GPU) | Agent原语 | Agent工作流 | CMA, AgentCore |

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
