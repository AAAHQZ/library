---
title: "Platform Engineering"
date: 2026-05-07
tags:
  - 平台工程
  - DevOps
  - 架构
type: entity
confidence: EXTRACTED
source: GUI型DevOps平台的天花板，Ashby在1956年就画好了
---

# Platform Engineering（平台工程）

> 构建内部开发者平台（Internal Developer Platform）的工程实践，使研发团队能够自助完成从代码到生产的全流程。

## 核心观点

GUI 型 DevOps 平台是 Platform Engineering 的典型形态——打开浏览器能看到「项目 / 环境 / 服务 / 部署 / 审批流」的图形化产品。商业产品（Ashby 文章分析对象）和企业内部平台工程团队搭建的 portal 都属于此类。

### GUI 型平台的控制论天花板

根据 [[Ashby-必要多样性定律]]，GUI 平台的工程容量（控制器多样性）被产品团队的编码能力锁死，而被控系统（工程组织 + 基础设施）是开放的。当后者多样性超过前者时，结构性失败不可避免。

**三条路**：
1. **提升控制器多样性**：做更多 UI 开关、插件机制、自定义字段 → 失败，产品工程容量跟不上
2. **降低被控对象多样性**：标准化工程组织形式（如 [[Terraform]] 的 module 封装）→ 可行
3. **换语义层**：把"服务/环境/变更"定义从产品代码移到用户 IaC/配置 → 新一代平台方向

### GUI 型平台的慢性病

- 老客户历史遗留"模式开关"没人敢删
- 新客户发现平台概念与组织对不上
- `if customer == X` 只能由产品团队加

### 新一代平台方向

核心在语义层而非 UI 层。GUI 角色从"告诉用户系统长什么样"退化为"把推理结论展示给用户"。

## 与 [[Harness-Engineering]] 的关系

Harness Engineering 是 Platform Engineering 在 AI Agent 时代的演进——平台的核心从 UI 变成了 harness（控制循环），从"人机界面"变成了"人-Agent-工具"协同系统。

## 相关页面

- [[Ashby-必要多样性定律]]
- [[Terraform]]
- [[Harness-Engineering]]
- [[VibeCoding]]
