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

### 与熵控制的关系

[[Harness-Engineering]] 的熵增控制本质上是在对抗多样性失控——系统自发趋向混乱，控制论提供两条路：提升控制器多样性，或降低被控对象多样性。Harness Engineering 选择前者。

### Vibe Coding 的困境

[[VibeCoding]] 也是"以少御多"尝试——自然语言描述复杂系统，Ashby 定律预言了瓶颈。

## 原文出处

- W. Ross Ashby, *An Introduction to Cybernetics*, 1956, Chapter 11
- Ashby 本职是英国精神科医生，研究大脑如何维持稳定

## 相关页面

- [[Harness-Engineering]]
- [[VibeCoding]]
- [[Platform-Engineering]]
- [[Terraform]]
- [[Spec驱动开发]]
