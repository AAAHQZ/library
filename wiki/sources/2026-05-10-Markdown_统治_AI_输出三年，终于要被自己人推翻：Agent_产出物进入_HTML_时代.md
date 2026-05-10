---
tags: [素材摘要]
created: 2026-05-10
updated: 2026-05-10
source_type: 微信公众号
source_path: raw/wechat/2026-05-10-Markdown_统治_AI_输出三年，终于要被自己人推翻：Agent_产出物进入_HTML_时代.md
confidence: EXTRACTED
---

# Markdown 统治 AI 输出三年，终于要被自己人推翻：Agent 产出物进入 HTML 时代

> Anthropic Claude Code 工程师 Thariq Shihipar 发文主张 agent 对人产出物应从 Markdown 切到 HTML。背后是 agent 产品形态的转向：从对话框里的写作助手，转向能交付任意计算性载体的协作者。

## 基本信息

- **来源类型**：微信公众号
- **原文位置**：raw/wechat/2026-05-10-Markdown_统治_AI_输出三年，终于要被自己人推翻：Agent_产出物进入_HTML_时代.md
- **原文链接**：https://mp.weixin.qq.com/s/ABob0iOml6HiWbyTEcyLtg
- **消化日期**：2026-05-10

## 核心观点

1. **Markdown 成为 agent 默认输出的四条原因**：训练分布广（GitHub/StackOverflow 大量 MD）、双向可读（纯文本+渲染）、早期 agent 产出线性（顺序段落）、文件系统友好（可 cat、diff、grep）。

2. **Markdown 四大物理限制**：①一维顺序流压扁空间信息（diff 左右栏、多方案并排对比做不到）；②无原生颜色图形（Agent 用 Unicode 半角块字符画色板——"一个能写 1M token 的模型在用算盘做加法"）；③不能直接分发（Markdown 不是浏览器原生格式，HTML 一个链接即可打开）；④单向消费（Markdown 不能反向操作，HTML 加 JS 可做临时交互工具）。

3. **格式分层方法论**：Web→LLM 用 Markdown（token 效率）、LLM→Human 用 HTML（视觉密度+可交互）、LLM→LLM 用 Markdown/JSON（可再塞回 prompt）、Human→LLM 用 Markdown/纯文本。"all Markdown" 是把四种链路混在一起用同一个格式凑合。

4. **三个前提条件成熟**：模型生成 HTML 能力跨过实用线（Sonnet 3.5/4）；Artifacts/Canvas 把 HTML 渲染从 hack 带入产品形态；Token 价格和上下文窗口让 HTML 的"贵 2-4 倍"不再致命。

5. **Claude Code 团队的群体偏移证据**：Thariq 自己的文章配图是扫本地仓库统计 HTML 产出物；Simon Willison 当晚实验用 copy.fail PoC 生成交互式 HTML 报告并公开改口；Anthropic 发布 frontend-design plugin 和 `/insights` 内置命令。

6. **Markdown 仍然赢的场景**：Agent 间通信、配置/指令文件（CLAUDE.md/SKILL.md）、版本控制（long-form 文档）——判断标准：产出物接下来会被喂回模型，还是给人做一次性决策。

## 关键概念

- [[产出物格式分层]] —— 按数据流向选格式的方法论
- [[Harness-Engineering]] —— 文件系统异质化、产出物计算性抬升给 harness 的新需求
- [[VibeCoding]] —— 产出物格式选择的背景上下文

## 与其他素材的关联

- 与 [[sources/2026-05-10-Vibe_Coding时代，每个项目都需要意图文件|Vibe Coding 时代，每个项目都需要意图文件]] 的关系：互补——上篇讲意图文件（Markdown）的标准化，这篇讲对人输出（HTML）的升级，共同指向 Agent 产出物的格式分层趋势
- 与 [[sources/2026-05-06-三大Agent平台收敛到Markdown方案|三大 Agent 平台收敛到 Markdown]] 的关系：正好是反向趋势——Markdown 在 Agent 内部通信层收敛，HTML 在 Agent→Human 输出层崛起
- 与 [[sources/2026-05-07-自然语言不适合编程Dijkstra半个世纪前的判断今天依然成立|Dijkstra 形式化思维]] 的关系：HTML 本质上是对 Markdown 的形式化升级，符合"收窄界面宽度"的大趋势

## 原文精彩摘录

> 之前的"all Markdown"其实是把四种链路混在一起用同一个格式凑合。系统成熟之后，每条链路开始挑自己最优的格式。

> 一个简单的判断方法：这份产出物接下来会被喂回模型，还是给人做一次性决策？前者继续 Markdown，后者考虑 HTML。

> 把这次 Markdown 到 HTML 的迁移放在更长的时间尺度上看，它不是格式层的优化，而是 agent 产品形态的一次转弯：从对话框里的写作助手，转向能交付任意计算性载体的协作者。

> 最深远的变化是 agent 输出物的计算性在抬升。一份 plan 不再是"读完决定"，而是"和它互动得到决定"。

## 相关页面

- [[VibeCoding]]
- [[Harness-Engineering]]
- [[意图文件]]
- [[Spec驱动开发]]
