---
tags: [Anthropic, Harness, Agent架构, 牲口哲学, 基础设施]
created: 2026-05-05
updated: 2026-05-05
sources: [../raw/Anthropic 的 Harness 哲学：把 Agent 当牲口，而非宠物.md]
---

# Anthropic 的 Harness 哲学：把 Agent 当牲口，而非宠物

> Anthropic 将"宠物 vs 牲口"的运维哲学引入 [[AI-Agent]] 架构设计——通过 Session/Harness/Sandbox 三层解耦，让 Agent 基础设施从应用层沉入基础设施层，实现可替代、可扩展、安全的托管服务。

## 核心观点

1. **宠物 vs 牲口隐喻**：宠物服务器（如 Zeus、Athena）需要精心维护，挂了要抢救；牲口服务器（#001、#002）坏了直接替换。Anthropic 把这套哲学引入 Agent 设计。
2. **脑手分离架构**：Managed Agents 拆成三层——Session（会话日志，只增不改）、Harness（编排层，无状态）、Sandbox（执行环境，隔离安全）
3. **牲口化收益**：p50 首 Token 延迟下降约 60%，p95 下降超过 90%；多个 Harness 可连接多个 Sandbox，一个挂了新的自动接替
4. **安全隔离**：凭证永不进入 Sandbox，Git token 初始化时注入，OAuth token 通过 MCP 代理访问——即使 prompt injection 成功也偷不到钥匙
5. **信任框架五原则**：人类控制、价值对齐、安全交互、透明度、隐私保护；Plan Mode 从"逐步审批"变为"战略审批"

## 架构设计

### 三层分离
```
Session（会话日志） → Harness（无状态编排） → Sandbox（隔离执行）
```

- **Session**：只增不改的事件流，存在外部数据库（Postgres/SQLite），容器挂了日志还在
- **Harness**：无状态，从 Session 读取状态，调用 Claude、路由工具、管理上下文
- **Sandbox**：代码执行环境，隔离且碰不到凭证

### 接口抽象
每层只需满足接口约定，具体实现可替换——灵感来自操作系统，接口比实现更持久。

## 牲口哲学核心

> "我们把接口当作比实现更持久的东西来对待，就像操作系统在几十年前就虚拟化了硬件一样。"

**没有什么是不可替代的。** 这与云计算演进路径相似：自己搭服务器（宠物）→ EC2（自己管的牲口）→ Lambda（函数即服务）。

## 基础设施沉入

Anthropic 把 MCP 捐给 Linux 基金会的 Agentic AI Foundation——当 Agent 变成牲口，接口就变成标准件。Harness 正从应用层沉入基础设施层。

## 相关页面

- [[AI-Agent工程实践]]
- [[从第一性原理思考AgenticEngineering|sources/2026-05-05-从第一性原理思考AgenticEngineering]]
- [[深度解析HarnessEngineering|sources/2026-04-15-深度解析HarnessEngineering]]
