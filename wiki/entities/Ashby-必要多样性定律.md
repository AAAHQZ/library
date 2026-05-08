---
title: "Ashby 必要多样性定律"
date: 2026-05-07
tags:
  - 控制论
  - 系统工程
  - 架构
type: entity
confidence: EXTRACTED
source: GUI型DevOps平台的天花板，Ashby在1956年就画好了
---

# Ashby 必要多样性定律（Law of Requisite Variety）

> **Only variety can destroy variety.** 控制器的多样性必须不小于扰动的多样性。1956年，W. Ross Ashby，《控制论导论》第十一章。

## 定律内容

Ashby 定义的**多样性（Variety）**：一个系统能呈现的不同状态数。

- 一个开关：2 种状态
- 8 位寄存器：256 种状态
- 100 个微服务各有 3 种状态（healthy/degraded/down）：3¹⁰⁰，天文数字

**核心定律**：要让被控系统稳定在期望状态，控制器最少需要的不小于扰动的多样性。

**推论**：一切"以少御多"的尝试都注定失败。要么提升控制器的多样性，要么降低被控对象的多样性，没有第三条路。

## 在软件工程中的应用

### GUI 型 DevOps 平台的天花板

GUI 平台在 UI 里预先定义核心概念，产品团队替用户做出"哪些可变、哪些不可变"的决定。当工程组织的真实多样性超过产品团队的编码容量时，结构性失败不可避免。

### IaC 为什么能爬出去

Terraform/Pulumi 把多样性外包出去：Cloud API 多样性外包给 provider，组织多样性外包给 module，部署模式多样性外包给 user code。多样性住在 user-space，核心代码量不爆炸。

### 工程组织的多样性维度

受控的工程组织的多样性远超出直觉。至少 6 个维度，每个维度 5 种取值：

- **服务命名约定**：team-domain-service / service.team.domain / domain/team/svc / 纯 UUID / 没约定
- **环境模型**：dev/staging/prod / 按 region 切 / 按账号切 / 按 tenant 切 / 混合
- **部署单元**：service / application / deployment group / release train / bounded context
- **变更类别**：code change / config change / secret rotation / infra change / feature flag flip / shadow traffic
- **依赖关系来源**：代码静态分析 / 运行时 trace / CMDB 手维护 / IaC 推断 / 混合
- **审批与合规**：单审 / 双审 / SOX / HIPAA / PCI / 按金额 / 按 blast radius

6 个维度 5 种取值 → 15,625 种形状。真实世界远不止 6 个维度。

### GUI 的语义困境

GUI 型平台覆盖这套多样性的方式是在产品里做模型、做开关、做插件机制、做自定义字段。但只要这些东西落进产品代码——落进 if-else、React 组件、schema 定义——控制器的多样性就被产品团队的工程容量锁死了。

同样的症状，IaC 那边是早期阵痛（发版赶不上需求、if customer == X），GUI 这边变成慢性病：

- 用了三年的客户，UI 上一堆历史遗留的"模式开关"没人敢删
- 新客户发现平台的概念和自己组织对不上，但又改不了平台
- if customer == X 永远只能由产品团队加，用户没法在自己那一侧补

**根本原因**：GUI 这个介质天生没法外包语义——你没法给用户一张表单、让他在表单里定义新的表单。语义的注入面太窄。

### 解决方案：语义层架构

出路是把语义的来源换出去。核心概念（什么是"服务"、"环境"、"变更"）不该写死在产品里，应该作为开放的 schema，让用户在 IaC 代码、配置、注解中去定义。审批、依赖、blast radius 应该基于用户的语义去推理。

GUI 的角色变了：从"告诉用户系统长什么样"变成"把基于用户语义算出来的结论展示给用户"。

**核心隐喻**：从菜谱（每道菜做法写死）→ 语法书（定义语法，让用户自由表达）。菜谱再厚也写不完世界上所有的菜。语法定义清楚，能表达的句子是无限的。

### 下一代 DevOps 平台

下一代 DevOps 平台的核心不在 UI 里，在它背后的**语义层**。控制器想活下来，先得承认自己不可能比被控系统更复杂。

### 与熵控制的关系

[[Harness-Engineering]] 的熵增控制本质上是在对抗多样性失控——系统自发趋向混乱，控制论提供两条路：提升控制器多样性，或降低被控对象多样性。Harness Engineering 选择前者。

### Vibe Coding 的困境

[[VibeCoding]] 也是"以少御多"尝试——自然语言描述复杂系统，Ashby 定律预言了瓶颈。

## 原文出处

- W. Ross Ashby, *An Introduction to Cybernetics*, 1956, Chapter 11
- Ashby 本职是英国精神科医生，研究大脑如何维持稳定

## 相关页面

- [[控制论]]
- [[W.Ross-Ashby]]
- [[Harness-Engineering]]
- [[VibeCoding]]
- [[Platform-Engineering]]
- [[Terraform]]
- [[Spec驱动开发]]
