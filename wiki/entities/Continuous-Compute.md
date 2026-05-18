---
tags: [CI/CD创新, 持续计算, Agent驱动]
created: 2026-05-18
updated: 2026-05-18
sources: ["2026-05-15-CI_CD_又被杀死了？.md"]
---

# Continuous-Compute

> 面向AI Agent并发海量PR的新一代CI架构，替代传统为人类节奏设计的CI/CD

## 简介

传统CI/CD为人类节奏设计，扛不住Agent并发海量PR。Continuous Compute核心变化：无PR，以Intent+Plan驱动→Agent Harness循环→Pre-Merge Queue→Git明细。人类只看Intent/Result对照，External Validation交给Security LLM等Agent。

## 相关页面

- [[Harness-Engineering]]
