---
title: "Codeindex 知识图谱：能力-应用-基础设施拓扑视图"
slug: codeindex-knowledge-graph
summary: Codeindex 知识图谱的径向拓扑设计图。采用同心圆布局（Ring 1 核心能力 → Ring 2 应用场景 → Ring 3 基础设施），展示 Codeindex 的四项核心能力（语义化检索、语义化摘要、函数依赖图、增量索引）、三类应用（CodeWiz、AI CR Agent、CodeWiki）和五项基础设施组件（Tree-sitter、SDK、OpenAPI、Postgres Age、KuzuDB）之间的依赖与调用关系。核心能力层在圆心，应用层居中消费核心能力，基础设施层在最外支撑所有功能。
description: Codeindex 知识图谱可视化方案，以径向拓扑结构组织其核心能力、应用场景和基础设施，展示各组件之间的连接关系。
tags:
  - Codeindex
  - 知识图谱
  - 代码索引
  - 可视化
created: 2026-05-08
source: ../raw/codeindex-knowledge-graph/plan.md
---

# Codeindex 知识图谱：能力-应用-基础设施拓扑视图

## 基本信息

- **素材类型**：知识图谱可视化设计方案（径向拓扑 / 同心圆布局）
- **对应的详细文章**：[[sources/2026-04-24-Codeindex让大模型更好地理解你的代码|Codeindex · 让大模型更好地理解你的代码]]

## 核心观点

1. **三层径向结构**：Codeindex 的系统架构可以抽象为三层同心圆——最内是核心能力层（语义化检索/摘要/函数依赖图/增量索引），中间是应用层（CodeWiz/AI CR Agent/CodeWiki），最外是基础设施层（Tree-sitter 等工具链），层次结构清晰。
2. **能力到应用的映射**：语义化检索 → CodeWiz，函数依赖图 → AI CR Agent（智能代码审查），语义化摘要 → CodeWiki（自动文档生成），每条能力线都有明确的落地场景。
3. **基础设施依赖**：语义化摘要和函数依赖图依赖 Tree-sitter（AST 解析），函数依赖图同时依赖 KuzuDB/Postgres Age 图数据库，检索能力依赖 SDK/OpenAPI 双接口。这种依赖设计体现了"解析层 + 存储层 + 接口层"的解耦思路。
