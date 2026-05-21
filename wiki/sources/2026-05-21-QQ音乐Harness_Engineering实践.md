---
title: "QQ音乐Harness Engineering实践"
date: 2026-05-21
tags:
  - Harness-Engineering
  - AI-Agent
  - 软件工程
  - 大仓多服务
  - Multi-Agent
  - 工程治理
type: source
confidence: EXTRACTED
source: https://mp.weixin.qq.com/s/yw3DvqKBIV5fIZkSG12zdA
---

# QQ音乐Harness Engineering实践

> 原文链接：https://mp.weixin.qq.com/s/yw3DvqKBIV5fIZkSG12zdA

## 基本信息

- **来源：** 微信公众号原创（腾讯云开发者）
- **字数：** 约15000字
- **核心观点：** 在大仓多服务（Monorepo Microservices）场景中，如何把AI协作从对话式编码升级为可控、可审计、可复用的工程过程
- **标签：** Harness Engineering、AI-Agent、软件工程、工程治理、大仓多服务、Multi-Agent治理

## 核心概念

### 核心公式：代码产出 = AI能力 × 上下文质量

**乘法而非加法**：当上下文质量趋近于零时，模型再强产出也是零。瓶颈不在模型，而在上下文。

### 五类上下文缺口

1. **隐性规范** - 团队约定的锁机制、埋点规则、错误码空间
2. **历史决策** - "为什么当时选了A方案不选B"
3. **服务契约** - IDL字段的冻结状态、下游是否强依赖
4. **跨服务依赖** - 同一个需求要改哪几个服务、谁调谁
5. **演进轨迹** - 某个模块上次大改的坑、灰度策略

## 主要内容

### 1. 从Vibe Coding到Harness Engineering

AI辅助编程的三代演进：代码补全 → 对话式编码（Chat）→ 自主式Agent

**Vibe Coding的三个结构性缺陷：**
- 信息损耗：同一句话多次执行给出不同实现
- 知识孤岛：AI只知训练语料里的通用知识，不懂团队历史决策
- 验证断档："能跑"就直接提交，概率性错误顺着MR滑进主干

**Harness Engineering核心理念**：AI参与问题分析、方案设计、编码实现、审查和验证，但最终判断权始终留在工程师手中。

### 2. QQ音乐Harness Engineering框架

业务场景：50+微服务，单仓多服务与多仓协同（业务仓、IDL契约仓、Harness规范仓）

**四类约束：**
- **流程约束** - 五阶段主流程 + 四道门禁
- **拓扑约束** - .service-matrix/dependencies.yaml
- **契约约束** - 三仓联动 + IDL门禁
- **知识约束** - context/team/、context/harness-framework/、context/project/三层知识
- **演进约束** - Self-Refinement + experience/*.md版本化沉淀

### 3. 五阶段 + 四门禁

**五阶段：** 初始化 → 需求定义⭐ → 设计⭐ → 开发⭐⭐ → 交付

**四道强制门禁：**
1. 需求评审门禁（阶段2.2）- 拦住"需求没理解对"
2. 设计门禁（阶段3.3）- 拦住"方案漏了关键约束/没追溯到需求"
3. Dev进入门禁（阶段4.2）- 拦住"feature拆分不合规，开发起点不对"
4. 服务仓库检查（阶段4.3）- 拦住"三仓分支漂移/IDL契约仓未就位"

### 4. 三层知识体系

| 层级 | 位置 | 范围 |
|------|------|------|
| 团队级 | context/team/ | 所有项目必须遵循（Git规范、错误码空间、日志规范） |
| 框架工程级 | context/harness-framework/ | 所有需求研发必须遵循（五阶段流程、门禁规则、文档模板） |
| 服务级 | context/project/{project}/{module}/{service}/ | 特定服务（架构图、API、运维手册、踩坑经验） |

### 5. 三仓联动

每个需求在三个仓里用完全相同的分支名 `feature/{devops-name}/{tapd-id}`：
- **Harness仓（脑）** - 规范、知识、需求状态、工具链
- **业务仓（手脚）** - 代码和测试
- **IDL契约仓（神经）** - 跨服务协议

### 6. Skill Agent Command三件套

- **34个Skill** - 可复用工作流规范/最佳实践
- **24个Agent** - 自主子任务执行者
- **35个Slash Command** - 标准化入口

### 7. Self-Refinement

让AI从错误中沉淀经验的闭环机制：
1. 用户纠正AI某个错误
2. AI识别"模式性教训"还是"一次性diff"
3. AI主动提议沉淀层级（团队级/框架工程级/服务级）
4. 用户确认后生成experience文档/更新Skill/修订规范
5. 下次同类场景，AI主动引用

## 关键观点

- **Harness Engineering是L5工程治理层**，不替代执行工具（Claude Code/Gemini CLI等），只定义执行工具必须遵守的工程上下文和协作协议
- **工程化不是慢，是稳** - 错误越早被拦住，代价越低
- **上下文质量的提升比模型能力的提升更高效** - 模型能力依赖外部厂商，上下文质量完全掌握在团队自己手中

## 相关页面

### 核心实体
- [[Multi-Agent-Governance]] - Multi-Agent治理
- [[Five-Stage-Gate-Workflow]] - 五阶段四门禁
- [[Self-Refinement-Loop]] - 自我改进闭环
- [[Three-Warehouse-Linkage]] - 三仓联动
- [[Service-Matrix]] - 服务矩阵

### 相关概念
- [[Vibe-Coding]] - AI编程第二阶段问题
- [[Harness-Engineering]] - Harness工程框架
- [[Superpowers]] - Claude Code Skill集合
