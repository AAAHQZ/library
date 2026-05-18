---
tags: [CI/CD, 持续计算, Agent驱动]
created: 2026-05-18
updated: 2026-05-18
sources: ["2026-05-15-CI_CD_又被杀死了？.md"]
---

# Pre-Merge-Queue

> Continuous Compute架构中的Pre-Merge Queue，用于处理Agent提交的PR

## 简介

传统PR→Merge Queue串行化瓶颈。Continuous Compute新架构：Intent+Plan驱动→Agent Harness循环→Pre-Merge Queue→Git明细。

## 相关页面

- [[Harness-Engineering]]
