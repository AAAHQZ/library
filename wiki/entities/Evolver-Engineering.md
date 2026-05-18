---
title: "Evolver-Engineering（进化工程）"
description: 关注AI-Agent离线能力增长的工程方法论，与Harness工程互补
tags: [AI-Agent, Harness-Engineering, 进化工程, Self-Evolution]
date: 2026-05-11
type: entity
confidence: EXTRACTED
source: 技术预测：从智能体的Harness驾驭工程到Evolver进化工程
---

# Evolver-Engineering（进化工程）

> 与 Harness（驾驭）工程互补，关注 AI-Agent 的离线"能力增长"问题。

## 核心问题

Evolver 工程要回答：如何让智能体"越用越好用"？

- 如何避免犯重复错误？
- SKILL/提示词 是否有改善空间？
- 如何降低 LLM Token 成本？
- 如何从对话记录提炼出适合的 SKILL/SOP？
- 基础模型更新后如何改进提示词/SKILL/Memory？

## Self-Evolution 路径

在**不触及基础模型**前提下实现自进化，三大路径：

1. **压缩** — 规约化小技能
2. **量化** — 对 SKILL/Memory 要素打分，删减负能力要素
3. **成本** — 把 Token 成本优化作为核心目标

### GENE 方法（规约化小技能）

- 构建规约化小技能（GEP）
- 小技能不考虑阅读性，只追求简洁、可靠
- 标准可复用的技能大小和规模是开放的

### 量化打分方法

- 量化上下文要素对完成任务的贡献
- 给每个 SKILL/Memory 进行打分
- 删减负能力要素，实现能力提升

## 与 Harness 工程的关系

| | Harness 工程 | Evolver 工程 |
|--|------------|-------------|
| 关注点 | 单次任务执行 | 能力增长 |
| 问题 | 如何让 Agent 完成任务 | 如何让 Agent 越用越好 |
| 时机 | 在线/实时 | 离线/积累后 |

## 代表案例

- **Hermas Agent** — 进化工程典型（2026年4月大热）
- **Evomap** — 进化工程实践
- **GenericAgent** — 以节省 Token 为出发点

## 相关实体

- [[Harness-Engineering]] — 驾驭工程，与进化工程互补
- [[AI-Agent]] — 智能体的两种工程维度

## 相关素材

- [[2026-05-11-技术预测：从智能体的Harness驾驭工程到Evolver进化工程|sources/2026-05-11-技术预测：从智能体的Harness驾驭工程到Evolver进化工程]]
