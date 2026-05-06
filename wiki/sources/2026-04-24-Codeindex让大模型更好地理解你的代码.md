---
title: "Codeindex · 让大模型更好地理解你的代码"
slug: codeindex
summary: Codeindex 是一款代码语义化索引、检索和函数依赖图生成工具，支持增量索引。核心技术包括语义化摘要（基于 Tree-sitter AST 分块）、函数依赖图（KuzuDB/Postgres Age 图数据库）和 SDK/OpenAPI 双接口。应用于 CodeWiz 代码检索、AI CR 智能代码审查、CodeWiki 自动生成文档等场景。
description: 淘天集团跨端技术团队开发的代码语义索引工具，解析其核心能力、架构设计和应用场景。
tags:
  - Codeindex
  - 代码索引
  - 语义检索
  - 函数依赖图
  - 图数据库
  - KuzuDB
  - Tree-sitter
  - 淘天集团
  - AI Agent
  - RAG
created: 2026-04-24
source: ../raw/Codeindex · 让大模型更好地理解你的代码.md
images:
  - ../raw/Codeindex让大模型更好地理解你的代码_image_001.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_002.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_003.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_004.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_005.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_006.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_007.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_008.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_009.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_010.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_011.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_012.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_013.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_014.jpg
  - ../raw/Codeindex让大模型更好地理解你的代码_image_015.jpg
---

# Codeindex · 让大模型更好地理解你的代码

## 核心能力

### 1. 语义化索引与检索
- 增量语义化索引：根据仓库及分支对代码建立索引，存储文件哈希值，二次索引可复用
- 语义化检索：通过语义化描述检索代码仓库中相关的代码片段及其语义化摘要

### 2. 语义化摘要
- **codeChunker**：利用 Tree-sitter 解析器生成 AST，基于语法结构进行分块
  - 若 Class 内代码超出 chunk token 上限，对内部函数体省略，保持 Class 整体结构完整
  - 对 Class 内部的函数成员二次处理，单独进行 Chunk 拆分
- **basicChunker**：简单的基于行的分块器，适用于纯文本和 Markdown 文件

### 3. 函数依赖图
- 分层架构设计：Parser 适配层 + GraphDB 适配层
- 嵌入式中图数据库采用 KuzuDB，线上使用 Postgres Age 插件
- 图数据模型：Files、Functions、Contains、FunctionCalls、FileCalls、Imports、Exports、FunctionContains

### 4. 应用场景
| 应用 | 说明 |
|------|------|
| CodeWiz | 基于 codeindex 索引/检索能力的代码库索引功能 |
| AI CR Agent | 智能代码审查，获取函数依赖上下游信息，判断变更影响 |
| CodeWiki | 根据仓库代码自动生成 wiki 文档 |

## 技术架构

```
Codeindex
├── SDK（客户端库）
└── OpenAPI（服务端接口，依赖 Node.js SDK 实现）
    ├── 语义化检索 → SDK/OpenAPI
    ├── 语义化摘要 → Tree-sitter
    ├── 函数依赖图 → Tree-sitter, KuzuDB, Postgres Age
    └── 增量索引 → SDK/OpenAPI
```

## 图数据模型

| 节点/关系 | 类型 | 说明 |
|-----------|------|------|
| Files | 节点 | 代码库中每个文件的基本信息 |
| Functions | 节点 | 代码中定义的函数信息 |
| Contains | 关系 | 文件与函数的包含关系 |
| FunctionCalls | 关系 | 函数之间的调用关系 |
| FileCalls | 关系 | 文件直接调用函数的关系 |
| Imports | 关系 | 文件之间的导入/导出关系 |
| Exports | 关系 | 文件导出函数的关系 |
| FunctionContains | 关系 | 函数内部定义其他函数（嵌套函数） |

## 相关页面
- Codeindex 知识图谱
- [[深度解析HarnessEngineering|sources/2026-04-15-深度解析HarnessEngineering]] - Harness Engineering 深度解析
- [[AI-Agent框架选型与工程实践|sources/2026-04-22-AI-Agent框架选型与工程实践]] - AI-Agent 框架选型与工程实践
