---
title: 基于 Harness + SDD + 多仓管理模式的 AI 全栈开发实践｜得物技术
source: https://mp.weixin.qq.com/s/ygQGSH5c7GHYDvkqWoQTXQ
author: 得物技术 / 盖伦
date: 2026-05-06
tags: [Harness Engineering, SDD, 多Agent, 全栈开发, AI编程, Cursor, Claude Code]
topics: [AI-Agent工程实践, 研发效能管理]
---

## 摘要

得物技术团队提出 Harness（约束）+ SDD（Spec 驱动开发）+ 多 Agent 协作的全栈 AI 开发方法论，核心思路是给 AI 一个可模仿的参考实现而非让其凭空创造。通过将前后端代码放在同一工作区进行 Codebase Indexing、生成前后端两份 SDD 文档并行开发，配合三阶段验证策略，该团队将原本 2+4 人日的需求压缩至 3 人日，提效 50% 以上。文章还重点警示了 SDD 的隐性功能陷阱——AI 会悄悄复刻参考代码中的逻辑而不写入文档。

## 核心概念

- [[Harness-Engineering]] — 给 AI 约束而非自由发挥，通过提供相似功能代码作为参考，让 AI 模仿已有实现，确保产出代码风格一致、复用率高。
- [[Spec驱动开发]] — 全栈场景下需生成前后端各一份 SDD 文档（proposal.md / spec.md / tasks.md），接口契约严格对齐后再启动代码生成。
- [[Agentic-CI-CD]] — 多 Agent 并行开发后，通过三阶段验证（Mock → 后端构建 → 联调）和前后端分步部署完成交付。
- [[Claude-Managed-Agents]] — Claude Code 的 Subagent 机制可用于并行执行前后端代码生成任务，支持 Subagent 和 Team 两种协作模式。
- [[Skills]] — SDD 指令（如 `openspec-propose`、`openspec-apply-change`）构成 Agent 的技能编排。
- [[Agent-Memory框架]] — SDD 文档作为 Agent 的持久化上下文，防止长链路任务中信息丢失。
- [[Sandbox]] — 后端编译验证（`mvn clean compile`）作为轻量级沙箱验证手段，无需完整启动服务。
- [[MCP]] — 工具集成协议在 Subagent 配置中用于声明可用工具集。

## 正文摘要

### 一、核心理念：Harness 思维

全栈 AI 开发最大的坑是让 AI 从零写代码——生成的"外星代码"风格不统一、复用率低、Review 成本反而更高。Harness 思维的核心是给 AI 一个模仿对象：找代码库中最相似的已有实现，复用现有组件和数据结构，在提示词中明确指定参考文件和接口。例如实现"结束语"功能时参照"场景欢迎语"的前后端完整链路，复用 `greetingExtendInfo` 的数据结构新建 `closingExtendInfo`。约束越精准，代码可用性越高。

### 二、全栈工作区搭建与 Codebase Indexing

前后端代码分布在两个独立仓库时，AI 容易生成字段对不上的代码。将前后端放在同一个工作区有三个好处：Cursor 的 Codebase Indexing 对工作区所有代码建立语义索引，AI 能跨仓库理解代码关系；上下文完整，接口字段自然对齐；SDD 文档集中管理。文章还对比了 Cursor 和 Claude Code 的差异——Cursor 支持语义索引、代码引用便捷、生成速度极快，适合快速迭代；Claude Code 依赖模型自身能力、支持 Subagent 机制，适合长链路复杂任务。

### 三、SDD 驱动的全栈代码生成流程

全栈 SDD 的特殊之处在于需要生成前后端各一份文档，接口契约必须严格对应。文章给出了经过验证的提示词模板，核心要素包括：让 AI `cd` 到对应目录创建 SDD 文件、明确"生成两份"、暗示后续多 Agent 并行、让 AI 先确认细节再生成。前端需求点清单需给出完整的 UI 细节（组件状态、字段约束、交互逻辑），后端需求点清单需提前暴露模糊的设计问题（主键设计、优先级自增逻辑、排序高效更新方案等）让 AI 在写 SDD 前先回答。产出包括 proposal.md、spec.md、tasks.md 等，配合 `openspec-*` 系列指令完成从提案到归档的全流程。

### 四、多 Agent 协作

SDD 文档生成后，前后端代码生成相互独立，天然适合并行。Cursor 中可利用多 Tab 分别负责前端和后端代码生成，互不阻塞。Claude Code 提供 Subagent 机制，支持两种模式：普通 Subagent（只向主 Agent 汇报）和 Team 模式（子 Agent 之间可直接沟通）。实践建议包括：SDD 先行确保接口契约对齐、一个 Agent 一个职责、接口契约作为桥梁、分阶段验证。

### 五、前后端联调：Mock 数据与分阶段验证

推荐三阶段分离验证而非直接联调：阶段一用 Mock 数据验证前端 UI 逻辑（Mock 数据需与后端 SDD 字段完全一致，覆盖边界场景）；阶段二后端 `mvn clean compile` 编译验证；阶段三前后端联调，前端连接测试后端接口进行端到端验证。这样前后端问题可提前分别发现和修复，避免联调阶段才暴露。

### 六、警惕 SDD 陷阱

SDD 描述的是"技术上怎么实现"，而非"业务上所有的行为"。AI 在模仿参考代码时会自动复刻隐性功能——变量/表单清除、数据格式转换、默认值补齐等逻辑可能被悄悄实现而未写入文档。这些隐性功能可能正是需要的，也可能不符合当前需求，问题在于开发者不知道它们的存在。建议测试在 SDD Review、代码 Review 和联调测试三个阶段分别关注接口契约完整性、隐性功能识别和边界场景覆盖。

## 相关页面
- [[Harness-Engineering]]
- [[Spec驱动开发]]
- [[Agentic-CI-CD]]
- [[Claude-Managed-Agents]]
- [[Skills]]
- [[Agent-Memory框架]]
- [[Sandbox]]
- [[MCP]]
