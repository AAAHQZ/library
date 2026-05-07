---
title: "AI 流量分析"
date: 2026-05-07
tags:
  - AI Agent
  - 流量分析
  - 内容分发
  - 数据分析
type: entity
confidence: EXTRACTED
---

# AI 流量分析

> 对 AI Agent 和爬虫驱动的网络流量进行的分析与分类方法。

## 定义

AI 流量分析指在 Agent 时代，区分和量化网站流量的构成——传统人类访客、AI 训练爬虫、SEO 爬虫、以及 Agent 执行任务时产生的自动化请求。传统 Web 分析工具（Google Analytics）仅能捕捉人类行为，对 Agent 流量几乎无感知。

## 三类 Agent 时代流量

| 流量类型 | 示例 | 目的 |
|---------|------|------|
| AI 爬虫 | GPTBot、ClaudeBot、PerplexityBot、ByteSpider | 抓取训练数据 |
| SEO 爬虫 | Googlebot、AhrefsBot | 索引页面（下游 AI 系统也依赖） |
| Agent 类访问 | 脚本驱动的浏览器模拟请求 | Agent 执行任务时主动抓取 |

## 特征

- Agent 类访问：一次性 IP 数量暴涨、API endpoint 高频探测
- 传统 GA 统计无法反映真实流量构成
- Cloudflare 等 CDN 的日志分析成为主要观察手段

## 相关素材

- [[2026-05-07-三个月翻100倍Agent时代的流量画像|sources/2026-05-07-三个月翻100倍Agent时代的流量画像]]
