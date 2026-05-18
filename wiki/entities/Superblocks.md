---
title: "Superblocks"
date: 2026-05-11
tags:
  - AI编程
  - 平台工程
  - 企业级AI
type: entity
confidence: EXTRACTED
---

# Superblocks

> 一个面向企业的低代码/高代码混合开发平台，核心创新是通过 Clark AI 和三层工作流（Generate/Refine/Extend）为 Vibe Coding 提供企业级治理能力。

## 概述

Superblocks 是一个企业级应用开发平台，其 2.0 版本通过引入 Clark AI 专门解决 Vibe Coding 在企业环境中的治理难题。与传统低代码平台不同，Superblocks 强调"双向同步"——既能通过 AI 生成代码，也能通过可视化编辑器 human-in-the-loop，还能与 VS Code 等本地 IDE 实时同步进行硬核调试。

## 核心组件

### Clark AI

Superblocks 2.0 的核心智能代理，与通用 LLM 不同，专门为企业级应用设计。Clark AI 在生成代码的每一步都受平台内置"治理引擎"约束，会主动添加 RBAC 鉴权中间件等安全校验。

### 三层工作流

1. **Generate（生成）**：AI 处理 90% 的繁琐工作，用户只需描述需求
2. **Refine（精简）**：可视化编辑器下的"人在回路"，在代码"硬化"前提供缓冲区
3. **Extend（扩展）**：与 VS Code 实时同步，支持原生 TypeScript 代码重构

## 企业级特性

- **MCP 管理控制平面**：IT 和安全团队可实时审计每个应用的权限、每一行访问生产数据的操作
- **VPC 部署支持**：数据运行在企业自有的受控基础设施中
- **气隙环境（Air-gapped）支持**：数据不用于模型训练，也不泄露到公网

## 典型客户

- Instacart
- SoFi（金融科技）

## 相关页面

- [[Clark-AI]]
- [[VibeCoding]]
- [[Harness-Engineering]]
- [[理解债]]
