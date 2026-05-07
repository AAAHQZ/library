---
title: "基于Harness+SDD+多仓管理模式的AI全栈开发实践"
slug: dewu-ai-fullstack-harness-sdd
tags: [AI全栈开发, Harness, SDD, 多仓管理, Cursor, Claude-Code, 得物]
created: 2026-05-06
updated: 2026-05-06
source: ../raw/wechat/2026-05-06-基于_Harness_+_SDD_+_多仓管理模式的_AI_全栈开发实践｜得物技术.md
references:
  - https://mp.weixin.qq.com/s/ygQGSH5c7GHYDvkqWoQTXQ
---

# 基于 Harness + SDD + 多仓管理模式的 AI 全栈开发实践

> 得物技术团队，分享基于 Harness 思维 + SDD（Spec-Driven Development）+ 多仓工作区的 AI 全栈开发方法论。核心价值：采纳率提升、耗时降低 50%+、调试不依赖阻塞、AI 全栈学习成本骤降。

## 核心理念：Harness 思维

**问题**：让 AI 从零写代码，生成的是"外星代码"——风格不一致、复用率低、Code Review 成本高

**Harness 思维本质**：给 AI 一个已有的实现作为参照，让它照着复刻，而非凭空创造

**Harness 四原则**：
| 原则 | 说明 |
|------|------|
| 找相似实现 | 在代码库中找到功能最相似的已有实现作为参照 |
| 复用优先 | 能复用的组件、接口封装、数据结构直接复用 |
| 模仿着复制 | 哪怕"抄一份改一改"，也比用新方式写好 |
| 约束生成范围 | 在提示词中明确指定参考文件、参考接口 |

** Harness 提示词示例**：
```
推荐（Harness 约束）：请参照现有"场景欢迎语"功能（后端接口 /api/v1/feature/list，前端入口 FeatureTable/index.tsx:53-58）实现"结束语"功能。数据结构、分层方式、命名风格都保持一致。新增场景 code：categoryCode = "SCENARIO_CLOSING"
```

## 全栈工作区搭建

**为什么需要多仓工作区**：
- AI 生成后端接口时能看前端调用方式，生成前端代码时能看后端返回结构
- Codebase Indexing 覆盖两侧代码，接口字段自然对齐
- SDD 文档集中管理，便于接口契约对齐

**Cursor vs Claude Code 对比**：
| 功能 | Cursor | Claude Code |
|------|--------|-------------|
| 语义索引 | grep + 代码段语义相似度 | 仅 grep |
| 代码生成速度 | 极速（1-3 分钟） | 中速（3-30 分钟） |
| 多 Agent | 默认开启（多 Tab 并行） | 需手动注册 Subagent |
| 费率模型 | 失败任务不收费 | 失败任务耗时且耗 token |
| 会话恢复 | 仅当前项目 | 全局会话记录 |

## SDD 驱动的全栈代码生成

**全栈 SDD 特殊之处**：生成两份 SDD 文档（前端 + 后端），接口契约必须严格对齐

**SDD 文档产出**：
- 前端：proposal.md（需求提案）+ spec.md（技术规格）+ tasks.md（任务拆分）
- 后端：proposal.md + spec.md + design.md（详细设计）+ tasks.md

**全栈 SDD 提示词模板**：
```
这是一个前后端全栈开发工作区，需要你设计技术接口方案，同时开发前后端项目；
首先你需要 cd 到对应前后端应用目录中，创建 sdd 文件；
需要生成两份 sdd 文档，之后启动两个 agent 分别实现；
在生成之前，如果需要确认某些细节，你应当先确认后生成 sdd 文档。
```

## 多 Agent 协作

**为什么需要多 Agent**：前端 SDD 和后端 SDD 生成完毕后，前后端代码生成工作相互独立，天然适合并行执行

**Cursor 多 Agent**：Tab 1 负责前端，Tab 2 负责后端，两个 Agent 同时运行互不阻塞

**Claude Code Subagent 配置**：
```json
{
  "description": "前端代码生成专家",
  "tools": ["Read", "Edit", "Write", "Bash"],
  "permissionMode": "bypass",
  "model": "sonnet",
  "skills": ["前端编码规范"]
}
```

**全栈开发场景 Subagent 分配**：
- Subagent 1：读取前端 SDD，生成前端代码
- Subagent 2：读取后端 SDD，生成后端代码
- Subagent 3（可选）：生成接口 Mock 数据

## 三阶段验证策略

| 阶段 | 内容 | 目标 |
|------|------|------|
| 阶段 1 | 前端代码 + Mock 数据 → 本地跑通页面交互 | 验证 UI 逻辑 |
| 阶段 2 | 后端代码 → mvn clean compile → 部署测试环境 | 验证代码可编译 |
| 阶段 3 | 前端连接测试后端接口 → 端到端验证 | 验证完整链路 |

**Mock 数据要点**：字段名/类型必须与后端 SDD 完全一致；参考已有接口真实返回数据；覆盖边界场景

## SDD 陷阱：隐性功能

**问题**：AI 模仿参考代码生成新代码时，会自动复刻很多隐性功能（未写进 SDD 但已实现）

**隐性功能示例**：
- 前端：关闭弹窗时清空表单字段（`form.resetFields()`）
- 后端：永久有效时自动清除开始/结束日期
- 后端：优先级自动递增逻辑

**测试介入建议**：
- SDD Review 阶段：关注接口契约是否完整
- Code Review 阶段：对照 SDD 文档和实际代码差异，寻找隐性功能
- 联调测试阶段：不要只测 happy path，覆盖边界场景和隐性行为

## 实践效益

- **采纳率提升**：工作区模式把项目需求上下文放一起，Cursor 索引能力进一步提高采纳率和功能完整性
- **耗时降低 50%+**：前后端 2+4 人日需求压缩至 3 人日（含环境准备、踩坑、联调自测）
- **调试不依赖阻塞**：前端可 mock 数据自测；后端支持远程调试
- **AI 全栈学习成本骤降**：只需掌握入门级前后端知识即可介入简单全栈需求

## 相关页面

- [[Harness-Engineering]]
- [[Spec驱动开发]]
- [[Cursor]]
