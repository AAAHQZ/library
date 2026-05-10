---
tags: [素材摘要]
created: 2026-05-10
updated: 2026-05-10
source_type: 微信公众号
source_path: raw/wechat/2026-05-10-Harness时代，谁在驾驭AI这匹野马？.md
confidence: EXTRACTED
---

# Harness时代，谁在驾驭AI这匹野马？

> 「Harness时代」系列开篇。从 Prompt Engineering 到 Context Engineering 再到 Harness Engineering 的三次进化，Harness 的五模块三层架构，以及驾驶员的终局角色。

## 基本信息

- **来源类型**：微信公众号（「Harness时代」系列之一）
- **原文位置**：raw/wechat/2026-05-10-Harness时代，谁在驾驭AI这匹野马？.md
- **原文链接**：https://mp.weixin.qq.com/s/yN3kHBXNQANppeDHoGTHGg
- **消化日期**：2026-05-10

## 核心观点

1. **三次工程革命**：Prompt Engineering（学说话，2022-2024）→ Context Engineering（学选信息，2025）→ Harness Engineering（造环境，2026）。每次新词出现都意味着上一个认知框架不够用了。

2. **四大关键数据**：同一模型仅换 Harness，编程成功率 42%→78%；LangChain 仅调整 Harness，Terminal Bench 2.0 从 Top30→Top5；Anthropic 实验 $9/20min 无效 vs $200/6h 交付可用游戏；OpenAI 内部 3-5 人 5 个月 100 万行代码零手写。

3. **Harness 五模块**：Tools（原子化可组合）、Knowledge（按需加载）、Observation（可观测性）、Action Interfaces（标准化执行接口）、Permissions（沙箱隔离+审批）。

4. **三层架构**：基础驾驭层（Agent 循环心脏）→ 约束安全层（子Agent+技能库+上下文压缩）→ 生产质量层（后台任务+多Agent协作+工作树隔离）。

5. **文档即环境而非说明书**：AGENTS.md 只做目录（~100行），具体文档按需加载——文档质量直接决定 Agent 产出质量。

6. **终局**：当模型自己长出手脚，Harness 可能被模型吸收，但目的地（"去哪里、为什么去"）永远是人的责任。

## 关键概念

- [[Harness-Engineering]] —— 本文核心论述对象
- [[Context-Engineering]] —— Harness Engineering 的前置演进阶段
- [[Spec驱动开发]] —— 与 Harness 的约束设计理念相通

## 与其他素材的关联

- 与 [[sources/2026-05-09-品味负债：Harness狂奔后的隐形利息|品味负债]] 的关系：同一系列（之一→之三），本篇讲"建系统"，品味负债讲"防腐化"
- 与 [[sources/2026-05-07-告别氛围编程基于Harness治理和SDD的团队级AI研发范式演进与实践|告别氛围编程]] 的关系：从宏观历史脉络到具体团队实践
- 与 [[sources/2026-04-15-深度解析HarnessEngineering|深度解析 Harness Engineering]] 的关系：提供了更多具体工程数据

## 原文精彩摘录

> 模型决定下限，Harness决定上限。

> 这些约束有一个共同的特点：人没有告诉Agent应该怎么做，人只告诉它哪里不能做。从「你应该这样写代码」到「你随便写，但这条线不能碰」。从主动指导变成被动约束。

> 给Agent看的文档，不再是「说明书」，而是「运行环境的一部分」。

> 当机器什么都能干的时候，「干什么」变成了唯一重要的问题。

## 相关页面

- [[Harness-Engineering]]
- [[Context-Engineering]]
- [[Harness]]
- [[品味负债]]
