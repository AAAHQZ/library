---
tags: [Harness-Engineering, 工程架构, IDL, 微服务, 协同]
created: 2026-05-21
updated: 2026-05-21
sources: ["2026-05-21-QQ音乐Harness_Engineering实践.md"]
---

# Three-Warehouse-Linkage

> 每个需求在三个仓里用完全相同的分支名，实现跨仓协同的基础约束

## 简介

Three-Warehouse-Linkage（三仓联动）是Harness Engineering中极具特色的工程实践：业务仓、IDL契约仓、Harness规范仓三个仓库在同一个需求下使用完全相同的分支名。

## 三仓模型

```
┌───────────────┐   ┌────────────────┐   ┌───────────────┐
│  Harness仓    │   │   业务代码仓   │   │   IDL契约仓   │
│    (脑)       │   │    (手脚)     │   │    (神经)     │
│               │   │               │   │               │
│ 规范/知识/     │   │ 代码/测试      │   │ .jce契约      │
│ 需求状态/      │   │               │   │ (仅当涉及     │
│ 工具链        │   │               │   │  IDL变更)    │
└───────────────┘   └────────────────┘   └───────────────┘
```

## 分支命名规范

`feature/{devops-name}/{tapd-id}`

示例：一条TAPD单T12345，在三个仓里都创建分支 `feature/Base/T12345`

## 核心价值

1. **追溯链整洁** - 一条TAPD单ID → 三仓分支名一对一
2. **自动校验** - 阶段4.3服务仓库检查门禁自动校验三仓分支一致性
3. **快速对齐** - CR时可以快速对齐三个仓的改动
4. **同步回滚** - 回滚时三个仓同步处理，避免"代码回了、IDL没回"的不一致

## 门禁保障

阶段4.3门禁强制校验三仓分支名必须完全一致，任何漂移都会阻塞进入阶段4.4编码循环。

## 占位符词典

全仓只允许使用预定义的占位符，保证路径解析的一致性：

| 占位符 | 语义 |
|--------|------|
| {business-repo} | 业务代码仓根的磁盘路径（绝对） |
| {business-repo-name} | 业务代码仓根的目录名 |
| {idl-repo} | IDL契约仓 |
| {project-name} | 逻辑项目名 |
| {requirement-id} | 需求ID |
| {module-name} | 业务模块 |
| {service-name} | 服务 |

## 相关页面

- [[Five-Stage-Gate-Workflow]] - 五阶段四门禁
- [[Service-Matrix]] - 服务矩阵
- [[Multi-Agent-Governance]] - 多Agent治理
