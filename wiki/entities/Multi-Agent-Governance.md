---
tags: [AI-Agent, Multi-Agent, 工程治理, Harness-Engineering]
created: 2026-05-21
updated: 2026-05-21
sources: ["2026-05-21-QQ音乐Harness_Engineering实践.md"]
---

# Multi-Agent-Governance

> QQ音乐Harness Engineering中的Multi-Agent治理：让团队的AI协作跨会话、跨工具、跨服务可治理

## 简介

Multi-Agent-Governance（多Agent治理）是QQ音乐Harness Engineering框架的核心组成部分，解决的是真实企业级研发场景中的协作复杂度：**一个需求从开始到上线要经历多个Agent、多个工具、多个服务、多个仓库的协同**。

公式：**Team Agent Governance = Multi-Agent × Multi-Service × Multi-Lifecycle**

## 核心要素

### 1. 多Agent协作

- **24个专家Agent**：按阶段组织的专业化角色（需求评审、设计评审、代码审查等）
- **阶段4.4的8维度并行审查**：设计一致性、复杂度、并发安全、错误处理、安全漏洞、契约一致性、追溯性、辅助检查
- **Agent可调工具、调Skill**，形成层级化的任务委派体系

### 2. 多服务协同

- 50+微服务的单仓多服务场景
- 服务拓扑通过Service-Matrix声明
- 影响面分析从"搜索"变成"查表"

### 3. 多生命周期管理

- 需求生命周期：TAPD单 → 需求评审 → 技术方案 → IDL变更 → 代码实现 → 测试验证 → CR → 灰度上线
- 跨会话状态持久化
- Checkpoint/Crash Recovery

## 与Harness Engineering的关系

Multi-Agent-Governance是Harness Engineering在"Team Agent"层面的具体实现，属于L5工程治理层的核心能力。

## 相关页面

- [[Five-Stage-Gate-Workflow]] - 五阶段四门禁流程
- [[Service-Matrix]] - 服务矩阵
- [[Three-Warehouse-Linkage]] - 三仓联动
- [[Self-Refinement-Loop]] - 自我改进闭环
