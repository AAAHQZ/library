---
title: "MCP (Model Context Protocol)"
date: 2026-05-06
tags:
  - AI Agent
  - 协议
  - DevOps
type: entity
confidence: EXTRACTED
---

# MCP (Model Context Protocol)

> Agent 与 DevOps 工具链之间的统一通信协议。

## 定义

MCP（Model Context Protocol）是一种标准化协议，试图为 Agent 与外部工具之间建立统一通信方式。它的意义类似"接口层"：让 Agent 能够以标准方式发现工具能力、传递上下文、发起调用并接收结果。

## 在DevOps场景中的应用

MCP Server 可以覆盖：
- 读取 GitHub 仓库状态、创建 Issue 或 PR
- 查询 Kubernetes Pod 日志、Deployment 状态和事件
- 访问 Terraform Registry 模块信息
- 读取可观测平台中的指标和追踪数据

## 价值

对企业而言，MCP 降低了工具链改造成本，也让 Agent 能力更容易复用。

## 相关素材

- [[2026-05-06-Agent驱动的CICD流程变革从自动化执行到智能自治|sources/2026-05-06-Agent驱动的CICD流程变革从自动化执行到智能自治]]
