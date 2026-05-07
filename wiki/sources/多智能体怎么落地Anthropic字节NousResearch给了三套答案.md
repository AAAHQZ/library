---
source: article
title: 多智能体怎么落地Anthropic字节NousResearch给了三套答案
date: 2026-04-22
author: 
published: 
references: []
summary: > 原文链接：https://mp.weixin.qq.com/s/hzfVCE18PzpFs54-bACA_g
---

# 多智能体怎么落地Anthropic字节NousResearch给了三套答案

> 原文链接：https://mp.weixin.qq.com/s/hzfVCE18PzpFs54-bACA_g

> 公众号：MingBuilds

# 多智能体怎么落地？Anthropic、字节、NousResearch 给了三套答案

多智能体今年在技术圈几乎成了默认话题。随便打开一个 AI 相关的讨论，总有人在聊多 Agent 协同、上下文管理、自动评估这些概念。

但实际动手做过的人都知道，从"跑通 demo"到"稳定产出"之间有一段不小的距离。两个 Agent 可能会互相附和说"做得不错"，一个长任务跑到后面可能就开始敷衍收尾。问题不在模型本身，在于架构没考虑到这些行为模式。

2026 年 3 月底， Anthropic 的工程师 Prithvi Rajasekaran 发了一篇长文，详细拆解了他们是怎么把 Claude 从"能写个 demo"推到"能连续 6 小时自主开发完整应用"的。几乎同一时期，字节的 DeerFlow 在 GitHub 上冲到了趋势第一，而 NousResearch 的 Hermes Agent 则走了一条完全不同的路线。

三个项目，三种架构思路。不是谁对谁错，而是——**你在什么阶段，就该用什么方法**。

今天不聊概念，直接看这三个项目的底层设计，拆解出多智能体实践中真正 load-bearing 的原则。
## 为什么你的多 Agent 系统会跑飞

Anthropic 的文章里明确指出了两个失败模式，几乎总结了行业 90% 的问题：

**第一，上下文崩溃**。

模型在处理长任务时，随着对话窗口越来越满，开始出现"上下文焦虑"——它判断自己快到极限了，于是倾向于草草收尾。就像 Chrome 标签页开多了之后你会想赶紧关掉浏览器一样。更严重的是，有些模型在上下文接近填满时，判断力和输出质量会明显下降。

Anthropic 的解法叫&nbsp;**Context Reset**——不是原地压缩（ Compaction ），而是彻底清空对话窗口，用一个结构化的 handoff artifact 把之前的状态、进度和下一步任务打包，交给一个全新的 agent 。相当于让一个连续工作 8 小时的程序员去休息，换一个精力充沛的同事接着做。

不过 Opus 4.6 之后这个行为明显改善了不少，新模型的上下文管理能力增强， Anthropic 后来直接移除了这个机制。

**第二，自我评价失效**。

让 Agent 评价自己的产出，它基本会给出正面结论——而且相当自信。这不是模型在撒谎，而是它的训练方式决定了它天然倾向于认可 LLM 生成的内容，包括自己生成的。

Anthropic 的解法是&nbsp;**Generator-Evaluator 分离**，灵感来自 GAN （生成对抗网络）。生成器负责干活，评价器负责找茬。关键一点——评价器不是拿来就好的， Anthropic 的工程师花了好几轮调优才把它从"和稀泥 QA"变成"尖锐质检员"。

这两个问题，是多智能体架构设计要解决的核心痛点。
## 三条路线：同一个问题，三种答法
### Anthropic Harness —— 生成-评价质量飞轮

Anthropic 的方案是一个**三 Agent 系统**，整体架构如下：

- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 

```
用户输入（一句话需求）&nbsp; │&nbsp; ▼┌──────────────────┐│ &nbsp; &nbsp;&nbsp;Planner&nbsp; &nbsp; &nbsp; &nbsp;││ &nbsp;需求 → 规格文档 &nbsp; ││（deliverables） &nbsp; │└──────┬───────────┘&nbsp; &nbsp; &nbsp; &nbsp;│ 规格文档拆成&nbsp;Sprint&nbsp;列表&nbsp; &nbsp; &nbsp; &nbsp;▼┌──────────────┐ &nbsp; &nbsp; ┌──────────────┐│ &nbsp;Generator&nbsp; &nbsp;│────▶│ &nbsp;Evaluator&nbsp; &nbsp;││ &nbsp;写代码/交付 &nbsp;│ &nbsp; &nbsp; │&nbsp;Playwright验收│└──────┬───────┘ &nbsp; &nbsp; └──────┬───────┘&nbsp; &nbsp; &nbsp; &nbsp;│ &nbsp;◀ 未通过 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; │ 通过&nbsp; &nbsp; &nbsp; &nbsp;│ &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;│&nbsp; &nbsp; &nbsp; &nbsp;└────────────────────┘&nbsp; &nbsp; &nbsp; &nbsp; 循环直到&nbsp;Sprint&nbsp;Contract&nbsp;达成
```
三个角色的分工非常明确：
•**Planner**：把用户的一句话需求扩展成完整产品规格文档。它只定义 deliverables ，不规定 implementation details——"做什么"和"怎么验证"它管，"怎么写代码"它不管。•**Generator**：按规格文档一个一个 Sprint 地写。每个 Sprint 开始前， Generator 和 Evaluator 先协商一个 Sprint Contract ，约定"什么算完成"。•**Evaluator**——这是真正的杀手锏。它用 Playwright MCP 直接在运行中的应用里逐项验证 Sprint Contract 里的测试条件，像 QA 工程师一样。
这个循环最关键的设计是&nbsp;**Sprint Contract**——它不是一句话的"把这个功能做了"，而是一个结构化的验收清单：

- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 

```
Sprint: 关卡编辑器─────────────────────必须实现：✅ 网格化画布（至少 16x16）✅ 拖拽放置精灵✅ 缩放/平移画布✅ 保存/加载关卡（JSON 格式）验收标准：1.&nbsp;打开页面即显示 16x16 网格2.&nbsp;从侧边栏拖拽精灵到网格任意位置3.&nbsp;鼠标滚轮缩放不影响已放置精灵4.&nbsp;点击保存按钮下载 json 文件，&nbsp; &nbsp;重新加载后精灵位置完全一致不计入本次范围：❌ 精灵动画预览❌ 地形碰撞编辑❌ 多人协作编辑
```
有了这样一份契约， Generator 知道自己要做什么， Evaluator 知道怎么验收——双方不会扯皮。

效果对比——同样是"写一个复古游戏制作器"的需求：
•单
...
