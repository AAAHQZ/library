---
tags: [Harness-Engineering, 服务治理, 微服务, 拓扑, 依赖管理]
created: 2026-05-21
updated: 2026-05-21
sources: ["2026-05-21-QQ音乐Harness_Engineering实践.md"]
---

# Service-Matrix

> .service-matrix/dependencies.yaml —— Harness Engineering中的服务拓扑与仓库路径单一真相源

## 简介

Service-Matrix是QQ音乐Harness Engineering框架中管理50+微服务拓扑的核心机制，通过`.service-matrix/dependencies.yaml`声明服务关系，让AI不再"猜"服务关系和路径。

## 核心结构

```yaml
workspace: ".."
business_repo: "music_commercial_go_proj"
idl_repo: "qqmusicjce"
default_team: "music-commercial"

teams:
  music-commercial:
    business_repo: "music_commercial_go_proj"
    idl_repo: "qqmusicjce"

modules:
  vip:
    team: music-commercial
    name: 会员核心域

services:
  vipapi:
    module: vip
    repo_path: "{business-repo}/vipapi"
    idl_required: true
  assetcardmallcgi:
    module: assetcard
    repo_path: "{business-repo}/assetcard/mall/assetcardmallcgi"
```

## 关键特性

### 1. 路径从不硬编码

使用`{business-repo}`、`{idl-repo}`占位符，跨机器、跨账号无缝迁移

### 2. 多团队支持

`teams:`块让不同业务团队有各自的业务仓+IDL仓

### 3. Active Team三级解析

`$HARNESS_TEAM` > `.harness/local.yaml` > `default_team`

既支持会话级临时切换，也支持仓库级默认

### 4. 校验自动化

`scripts/validate-service-matrix.js`在每次CI运行，保证：
- 占位符能正确解析
- 没有幽灵依赖

## 服务路径深度分布

57个服务的真实分布：
- 1级：21个（vipapi、VipSale等）
- 2级：32个（assetcard/mall/*、supplier/*）
- 3级：4个（musicvip/viptab/*）

**超过一半的服务含"子域"层** → 框架不能对深度作过强假设

**解决方案**：一切走repo_path真相源，由它自身决定深度

## 核心价值

1. **影响面分析从"搜索"变成"查表"** - AI不再需要猜测服务依赖关系
2. **跨服务需求可自动解析** - 根据.service-matrix/dependencies.yaml读取服务依赖
3. **路径解析一致性** - 全仓使用统一占位符词典

## 相关页面

- [[Multi-Agent-Governance]] - 多Agent治理
- [[Five-Stage-Gate-Workflow]] - 五阶段四门禁
- [[Three-Warehouse-Linkage]] - 三仓联动
- [[Self-Refinement-Loop]] - 自我改进闭环
