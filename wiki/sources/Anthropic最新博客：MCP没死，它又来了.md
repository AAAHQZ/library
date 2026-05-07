---
source: article
title: Anthropic最新博客：MCP没死，它又来了
date: 2026-04-23
author: 
published: 
references: []
summary: "原文链接：https://mp.weixin.qq.com/s/Sz2hzXiNCyf1YNzPbeUo5Q"
---

# Anthropic最新博客：MCP没死，它又来了

> 原文链接：https://mp.weixin.qq.com/s/Sz2hzXiNCyf1YNzPbeUo5Q

# Anthropic 最新博客：MCP 没死，它又来了

Anthropic 最新发了篇博客，标题叫《Building agents that reach production systems with MCP》，翻译过来是：《构建能触达生产系统的 Agent：MCP 实践指南》。
![img](Anthropic最新博客MCP没死它又来了_image_001.jpg)
MCP 最新博客

在我去年 11 月的文章《[https://mp.weixin.qq.com/s?__biz=MzA4NzgzMjA4MQ==&mid=2453479107&idx=1&sn=0c2fe03df0734b044084d42da43334ac&scene=21#wechat_redirect](MCP 或将成弃子)》和上个月的文章《[https://mp.weixin.qq.com/s?__biz=MzA4NzgzMjA4MQ==&mid=2453481182&idx=1&sn=61787514e168c0bb09c21ef441ff0f5e&scene=21#wechat_redirect](一切软件，都将为 Agent 重写)》、《[https://mp.weixin.qq.com/s?__biz=MzA4NzgzMjA4MQ==&mid=2453481939&idx=1&sn=18cbd02ea060830a89c20486fe8fc02f&scene=21#wechat_redirect](钉钉飞书集体抛弃 MCP，CLI 才是 Agent 的终局)》中，我一直在阐述一个观点：

CLI + Skills 才是 Agent 连接外部系统的正道，因为 MCP 又贵又慢还占上下文。

但今天，MCP 的亲爹 Anthropic 自己出来再一次为 MCP 说话了。

它倒不是说「你们错了」，它说的是：「你们提的那些问题，我们有答案了。」01
## 之前聊过什么

这里来补一下前情提要。

过去一个多月，社区对 MCP 的批评集中在三件事上。

ScaleKit 做了一组严格的 benchmark，拿 GitHub 官方 MCP 服务器和 `gh` CLI 做对照，跑了 75 轮实验。

结果是 CLI 在 token 消耗上便宜 **10 到 32 倍**，按月算，每月 1 万次操作，CLI 大约 3.2 美元，MCP 大约 55.2 美元。**17 倍的成本差距。**
![img](Anthropic最新博客MCP没死它又来了_image_002.jpg)
成本与可靠性对比

问题出在 schema 膨胀上。

GitHub 的 MCP 服务器带了 43 个工具定义，每次对话都得把这 43 个工具的完整描述全塞进上下文。你只是想查个仓库语言，但模型得先读完所有 43 个工具的说明书。光是一个工具的定义就占了 4,026 tokens。

Perplexity 的 CTO Denis Yarats 也表态称：Perplexity 内部正在远离 MCP，原因是 **72% 的上下文窗口被 MCP 占掉了**。

龙虾之父 Peter 也在播客里对痛批 MCP：
> “ MCP 默认污染你的上下文，加上大部分 MCP 做得不好，总体来说不是一个很有用的范式。

而钉钉和飞书也是不约而同绕开 MCP，直接把产品「压扁」成了 CLI。在旧金山街头的随机调查里，CLI 得了 17 票，MCP 仅有 3 票。

当时我们的结论是：**MCP 倒是不会死，但它会退到它该待的地方。**

**而 CLI，将成为 Agent 操作一切软件的默认界面。接下来，整个 SaaS 的 API surface，将全都暴露成 CLI。**02
## Anthropic 的回应

而今天的这篇博客，则是 Anthropic 在重新定义什么是 MCP「该待的地方」。

博客开篇先摆了一个框架：Agent 连接外部系统有三条路，直连 API、CLI、MCP，**各有各的适用场景**。
![img](Anthropic最新博客MCP没死它又来了_image_003.jpg)
三条路

直连 API 适合简单的、一对一的场景。但如果你有 10 个 Agent 要接 10 个服务，那就是 100 个不同的集成方案，每个都要单独搞认证和工具描述。这就是经典的 M×N 问题。
![img](Anthropic最新博客MCP没死它又来了_image_004.jpg)
M×N 噩梦 vs 标准化

CLI 在本地和沙箱环境里确实更合适。Agent 天生就说命令行语言，`--help` 就能自描述，`jq` 就能过滤，pipe 就能组合。这一点 Anthropic 是承认的。

但博客里话锋一转：**生产级 Agent 越来越多的，正在跑在云上。
![img](Anthropic最新博客MCP没死它又来了_image_005.jpg)****三条路对比：API vs CLI vs MCP**

Claude Cowork、Claude Managed Agents、移动端、Web 端……这些环境里没有本地文件系统，跑不了 CLI。而 MCP 的定位，则恰恰就是为这个场景服务的：**一个远程服务器，通吃所有客户端**。

Claude、ChatGPT、Cursor、VS Code，一个 MCP 服务器全覆盖。

博客里给出的一个数据是：**MCP SDK 的月下载量从年初的 1 亿涨到了 3 亿。**

社区怎么争论是一回事，但事实上则是：用脚投票的人越来越多了。03
## Token 解法

这应该算是整篇博客中，最为关键的部分了。

之前社区骂得最狠的就是 token 膨胀。Anthropic 这次则直接给了两个解法。

第一个叫 **Tool Search**。
![img](Anthropic最新博客MCP没死它又来了_image_006.jpg)
Context Usage 传统方式 vs Tool Search 对比

之前的做法是把所有工具定义一股脑塞进上下文。43 个工具，55,000 tokens，还没开始干活，工作台就被说明书堆满了。

![img](Anthropic最新博客MCP没死它又来了_image_007.jpg)

现在的做法是按需加载。Agent 先描述它想做什么，系统在运行时搜索相关工具，只把匹配的几个拉进来。
![img](Anthropic最新博客MCP没死它又来了_image_008.jpg)
Tool Search 效果

博客给出的测试数据是：**工具定义的 token 消耗减少了 85% 以上，工具选择的准确率没有下降。**

**工具要按意图分组，别按 API 分。**
![img](Anthropic最新博客MCP没死它又来了_image_009.jpg)
按意图分组：细粒度工具 vs 意图级工具

而之前 ScaleKit 测出来 GitHub MCP 查仓库语言要 44,026 to
...
