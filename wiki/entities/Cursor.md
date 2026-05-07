---
title: "Cursor"
date: 2026-05-06
tags:
  - AI编程
  - AI Agent
  - 工具
  - IDE
type: entity
confidence: EXTRACTED
source: 得物技术全栈开发实践
---

# Cursor

> AI 原生代码编辑器，支持语义索引（Codebase Indexing）、多 Tab 并行编程、@引用等核心能力，是当前最主流的 AI IDE 之一。

## 产品定位

Cursor 是一款以 AI 为核心设计的代码编辑器（VS Code 分支），通过深度集成 LLM 实现自然语言驱动的代码生成、重构和调试。被广泛认为是"**最好用的 AI IDE**"，适合大多数开发者的日常编码。

与其他 AI 编码工具的定位：

| 工具 | 定位 |
|------|------|
| **Cursor** | AI IDE，深度集成编辑器体验，适合快速迭代 |
| **Claude Code** | CLI 终端代理，适合长链路复杂任务 |
| **Codex (OpenAI)** | 终端代理，面向 Agent-First 架构 |
| **Pi** | 极简终端代理，可扩展 |

## 核心能力

### 1. Codebase Indexing（代码库语义索引）

Cursor 对工作区内所有代码进行向量化嵌入，建立语义索引，使 AI 能跨文件、跨仓库理解代码关系。

- 无需手动指定文件，AI 通过语义检索自动找到相关 Controller、Service、前端组件
- 跨仓库理解：前后端代码在同一工作区时，AI 生成后端接口时可参考前端调用方式，反之亦然
- 一次完整索引后，代码理解质量显著提升

### 2. 多 Tab 并行编程（多 Agent 协作）

Cursor 原生支持多个 AI 编程模式并行工作，每个 Tab 可独立负责不同的代码生成任务，互不阻塞。

- Tab 1 负责前端代码生成，Tab 2 负责后端代码生成
- 适合全栈开发场景下的前后端并行开发
- 无需手动注册子 Agent，开箱即用

### 3. @引用与文件上下文

通过 `@` 符号快速引用文件和代码段，将参考实现注入 AI 上下文。

- 支持文件名、代码段行号引用
- 快捷键、拖拽即可引用，操作便捷
- 是在 Harness 思维中"给 AI 模仿对象"的实操手段

### 4. `.cursor/rules/` 技能系统

支持通过 `.cursor/rules/*.mdc` 文件定义 AI 行为规则（Skills），规则在相关任务触发时自动加载。

- 纯 Markdown + YAML 元数据格式
- 可继承、可组合，类似 Claude Code 的 CLAUDE.md 机制
- 社区已有丰富规则模板（如 Karpathy 编码规范）

## Cursor vs Claude Code

| 维度 | Cursor | Claude Code |
|------|--------|-------------|
| 代码库语义索引 | 支持 grep + 语义相似度检索 | 仅支持 grep |
| 代码生成速度 | 极速（平均 < 1 分钟） | 中速（平均 3-30 分钟） |
| 代码采纳率 | 两者相当 | 两者相当 |
| 文件/代码引用 | 快捷键、拖拽 | 需手动 @文件路径 |
| 多 Agent | 默认开启（多 Tab 并行） | 需手动注册 Subagent |
| 费率模型 | 失败任务不收费 | 失败任务耗时长、易浪费 Token |
| 适用场景 | **快速迭代首选** | **长链路复杂任务** |

## 在 Harness 工程中的角色

Cursor 是 Harness Engineering 生态中的关键工具之一——作为"Harness"产品（Agent 运行时的驾驭层），它将自然语言意图转化为代码变更。

### Harness 思维下的使用

Harness 思维的核心是**给 AI 约束而非自由发挥**。在 Cursor 中实践：

- **提供参考实现**：通过 @ 引用相似功能的完整实现链路（Controller → Service → Repository → 前端组件）
- **复用已有模式**：数据结构、分层方式、命名风格保持与现有代码一致
- **约束生成范围**：在 Prompt 中明确指定参考文件和接口，而非让 AI 凭空创造

> 约束越精准，生成代码的可用性越高。

### 在 SDD 全栈开发中的位置

在得物技术提出的"Harness + SDD + 多 Agent"全栈开发方法论中，Cursor 扮演核心执行角色：

1. 搭建多仓工作区 → 利用 Codebase Indexing 跨仓库理解代码
2. 生成前后端 SDD 文档 → 通过 @ 引用和结构化 Prompt
3. 多 Tab 并行开发 → Tab 1 前端、Tab 2 后端
4. 分阶段验证 → Mock → 构建 → 联调

## 商业与历史

- 由 Anysphere 公司开发
- 被 Elon Musk/SpaceX 收购
- 曾短暂宣称毛利率为正，但截至某个时间点实际毛利率为负 23%
- 每小时约 1000 个 commit 通过 Cursor 生成

## 相关实体

- [[VibeCoding]] — Cursor 的典型使用场景之一（凭感觉编程）
- [[Harness-Engineering]] — Cursor 作为 Harness 工具的工程实践体系
- [[Spec驱动开发]] — Cursor 在 SDD 全栈开发中的应用
- [[Claude-Managed-Agents]] — Claude Code 的多 Agent 模式（Cursor 多 Tab 的对应方案）
- [[Pi]] — 极简终端代理（与 Cursor 形成对比定位）
- [[Skills]] — AI Agent 技能系统（Cursor 的 `.cursor/rules/` 机制）

## 参考文献

- [Cursor 官网](https://cursor.sh)
- [基于 Harness + SDD + 多仓管理模式的 AI 全栈开发实践｜得物技术](https://mp.weixin.qq.com/s/ygQGSH5c7GHYDvkqWoQTXQ) (2026-05-06)
- [三大 Agent 平台独立收敛到同一个 Markdown 方案](https://mp.weixin.qq.com/s/XA6OTtbsnfCbEr0Jbn0Uhw) (2026-05-06)
- [[多仓管理]]