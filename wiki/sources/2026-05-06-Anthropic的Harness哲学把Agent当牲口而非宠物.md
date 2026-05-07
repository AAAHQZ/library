---
title: "Agent Native云服务的雏形：从ClaudeManagedAgents看云计算的第三次原生化"
slug: agent-native-cloud-claude-managed-agents
tags: [Agent-Native, Claude-Managed-Agents, 云计算, 云原生, Anthropic]
created: 2026-05-06
updated: 2026-05-06
source: ../raw/wechat/AgentNative云服务的雏形从ClaudeManagedAgents看云计算的第三次原生化.md
references:
  - https://mp.weixin.qq.com/s/cMgOmPiGdxGUmWf1R5PKYA
---

# Agent Native 云服务的雏形

> 从 Claude Managed Agents（CMA）分析云计算的第三次原生化跃迁。论证 Cloud Native（CPU/VM）和 AI Native（GPU/NeoCloud）都无法原生承载 Agent Workload，Agent workload 独有的三个特征（不可信代码动态生成与执行、长周期异步执行、LLM 推理与 CPU 工具调用高频交错）催生 Agent Native 云服务。CMA 的四件套抽象（Agent/Environment/Session/Events）和五大关键技术（虚拟化哲学继承、凭证物理隔离、Memory Store OCC、Outcomes 目标闭环、多 Agent 单层委派）。

## 核心观点

1. **第三代云形态**：Agent Native 云服务是继 Cloud Native（CPU/VM）、AI Native（GPU/NeoCloud）之后的第三次原生化
2. **虚拟化对象跃迁**：从"硬件虚拟化"到"认知工作流虚拟化"——Session 虚拟化"经验"、Harness 虚拟化"调度"、Sandbox 虚拟化"执行"、Vault 虚拟化"身份"、Memory Store 虚拟化"记忆"
3. **CMA 是融合编排层**：建立在经典云 CPU 算力与 NeoCloud GPU 算力之上，统一暴露 Agent 原语，不取代前两代云

## 为什么前两代云无法承载 Agent Workload

| 特征 | 经典云 | NeoCloud | Agent Workload |
|------|--------|----------|----------------|
| 代码可信度 | 可信（人写，有 CI/CD） | 可信 | 不可信（LLM 生成） |
| 执行周期 | 毫秒-秒级 | 秒-分钟级 | 分钟-小时级 |
| 推理/执行 | 纯 CPU | 纯 GPU | GPU+CPU 高频交错 |
| 状态管理 | 集中数据库 | 无状态 | 持久化状态+检查点 |

**Agent workload 三个独有特征**：
1. **不可信代码的动态生成与执行**：LLM 即兴生成代码，行为空间无界
2. **长周期异步执行**：数分钟到数小时，需容忍断连、崩溃、上下文溢出
3. **LLM 推理与通用计算高频交错**：Reason-Act 循环每轮都 GPU 推理 + CPU 工具调用

## CMA 四件套领域抽象

| 概念 | 作用 |
|------|------|
| **Agent** | 可复用、版本化的配置实体（模型、系统提示词、工具集、MCP、Skills），不是进程而是"Agent 蓝图" |
| **Environment** | 云端容器模板（预装运行时、网络规则、挂载文件系统） |
| **Session** | 带持久化状态的长周期载体，底层是 append-only 事件日志 |
| **Events** | 应用与 Agent 双向通信（用户指令、工具结果、状态更新），通过 SSE 推送 |

## CMA 五项关键技术

### 1. Harness 与 Sandbox 解耦
- Session 定义为 append-only 事件日志，独立于模型上下文窗口持久化
- Sandbox 崩溃 → Harness 捕获为普通"工具调用错误" → 模型决定重试 → provision 新容器 → Session 无缝续跑

### 2. 凭证物理隔离（Vault/Proxy 架构）
- OAuth 令牌存在沙箱外的独立 Vault
- Claude 通过专用 Proxy 调用 MCP 工具，Proxy 拿会话绑定令牌去 Vault 取凭证
- Sandbox 自始至终对任何凭证一无所知，沙箱与真实凭证无物理连通路径

### 3. Memory Store OCC
- 创建时要求 `precondition={"type": "not_exists"}` 防误覆盖
- 编辑时要求传入原内容 SHA-256 哈希值，远端不一致时抛 `409 memory_precondition_failed`
- 单条上限 100KB，Session 最多挂 8 个 Memory Store

### 4. Outcomes 目标导向闭环
- 用户通过 `user.define_outcome` 声明最终交付形态和评分 Rubric
- 独立上下文窗口的评估器对产物做客观审查
- `satisfied` 即闲置，`needs_revision` 差距反馈主 Agent 自主迭代，`max_iterations_reached`（默认 3 次，上限 20 次）强制终止

### 5. 多 Agent 单层委派控制
- 子 Agent 共享底层文件系统，但每个子线程有独立上下文历史、系统提示词、MCP、Skills
- 主 Agent 的 `callable_agents` 数组明确列出授权子 Agent
- 子 Agent **无权继续向下裂变**，防止调用链路雪崩和递归死循环

## CMA 与 PaaS 的本质差异

| 维度 | CMA | AWS Step Functions + Lambda + Bedrock |
|------|-----|--------------------------------------|
| 虚拟化粒度 | Agent/Environment/Session 领域抽象（包含完整编程模型） | API 拼接 |
| 计费模型 | 混合计费（CPU 沙箱 + GPU 推理在同一 Session） | 分离计费 |
| Model-Infra Co-design | Anthropic 同时掌握模型与基础设施，双向闭环 | 水平 PaaS 无法复制 |

## 产业冲击

**Agent Native 云厂商**：Anthropic（CMA）握先手约 6-12 个月，OpenAI 开发者生态领先，Google 硬件整合最深但兑现最慢

**模型中立云厂商（AWS/Azure）**：长期风险是规模效应利好垂直整合玩家；AWS 放弃 Model-Infra Co-design 护城河，Azure 左右互搏

**NeoCloud**：商业模式是卖 GPU-hour，但 Agent 负载 GPU 利用率低；CoreWeave 被迫向上延伸做推理服务

**SaaS 冲击节奏**：
- 浅层工作流 SaaS（18-24 个月）：最直接替代压力
- 深层数据 SaaS（有 12 个月转型窗口）：API 授权费转型
- 合规/监管 SaaS：几乎不受冲击

## 三元并存格局预判

- **全球商业 Agent Cloud**：Anthropic CMA、OpenAI 类似产品、2-3 个中国厂商
- **主权 Agent Cloud**：欧盟、中国、印度等基于产业政策与数据主权的本土替代
- **开源私有部署**：LangGraph、Multica、Cabinet + 企业自有算力

## 相关页面

- [[Agent-Native]]
- [[Claude-Managed-Agents]]
- [[Harness-Engineering]]
- [[Sandbox]]
- [[Memory-Store]]
