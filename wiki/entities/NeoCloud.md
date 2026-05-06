---
title: "NeoCloud"
date: 2026-05-06
tags:
  - 云计算
  - GPU
  - AI基础设施
type: entity
confidence: EXTRACTED
---

# NeoCloud

> 面向 LLM 训练与推理的 GPU 专用云（CoreWeave、Lambda、Crusoe、Nebius 等），与 AI Native 互用。

## 与 Agent Native 的关系

NeoCloud 处境微妙：
- 商业模式是卖 GPU-hour，不卖工作流
- Agent 负载的 GPU 利用率是脉冲式、低占空比的
- 大量时间花在 CPU 侧的沙盒执行、文件 IO、工具调用
- 高密度 GPU 集群利用率会出现明显下滑

## 分化趋势

- 头部玩家（CoreWeave）被迫向上延伸做推理服务和 Agent 基础设施
- 中小 NeoCloud 面临退化为 GPU 现货市场的压力

## 相关素材

- [[2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化|sources/2026-05-06-AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化]]
