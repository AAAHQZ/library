---
source: article
title: 零一智源superpowers底层实现解析
date: 2026-04-21
author: 
published: 
references: []
summary: "原文链接：https://mp.weixin.qq.com/s/-elbJezcr_58OpPaAS7Sdg"
---

# 零一智源superpowers底层实现解析

> 原文链接：https://mp.weixin.qq.com/s/-elbJezcr_58OpPaAS7Sdg




关于superpowers的使用和底层实现，已经有不少解读，但是“纸上来的终觉浅”，还是实际梳理一遍，心里踏实。另外，关于如何使用，不在本文讨论范围内。本文的目的：

1. 深入superpowers的底层实现，加深了解，更好的使用这一工具
2. 更进一步：借鉴superpowers的底层实现，可以构建个人的组合技能

## Superpowers 是什么

## 全景概览——有个物理印象

Superpowers可以分为3层，接口层-编排层-原子能力层，其中接口层是指：可以被claude code、cursor、codex、opencode等使用；编排层是指：在应用中提出的需求，会触发多个技能组合（比如：brainstorming->writing-plans->executing-plans->code-review）；原子能力层是指：superpowers有14个技能。如下图所示：

![](零一智源superpowers底层实现解析_image_001.png)

Superpowers 不是工具，不是框架—— **它是一套给 AI 装上"职业习惯"的标准化开发流程** 。如何理解呢？在https://skills.sh 中搜索superpower时，出现的不是一个单一技能，而是一个技能合集：

![](https://mmbiz.qpic.cn/sz_mmbiz_png/peicftpGZflvvQib7AOE2oEREHuWQCIiaQQiboEwqibAk2Zz1q6yvOQmrOEiaQfxhEjjGPNibqh4mSH8O9obImjYnnSR3bnTRQLRLwWiaQnsSZNEOiaE/640?wx_fmt=png&from=appmsg)

通过官方插件市场安装后，本地安装目录在~/.claude/plugins/cache/claude-plugins-official/superpowers/5.0.7，其文件组织形式如下（有删减，不影响理解）：

```
.
├── agents
│   └── code-reviewer.md
├── AGENTS.md -> CLAUDE.md
├── CLAUDE.md
├── CODE_OF_CONDUCT.md
├── commands
│   ├── brainstorm.md
│   ├── execute-plan.md
│   └── write-plan.md
├── docs
├── hooks
│   ├── hooks-cursor.json
│   ├── hooks.json
│   ├── run-hook.cmd
│   └── session-start
├── LICENSE
├── package.json
├── README.md
├── RELEASE-NOTES.md
├── scripts
│   ├── bump-version.sh
│   └── sync-to-codex-plugin.sh
├── skills
│   ├── brainstorming
│   ├── dispatching-parallel-agents
│   ├── executing-plans
│   ├── finishing-a-development-branch
│   ├── receiving-code-review
│   ├── requesting-code-review
│   ├── subagent-driven-development
│   ├── systematic-debugging
│   ├── test-driven-development
│   ├── using-git-worktrees
│   ├── using-superpowers
│   ├── verification-before-completion
│   ├── writing-plans
│   └── writing-skills
└── tests
```

## using-superpowers——引导技能

### 模式对比

在确定superpower是如何实现工作流编排之前，首先引导技能—— `using-superpowers` ，其功能描述如下：

> description: Use when starting any conversation - establishes how to find and use skills, requiring Skill tool invocation before ANY response including clarifying questions
> 
> **不管用户说什么，你必须先查技能库，绝不直接回复。**

也就是安装了Superpower之后，用户的问题将由各个专家（skill）负责拆解回答，而不是直接丢给模型，体现了“ **工具优先** ”的思想。下图直观展示了，「不使用技能」 VS 「使用superpower」智能体的响应模式。

![](零一智源superpowers底层实现解析_image_003.png)

### 1%命中原则

在 `using-superpowers` 的SKILL.md中，使用 `<EXTREMELY-IMPORTANT>` 特别强调了如下内容：

> <EXTREMELY-IMPORTANT>If you think there is even a 1% chance a skill might apply to what you are doing, you ABSOLUTELY MUST invoke the skill.
> 
> IF A SKILL APPLIES TO YOUR TASK, YOU DO NOT HAVE A CHOICE. YOU MUST USE IT.
> 
> This is not negotiable. This is not optional. You cannot rationalize your way out of this.</EXTREMELY-IMPORTANT>
> 
> **在任何响应或行动之前，先检查是否有适用的技能。哪怕只有 1% 的可能性，也必须调用。**

为什么是 1%？因为 AI 太容易"觉得不需要"了，所以在 `<EXTREMELY-IMPORTANT>` 之外，有用大写强硬语气写道：

```
IF A SKILL APPLIES TO YOUR TASK, YOU DO NOT HAVE A CHOICE. YOU MUST USE IT.
```

这样，其他技能被调用的可能性大大增加了，这是后续工具链的起点。

---

一点思考：我们在开发自己的技能时，对于特别需要重要的内容/约束，也可以采用 `<EXTREMELY-IMPORTANT>` +大写组合的方式！
...
