---
tags: [素材摘要]
created: 2026-05-10
updated: 2026-05-10
source_type: 微信公众号
source_path: raw/wechat/2026-05-10-Vibe_Coding时代，每个项目都需要意图文件.md
confidence: EXTRACTED
---

# Vibe Coding 时代，每个项目都需要「意图文件」

> DESIGN.md 爆火背后，是大量开发者正在用 AI 写 UI 却缺乏传递设计意图的标准方式。意图文件（Intent File）正在成为软件项目里新的结构层——将人类意图持久化、结构化地传递给 Agent。

## 基本信息

- **来源类型**：微信公众号
- **原文位置**：raw/wechat/2026-05-10-Vibe_Coding时代，每个项目都需要意图文件.md
- **原文链接**：https://mp.weixin.qq.com/s/lOBRx_KgOBo_kqD05c5Jsw
- **消化日期**：2026-05-10

## 核心观点

1. **DESIGN.md 的爆发是一个信号**：VoltAgent 把 55 个品牌的设计风格整理成 DESIGN.md，三天 star 过万、现七万多，说明大量 AI 写 UI 的开发者卡在没有标准方式传递设计意图给 Agent。

2. **设计交付链正在长出新节点**：传统交付物（Figma、Storybook、Design Token）终点是人类工程师；现在终点变为 AI Coding Agent，Agent 读不好 Figma，需要专门写给 Agent 看的"意图文件"。

3. **模式正在扩散**：代码规范有 CLAUDE.md，UI 风格有 DESIGN.md，未来还会有 VOICE.md（产品语气）、DOMAIN.md（数据模型与业务规则）、测试标准等——凡是"人类知道但 Agent 不知道"的东西都需要意图文件。

4. **意图文件是 Vibe Coding 混乱的真正解药**：Vibe Coding 的根本问题是人类意图没有稳定存放地点（prompt 是一次性的）。意图文件是持久的、结构化的、人类维护的——不是靠更聪明的模型，而是靠更好的上下文设计。

## 关键概念

- [[意图文件]] —— 给 AI Agent 的工作上下文文件，人类意图的结构化表达
- [[VibeCoding]] —— 意图文件要解决的核心问题场景

## 与其他素材的关联

- 与 [[2026-05-05-VibeCoding是中年男人的钓鱼|sources/2026-05-05-VibeCoding是中年男人的钓鱼]] 的关系：从"Vibe Coding 是什么"前进到"Vibe Coding 的问题如何解决"
- 与 [[2026-05-07-告别氛围编程基于Harness治理和SDD的团队级AI研发范式演进与实践|sources/2026-05-07-告别氛围编程基于Harness治理和SDD的团队级AI研发范式演进与实践]] 的关系：从"需要工程治理"具体到"需要意图文件"这个具体工具
- 与 [[2026-05-06-三大Agent平台收敛到Markdown方案|sources/2026-05-06-三大Agent平台收敛到Markdown方案]] 的关系：Agent 记忆/上下文正在向 Markdown 文件收敛，意图文件是同一趋势的不同侧面

## 原文精彩摘录

> 这类文件有一个共同的特征：它们不是代码，不是配置，不是文档——它们是给 Agent 的工作上下文，是人类意图的结构化表达，是软件项目里正在长出来的新一层。

> 意图文件解决的正是这个问题。它是持久的，不随对话消亡；它是结构化的，Agent 能高效读取；它是人类维护的，所以它传递的是真正的意图，而不是 Agent 的猜测。

> DESIGN.md 让设计意图持久化，CLAUDE.md 让工程意图持久化。这两个文件放在项目根目录里，构成了 Agent 工作的稳定地基——不是靠更聪明的模型，而是靠更好的上下文设计。

## 相关页面

- [[VibeCoding]]
- [[意图文件]]
- [[Harness-Engineering]]
- [[Spec驱动开发]]
- [[Context-Engineering]]
