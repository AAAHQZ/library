---
title: "Skills (Agent能力封装模式)"
date: 2026-05-06
tags:
  - AI Agent
  - 架构
  - DevOps
type: entity
confidence: EXTRACTED
---

# Skills (Agent能力封装模式)

> 将复杂运维动作封装成可复用、可审计、可版本化的Agent能力单元。

## 定义

Skills 模式将具体能力封装为 Skills。一个 Skill 可以对应"自动代码审查""Harbor 镜像清理""Docker 镜像构建""漏洞扫描策略配置"等场景。它把复杂操作隐藏在稳定接口后面，Agent 只需要理解什么时候调用、传入什么参数、如何处理结果。

## 工程价值

- **工程边界清晰**：减少 Agent 直接拼接命令的风险
- **平台化基础**：平台团队把常用 DevOps 能力做成可审计、可复用、可版本化的 Skill
- **安全可控**：避免每个项目各自实现一套不受控的自动化

## 示例

- 镜像清理 Skill：内部固定保留策略、白名单项目和删除前校验
- 部署 Skill：内部固定灰度比例、健康检查和回滚条件

## 相关素材

- [[2026-05-06-Agent驱动的CICD流程变革从自动化执行到智能自治|sources/2026-05-06-Agent驱动的CICD流程变革从自动化执行到智能自治]]
