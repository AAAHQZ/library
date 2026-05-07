---
source: article
title: Anthropic工程博客：25词system prompt如何摧毁Claude Code
date: 2026-04-23
author: 
published: 
references: []
summary: "原文链接：https://mp.weixin.qq.com/s/-U14OuMOUUufO1uy6_iEZA"
---

# Anthropic工程博客：25词system prompt如何摧毁Claude Code

> 原文链接：https://mp.weixin.qq.com/s/-U14OuMOUUufO1uy6_iEZA

# Anthropic 工程博客自爆：一条 25 词的 system prompt 限制如何摧毁 Claude Code

![img](Anthropic工程博客自爆一条25词的systemprompt限制如何摧毁ClaudeCode_image_001.jpg)

> **来源：** Anthropic Engineering Blog[1] · 2026 年 4 月 23 日

**TL;DR** — 2026 年 3 月至 4 月，大量 Claude Code 用户报告模型"变笨了"。AMD AI 高级总监 Stella Laurenzo 对 6,852 个会话的量化分析显示，thinking 深度暴跌 67%，盲编辑率从 6.2% 飙升到 33.7%。Anthropic 最终追踪到三个独立的产品层变更：将默认推理努力从 `high` 降到 `medium`（3 月 4 日）；一个缓存优化 bug 导致 thinking 历史在整个会话中被持续清除而非仅清除一次（3 月 26 日）；以及一条限制输出字数的 system prompt 指令（4 月 16 日）。三个问题分别影响不同用户群体、在不同时间窗口生效，叠加在一起看起来像是模型本身的全面退化。所有修复于 4 月 20 日的 v2.1.116 完成，Anthropic 同时重置了所有订阅用户的使用限额。这是 AI 编码工具领域迄今为止最有教育意义的一次产品事故——它证明了"模型没变，harness 变了"可以制造出与模型退化完全相同的用户体验。
## 一、事件全景

### 时间线
时间事件影响范围2 月 5 日Opus 4.6 发布，默认 effort 为 `high`Claude Code 全量用户3 月 4 日默认 effort 从 `high` 改为 `medium`Sonnet 4.6 + Opus 4.6 用户3 月 8 日Laurenzo 数据中 redacted thinking 占比超过 50%标志性转折点3 月 26 日上线缓存优化，意图是清除空闲超过 1h 的会话 thinking空闲过的 Sonnet 4.6 + Opus 4.6 会话4 月 2 日Stella Laurenzo 在 GitHub 发布 6,852 会话量化分析引发广泛关注，GitHub Issue #42796 获 1,901 upvotes4 月 7 日回滚 effort 默认值（Opus 4.7 → `xhigh`，其他 → `high`）修复第一个问题4 月 10 日修复缓存 bug（v2.1.101）修复第二个问题4 月 16 日上线 Opus 4.7 + 新 system prompt 限制输出字数Sonnet 4.6 + Opus 4.6 + Opus 4.74 月 20 日回滚 system prompt 变更（v2.1.116）修复第三个问题4 月 23 日发布公开 postmortem，重置所有订阅用户限额—

三个问题的时间窗口互相交错——第一个从 3 月 4 日持续到 4 月 7 日，第二个从 3 月 26 日持续到 4 月 10 日，第三个从 4 月 16 日持续到 4 月 20 日。对于一个在 3 月 26 日到 4 月 7 日之间使用 Claude Code 的用户来说，前两个问题可能同时生效：effort 被降档 + thinking 被反复清除。这解释了为什么社区报告呈现出"全面、不一致的退化"——不同用户在不同时间点命中不同的 bug 组合。
### 社区反应的烈度

这次事件引爆了 AI 编码工具用户群体中少见的集体愤怒。Laurenzo 在 GitHub Issue #42796 中的分析覆盖了 234,760 次工具调用和 17,871 个 thinking block，得出的结论是"Claude cannot be trusted to perform complex engineering tasks"，并宣布 AMD 团队正在切换 AI 编码供应商。这条 Issue 获得 1,901 个 upvote、128+ 条评论，随后被 The Register、VentureBeat、Mint 等主流科技媒体转载。

Hacker News 上的讨论更尖锐。最高票评论质疑的不是 bug 本身，而是透明度："with zero transparency, it's hard to trust Anthropic not to continue to silently optimize for cost"。$200/月的 Pro 订阅用户面对"随时可能被静默降级"的可能性，信任成本比技术成本更高。
## 二、三个问题的根因分析

### 问题一：默认推理努力从 `high` 降到 `medium`

**表面原因很直接：** 部分用户在 `high` effort 下遇到了极端长尾延迟，UI 看起来像是卡死了。Anthropic 团队看到内部 eval 显示 `medium` 在"大多数任务"上只损失了"slightly lower intelligence"，但显著降低了延迟，于是做出了切换。

**但为什么这个决定是错的？** 这里有一个产品经理思维和工程师思维的经典冲突。从产品指标看，`medium` 似乎是帕累托改进——大多数用户的大多数任务变快了，少数用户的少数任务变慢了一点。但 Claude Code 的用户群体不是"大多数用户"——他们是付费 $20–$200/月的专业开发者，选择 Claude Code 而不是免费替代品的核心原因就是"更聪明"。对这个群体来说，5% 的智力损失比 50% 的延迟增加更不可接受。

更深层的问题在于 test-time compute 曲线的非线性。Anthropic 自己在 postmortem 中画出了这条曲线：effort 和输出质量不是线性关系，在 `medium` 到 `high` 的区间有一个陡峭的上升段。这意味着从 `high` 降到 `medium` 的智力损失，不是 eval 上看到的那个平均数字能概括的——在简单任务上差异微乎其微，在复杂任务上差异会被放大。而 Claude Code 的核心用例恰恰是复杂任务。
![img](Anthropic工程博客自爆一条25词的systemprompt限制如何摧毁ClaudeCode_image_002.jpg)

Anthropic 做了一些设计迭代来缓解——启动提示、内联 effort 选择器、重新引入 ultrathink——但大多数用户保持了 `medium` 默认值不变。这暴露了一个用户行为事实：默认值的力量远大于"你可以手动调"的承诺。当产品把默认值设为 `medium` 时，90%+ 的用户不会主动切换，即使他们感觉到了质量下降，第一反应也是"模型变笨了"而不是"我该调个参数"。

**根本教训：** 对于以智力为核心卖点的产品，默认值的选择等同于产品定位的选择。选择
...
