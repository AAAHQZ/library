---
tags: [AI-Agent, 知识管理, 经验沉淀, Harness-Engineering]
created: 2026-05-21
updated: 2026-05-21
sources: ["2026-05-21-QQ音乐Harness_Engineering实践.md"]
---

# Self-Refinement-Loop

> 让AI从错误中沉淀经验，使团队的每一个"纠正"都成为团队资产的闭环机制

## 简介

Self-Refinement-Loop（自我改进闭环）是Harness Engineering中解决"AI错误修完就丢，下次继续犯"这一问题的核心机制。

LLM没有跨会话记忆，但团队的每一个"纠正"都是一次宝贵的信号。

## 闭环流程

```
①用户纠正AI某个错误
    ↓
②AI识别：这是"模式性教训"还是"一次性diff"？
    ↓ 模式性
③AI主动提议沉淀层级
   ├─ 团队级 → context/team/
   ├─ 框架工程级 → harness-framework/
   └─ 服务级 → context/project/{...}
    ↓ 用户确认
④生成experience文档/更新Skill/修订规范
    ↓
⑤下次同类场景，AI主动引用
   （新会话/新模型/新人也受益）
```

## 核心原则

> "错误不再'走一次算一次'，而是成为团队资产"

## 具体产物

- `context/project/music_commercial_go_proj/campaign/DEPENDENCY_ANALYSIS.md` - 子域依赖影响分析的真实记录
- `context/project/{project}/{module}/experience/*.md` - 踩坑经验（分页必须有上限、goroutine泄漏、🔒字段约束等）
- `context/project/{project}/sop/*.md` - 从经验提炼出的标准操作规程

## 触发机制

AGENTS.md的认知模式第5条：**当遇到新模式或教训时，主动提议更新到context/**

配合专门的`self-refinement` Skill执行。

## 设计意义

这种"知识驻留在仓库里"的设计，让新人、新模型、新会话都能复用团队的集体经验，而不是从零开始理解业务约束。

## 相关页面

- [[Multi-Agent-Governance]] - 多Agent治理
- [[Five-Stage-Gate-Workflow]] - 五阶段四门禁
- [[Three-Warehouse-Linkage]] - 三仓联动
- [[Service-Matrix]] - 服务矩阵
