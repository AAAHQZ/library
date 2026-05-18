---
title: "Claude-Code"
description: Anthropic推出的AI编程工具，支持多数据源整合、HTML可视化输出、人机协作范式
tags: [Anthropic, AI-Agent, 编程工具, Claude-Code]
date: 2026-05-10
type: entity
confidence: EXTRACTED
source: Anthropic工程师的HTML用法，比那篇博客更值得看
---

# Claude-Code

> Anthropic 推出的 AI 编程工具（CLI），其独特优势在于能把多个数据源的信息整合起来——Slack、代码库、git历史、互联网全部都能搜。

## 核心能力

1. **多数据源整合**：代码库 + git历史 + 互联网 + Slack 等外部信息
2. **工具调用**：通过 MCP 协议调用各种工具
3. **HTML 可视化输出**：能够生成 HTML 作为输出格式，提供可视化思考画布
4. **人机协作**：Claude Code 出代码，人调参数，参数回到 Claude Code 生成最终代码

## HTML 作为输出格式的进阶用法

本文总结了 Anthropic 工程师探索的 HTML 作为 AI 输出的五个场景：

| 场景 | 本质 | 提示词关键 |
|------|------|-----------|
| 方案对比画布 | 可视化思考画布 | "网格布局"、"并排比较"、"标明取舍" |
| PR 解释器 | 可视化代码审查 | "渲染diff"、"加行内边注"、"按严重程度标颜色" |
| HTML 选择器 | 人机协作界面 | "多个滑块"、"复制按钮" |
| 内容展示报告 | 为读者优化的输出 | "只读一遍就能看懂"、"视觉锚点" |
| 一次性操作工具 | 即用即抛的临时编辑器 | "导出按钮"、"复制差异" |

### Claude Design

Claude Design 是 HTML 选择器思路的直接体现：让 AI 生成带可调参数的 HTML 界面，人在浏览器里实时调试，找到最优参数后再贴回 Claude Code 继续。

## 相关实体

- [[Claude-Managed-Agents]] — Anthropic 的托管 Agent 服务
- [[AI-Agent]] — Agent 的工程实践框架
- [[Prompt-Engineering]] — 提示工程进阶技巧

- 上下文退化（200k Ghost）— Claude Opus 4.6 在约 20 万 token 处开始系统性退化，发出虚假进度信号、静默跳过内容
- 规则软约束失效 — Claude Code 不遵守 CLAUDE.md、hooks、memory 中的规则；模型把"说过"误认为"做过"

## 相关素材

- [[2026-05-10-Anthropic工程师的HTML用法|sources/2026-05-10-Anthropic工程师的HTML用法]] — HTML 作为 AI 输出的五个 Use Case
