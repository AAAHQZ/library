---
tags: [Superpowers, Agent技能系统, 工作流编排, Claude-Code, Skill]
created: 2026-04-21
updated: 2026-05-05
sources: [../raw/零一智源superpowers底层实现解析.md]
---

# 零一智源 Superpowers 底层实现解析

> Superpowers 不是工具，不是框架——它是一套给 AI 装上"职业习惯"的标准化开发流程。

## 核心架构

Superpowers 分为三层：
- **接口层**：支持 Claude Code、Cursor、Codex、OpenCode 等
- **编排层**：需求触发多技能组合（如 brainstorming→writing-plans→executing-plans→code-review）
- **原子能力层**：14 个独立技能

## 引导技能（using-superpowers）

核心原则：**不管用户说什么，必须先查技能库，绝不直接回复**

### 1% 命中原则

> If you think there is even a 1% chance a skill might apply, you ABSOLUTELY MUST invoke the skill.

只要有 1% 的可能性技能适用，就必须调用。这是避免 Agent"偷懒"的强硬约束。

### 指令优先级

1. 用户显式指令（最高）
2. Superpowers 技能（覆盖默认系统行为）
3. 默认系统提示词（最低）

### 技能分类

- **刚性技能**（TDD、调试）：严格照搬，不许变通
- **柔性技能**（设计模式）：理解核心原则后灵活应用

### 出场顺序

当多个技能都适用时：
1. **流程类技能优先**（brainstorming、debugging）—— 决定"怎么做"
2. **实现类技能其次**（frontend-design、mcp-builder）—— 指导"怎么干"

## 技术实现

### Session Start Hook

引导技能通过 `session-start` hook 在会话启动时完整加载：
```
会话启动 → 触发 SessionStart 事件 → 执行 run-hook.cmd session-start 
→ 读取 using-superpowers/SKILL.md 完整内容 → 注入上下文
```

### 文件结构

```
skills/
├── brainstorming/
├── dispatching-parallel-agents/
├── executing-plans/
├── finishing-a-development-branch/
├── receiving-code-review/
├── requesting-code-review/
├── subagent-driven-development/
├── systematic-debugging/
├── test-driven-development/
├── using-git-worktrees/
├── using-superpowers/        ← 引导技能
├── verification-before-completion/
├── writing-plans/
└── writing-skills/
```

## 设计智慧

1. **明确不要做什么**：12 种常见借口 + 现实真相对照表，防止 Agent 偷懒
2. **用户只负责提 WHAT**：AI 必须按技能规定的 HOW 执行
3. **防止无限套娃**：`<SUBAGENT-STOP>` 标记阻止 subagent 跳过元技能
4. **技能链式调用**：一个技能可以指定后续应该调用什么技能

## 相关页面

- [[从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering|sources/2026-05-05-从玩具到生产力用真实项目讲透AIAgent的HarnessEngineering]]
- [[Claude_Code_Skills|sources/2026-04-28-Claude_Code_Skills]]
- [[AIAgent架构设计一记忆系统设计|sources/2026-04-21-AIAgent架构设计一记忆系统设计]]
