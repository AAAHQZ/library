---
title: "三大Agent平台独立收敛到同一个Markdown方案"
slug: three-agent-platforms-markdown-convergence
tags: [Agent记忆, Markdown, Karpathy-LLM-Wiki, 知识管理, RAG]
created: 2026-05-06
updated: 2026-05-06
source: ../raw/wechat/2026-05-06-三大_Agent_平台独立收敛到同一个_Markdown_方案——这不是最优解，只是最容易走通的路.md
references:
  - https://antigravity.codes/blog/karpathy-llm-knowledge-bases
  - https://mem0.ai/blog/state-of-ai-agent-memory-2026
---

# 三大 Agent 平台独立收敛到同一个 Markdown 方案

> 分析三个独立 AI Agent 平台（Manus、Claude Code、OpenClaw）趋同进化到 Markdown 文件作为 Agent 记忆载体的现象。指出 Karpathy 的 llm-wiki 方法论的贡献与局限，论证人的知识系统和 Agent 的记忆系统是两个根本不同的问题，探讨 Wiki 模式在 Agent 规模下的五个断裂点及技术栈分层方向。

## 核心观点

1. **趋同进化是事实而非最优**：三个完全独立的平台各自收敛到 Markdown，证明这是"最容易走通的路"而非"最优解"
2. **Karpathy 的真正贡献**：把注意力从"生成"转向"积累"——从 LLM 生成答案到 LLM 维护持续生长的知识库
3. **Wiki 模式在 Agent 规模下存在断裂点**：事实级查询效率低、Token 预算硬约束、记忆漂移、排序难题、高频写入挑战
4. **未来架构是混合的**：人的层用 Markdown，Agent 的层用结构化存储

## Karpathy LLM Wiki 的核心设计

**架构**：raw/（只读原始材料）+ wiki/（LLM 生成的结构化 Markdown）+ CLAUDE.md/AGENTS.md（行为规则）

**优势**：
- 解决 RAG 根本缺陷：LLM 在 ingest 阶段已读完全文、理解上下文、写出结构化文章；查询时加载的是综合过的知识而非碎片
- "知识复利"机制：使用产生新的综合洞察，被写回 wiki 成为新页面
- KV Cache 友好：稳定文本前缀对缓存命中率极高（$0.30/百万 vs $3.00/百万未缓存）

**局限**：设计假设消费者是"坐在 Obsidian 前的人"，每天交互几次到几十次

## 三个平台的趋同进化

| 平台 | 记忆方案 | 特点 |
|------|---------|------|
| Manus | todo.md | 每步自动重写的 checklist |
| Claude Code | CLAUDE.md + MEMORY.md | 分层文件，200 行上限的动态记忆索引 |
| OpenClaw | MEMORY.md + 日期目录 | 记忆文件按日期组织 |

**趋同原因**：帕累托前沿上最显而易见的点——人类可读、Git 可追踪、工具链零依赖、LLM 原生友好

## Wiki 模式在 Agent 规模下的五个断裂点

### 1. Agent 需要事实，不是页面
- 人类想读整篇文章；Agent 通常只需要一个答案
- Wiki 不支持事实级查询，检索单元是页面不是三元组
- 在数千次调用规模下，页面加载变成结构性成本

### 2. Token 预算是硬约束
- Full-context 方式：72.9% 准确率，但每次查询 ~26,000 token，p95 延迟 17.12 秒
- Mem0 方案：66.9% 准确率（仅差 6 个百分点），p95 延迟 1.44 秒（降低 91%），token 节省 90%
- CLAUDE.md + MEMORY.md 约 5-15KB，每天 50 次交互月度 token 成本 $30-100

### 3. 记忆漂移是可靠性问题
- Markdown wiki 没有内置时间维度，所有内容平等存在于同一命名空间
- Karpathy 的 log.md 是审计日志而非结构化时序数据
- Zep/Graphiti 的时间知识图谱在 LongMemEval 上比 Mem0 高出 15 个百分点

### 4. 排序比存储难
- Markdown 是平的：没有置信度、重要性、新鲜度、强化次数等元数据
- Mercury Second Brain：每条记忆带 10 种标签 + 置信度/重要性/耐久度评分；强化 3 次以上自动从短期升长期

### 5. 高频写入改变了一切
- 人类偶尔更新笔记；Agent 在每次任务/对话/工具调用/决策后都可能更新
- Claude Code auto-memory 已实质上构建了结构化记忆系统：用文件名当主键、frontmatter 当 schema、目录结构当分区

## Agent 记忆的三层分类与四种架构

**三层记忆类型**：
- **情景记忆**：具体发生过的事件（对话日志、工具调用轨迹）
- **语义记忆**：累积的知识和事实（用户偏好、项目架构）
- **程序记忆**：操作规则和工作流（代码规范、部署流程）

**四种存储架构**：
| 架构 | 代表 | 强项 | 弱项 |
|------|------|------|------|
| 向量记忆 | Mem0, Zep | 语义相似度检索、低延迟 | 多跳关系弱 |
| 图记忆 | Zep/Graphiti, Mem0g | 实体关系、时序推理 | 延迟更高 |
| 情景记忆 | Letta/MemGPT | 长对话连贯性 | 额外延迟 |
| 混合系统 | 2026 主流 | 向量+图+情景缓冲组合 | 集成复杂度高 |

## 真正的架构是混合的

**给人的层用 Markdown**：笔记、报告、摘要、日志、身份文件（CLAUDE.md/AGENTS.md）
**给 Agent 的层用结构化存储**：事实、实体、关系、偏好、任务状态、索引、时间戳、置信度评分

## 对个人研究者和 Agent 开发者的建议

**个人研究者**：100 篇文章/40 万字规模以下，Markdown wiki + 大上下文窗口已被验证有效；关键心智：你是提问者和审阅者，不是维护者

**Agent 开发者**：记忆文件超过 15KB 且继续增长时，需要引入结构化记忆层；Letta 三层模型（Core/Recall/Archival）是参考架构

## 相关页面

- [[Karpathy-LLM-Wiki]]
- [[Agent-Memory框架]]
- [[Letta-MemGPT]]
- [[Mem0]]
