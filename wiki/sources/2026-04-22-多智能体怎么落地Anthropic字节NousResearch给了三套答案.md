---
tags: [多智能体, Agent架构, Anthropic, 字节, NousResearch]
created: 2026-04-22
updated: 2026-04-22
sources: [../raw/多智能体怎么落地？Anthropic、字节、NousResearch 给了三套答案.md]
---

# 多智能体怎么落地：Anthropic、字节、NousResearch 给了三套答案

> 三个项目三种思路：Harness（工程构建）、DeerFlow（研究探索）、Hermes（个人助手）

## 多智能体的两大核心痛点

**上下文崩溃**：模型处理长任务时，上下文窗口越来越满，出现"上下文焦虑"，倾向于草草收尾，输出质量下降。

**自我评价失效**：让 Agent 评价自己的产出，基本都会给出正面结论——不是因为撒谎，而是训练方式决定了它天然倾向于认可 LLM 生成的内容。

## 三套方案对比

| 维度 | Anthropic Harness | DeerFlow | Hermes Agent |
|------|-------------------|----------|--------------|
| 定位 | 工程构建 | 研究探索 | 个人助手（[[Hermes-Agent]]） |
| 架构 | 三 Agent 循环 | 多 Agent 流水线 | 单 Agent + 记忆 |
| 质量保障 | Evaluator 循环验收 | 沙箱隔离执行 | 无（靠模型本身） |
| 上下文管理 | Context Reset | LangGraph 状态图 | 持久记忆文件 |
| 成本 | 高（$100-200/次） | 中低 | 低 |
| 上手难度 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐ |

## Anthropic Harness：三 Agent 质量飞轮

- **Planner**：把用户需求扩展成产品规格文档
- **Generator**：按规格一个 Sprint 一个 Sprint 地写代码
- **Evaluator**：用 Playwright MCP 直接在运行中的应用里逐项验收

**核心设计**：Sprint Contract——结构化的验收清单，约定"什么算完成"，双方不会扯皮。

效果对比（"写一个复古游戏制作器"）：
- 单 Agent：20分钟，$9，能搭界面但核心功能跑不起来
- 完整 Harness：6小时，$200，做出了16个功能，游戏真的能玩

**核心洞察**：Evaluator 的价值取决于任务离模型能力边界有多近。任务在边界处，Evaluator 是决定性杠杆。

## ByteDance DeerFlow：研究探索流

基于 LangGraph 构建，核心是**研究型超级 Agent**：
- Researcher：拆解查询，生成搜索计划
- Searcher：多引擎搜索
- Web Crawler：抓取/清洗
- Coder Agent：执行代码/数据分析
- Reporter：汇总最终报告

47K stars 说明开源社区的选择。

## NousResearch Hermes：个人 Agent 极简路线

slogan 是"The agent that grows with you"——不是企业级多智能体系统，而是**个人智能 Agent 框架**。

核心能力：
- **持久记忆**：对话经验持续积累
- **多 Profile 隔离**：同一机器上跑多个独立实例
- **多平台 Gateway**：Telegram、Discord、Slack、WhatsApp

## 提炼的5个设计原则

1. **做的人跟评价的人必须分开**：不要让 Agent 自己检查自己的工作。生成器-评价器双 Agent 循环是最小可跑实现。

2. **上下文管理比架构花哨更重要**：通过结构化 artifact 传递状态，不要靠"大概说一声"。

3. **契约化协作是 Agent 间通信的唯一可靠方式**：Agent 之间不靠自然语言模糊交流，靠结构化的、双方确认的约定。

4. **复杂度必须与模型能力匹配**：最简单的架构 + 最强的模型 = 最优解。别一上来就搭三 Agent 系统。

5. **简单 Agent 用持久记忆，复杂任务用多 Agent 编排**：先搭一个带持久记忆的单 Agent，发现不稳定时引入评价器，任务更复杂时再加 Planner。

关于 [[AI-Agent]] 的核心概念和范式转变，请参阅实体页面。

## 相关页面

- [[Anthropic的Harness哲学把Agent当牲口而非宠物|sources/2026-05-05-Anthropic的Harness哲学把Agent当牲口而非宠物]]
- [[从第一性原理思考AgenticEngineering|sources/2026-05-05-从第一性原理思考AgenticEngineering]]
- [[什么是pi下一代Agent架构|sources/2026-05-05-什么是pi下一代Agent架构]]
- [[给10万Star的Hermes装个记忆外挂|sources/2026-05-05-给10万Star的Hermes装个记忆外挂]]
