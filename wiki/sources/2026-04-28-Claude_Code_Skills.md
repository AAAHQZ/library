---
title: "Claude Code 是需要管的，实测一个靠谱的 Skills"
slug: claude-code-skills
summary: 通过实测对比验证 Karpathy 风格的 CLAUDE.md 约束（编码前思考、简洁优先、精准修改、目标驱动执行）如何显著减少 Claude Code 的过度代码生成，使 32 行代码修改从 +95 行降至 +38 行。
description: 介绍 andrej-karpathy-skills 这组 Skills，通过对照实验验证其对 Claude Code 行为的约束效果。
tags:
  - Claude Code
  - Skills
  - CLAUDE.md
  - Karpathy
  - AI 编程
  - Agent 约束
  - 代码质量
created: 2026-04-28
source: ../raw/Claude_Code_Skills.md
images:
  - ../raw/Claude_Code_Skills_image_001.png
---

# Claude Code 是需要管的，实测一个靠谱的 Skills

## 核心观点

Karpathy 吐槽 LLM 写代码的三大毛病：
1. 模型代你做错误假设，不管理自身困惑，不寻求澄清
2. 喜欢把代码和 API 搞复杂，堆砌抽象概念
3. 改动或删除自己理解不足的代码和注释

## 四条核心原则

### 1. 编码前思考（Think Before Coding）
- 不要假设、不要藏住困惑
- 要把 tradeoff 摆出来，显式说明假设
- 不确定就问，有多种解释时全部列出来

### 2. 简洁优先（Simplicity First）
- 用最少的代码解决问题
- 用户没要的功能不要加
- 200 行能写成 50 行就重写

### 3. 精准修改（Surgical Changes）
- 只碰必须碰的
- 不要"改进"相邻的代码、注释、格式
- 不要重构没坏的东西

### 4. 目标驱动执行（Goal-Driven Execution）
- 把指令翻译成可验证的目标
- 循环执行直到达成
- 弱标准会让模型不停回头问，强标准让模型自己 loop

## 对照实验结果

| 组别 | 文件行数增量 | 原文件 32 行 |
|------|-------------|-------------|
| 不带 skill | **+95 行**（+197%） | 32 行 |
| 带 skill | **+6 行**（+19%） | 32 行 |

**结论**：带约束的模型解读为"把 print 换成 logger 止于此"，不带约束的解读为"把所有能想到的最佳实践全堆上去"。

## 安装方式

```bash
# A. Claude Code 插件（推荐）
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills

# B. 项目级 CLAUDE.md
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

## 适用场景

这套规则的真正价值在于：
- 用户描述模糊的需求
- 带"顺便"两个字的请求
- 跨多文件多模块的改动
- 用户自己也没想清楚要什么的时候

对于简单的拼写错误修复、显而易见的一行修改，两组 diff 几乎一样，无需完整严谨流程。

## 相关页面

- [[深度解析HarnessEngineering|sources/2026-04-15-深度解析HarnessEngineering]] - Harness Engineering 深度解析
- [[AI-Agent框架选型与工程实践|sources/2026-04-22-AI-Agent框架选型与工程实践]] - AI-Agent 框架选型与工程实践
