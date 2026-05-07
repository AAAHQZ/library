---
source: article
title: 阿里云的P0故障和PaaS产品的特性
date: 2026-05-05
author: 
published: 
references: []
summary: > 原文链接：https://mp.weixin.qq.com/s/JLGulYq3QGbj4euuyfmJdw
---

# 阿里云的P0故障和PaaS产品的特性

> 原文链接：https://mp.weixin.qq.com/s/JLGulYq3QGbj4euuyfmJdw

> 公众号：云算计

# 阿里云的P0故障和PaaS产品的特性

**目录
**

- 
读者的关注点有问题

- 
硬核资料的来源

- 
真实的故障叫PaaS鉴权故障

- 
PaaS云的鉴权和IaaS不一样

- 
为什么故障动静那么大

- 
我为什么感谢阿里云

**0.&nbsp;开篇曲**

我不是故意的，找图的时候找到的

- 
她唱着他乡遇故知 —— 客户端走过广域网来访问PaaS云产品

- 
一步一句是相思 —— 每一次访问都要做认证

- 
台下人金榜正提名 —— 鉴权服务拿到一个新的白名单

- 
不曾认台上旧相识 ——&nbsp;直接把客户端访问给拒了

**1. 读者的关注点有问题**

我从写公众号至今，为了装薄一，我从来不追热点，只写硬核知识分享，阅读量老是在1000-3000之间徘徊，写的最好的那篇《[一文谈尽边缘计算](http://mp.weixin.qq.com/s?__biz=MzU5NDAzNDEyMA==&amp;mid=2247484164&amp;idx=1&amp;sn=cebb77ba31a66a6068227905bd6df4cd&amp;chksm=fe062fa3c971a6b5ffd1b6d9acbce5150dff5c0180bc3b0d08a945c878e427f72d77ab537065&amp;scene=21#wechat_redirect)》，阅读量也才32767左右。

在这次杭州云故障喜提热搜以后，我凑个热点，趁着各位云计算爱好者发炎之前，写了一篇《[去年今日的杭州云故障不同](http://mp.weixin.qq.com/s?__biz=MzU5NDAzNDEyMA==&amp;mid=2247486210&amp;idx=1&amp;sn=b7aef5b9fbf73362b2b2352f60904cff&amp;chksm=fe0627a5c971aeb30d7da833d7f849568f12e11924e3bcc3fb6a67623ed02c4f47dcbeea5833&amp;scene=21#wechat_redirect)》，阅读量到4万多我是很懵的，你们肯定是把这个当做娱乐内容了。

这次阿里云的故障，我搜集了一些硬核资料，再发一篇凑热点的文章，我看这次凑热点的阅读量能到65535吗。

毕竟我要出书，毕竟本文介绍的观点，也是我书中的核心观点：

- 
应该以什么标准IaaS云和PaaS云？

- 
区分IaaS云和PaaS云产品是故弄玄虚还是真有价值？

![阿里云的P0故障和PaaS产品的特性_image_001.png](阿里云的P0故障和PaaS产品的特性_image_001.png)

**2. 硬核资料的来源**

我发布带厂商标题的《[阿里云故障理性分析](http://mp.weixin.qq.com/s?__biz=MzU5NDAzNDEyMA==&amp;mid=2247486234&amp;idx=1&amp;sn=2eee13babd29f26231a24b77a947b2c9&amp;chksm=fe0627bdc971aeab48404f33de02e5e7b5da0884806b6366f244ae187151e8971525d670dd10&amp;scene=21#wechat_redirect)》的名字，既不怕阿里云说我造谣，也不怕云计算爱好者笑我迂腐，绝不是只看了公开的故障报告，我也拿到了一些硬核资料。

- 
我[前篇文章](http://mp.weixin.qq.com/s?__biz=MzU5NDAzNDEyMA==&amp;mid=2247486210&amp;idx=1&amp;sn=b7aef5b9fbf73362b2b2352f60904cff&amp;chksm=fe0627a5c971aeb30d7da833d7f849568f12e11924e3bcc3fb6a67623ed02c4f47dcbeea5833&amp;scene=21#wechat_redirect)火了以后，有兄弟让我瞅了一眼内部的复盘结果。我从其他渠道确认，要被咔嚓的员工所属部门就是故障部门。

- 
随着故障发酵，该云高管需要去客户现场做解释。恰好有个朋友要接待该云，我戴着口罩混在会议室里，听着一个货真价实的云高管解释了故障现象。

- 
我也有其他朋友是阿里云的用户，他们的反馈是当天没什么感觉，或者快速将业务切走了，或者虽无故障但预防性将业务切离了。

- 
全球各地的运营商都可以查一下故障时段，阿里云的带宽是彻底归零了，还是仍然有较为活跃的业务负载？

阿里云这次故障，确实就是访问密钥业务异常了，异常原因就是写错了访问密钥服务的白名单。网上传来传去的阿里云故障报告，和我从内部拿到的信息基本相同。

![阿里云的P0故障和PaaS产品的特性_image_002.jpg](阿里云的P0故障和PaaS产品的特性_image_002.jpg)

阿里云这次故障，是本文的解释一个硬核问题的关键证据。但很多朋友不懂IT，他们只想知道阿里云有没有故障。我多次尝试都很难把两个事情一并写好，所以我单独发了一篇《[阿里云故障理性分析](http://mp.weixin.qq.com/s?__biz=MzU5NDAzNDEyMA==&amp;mid=2247486234&amp;idx=1&amp;sn=2eee13babd29f26231a24b77a947b2c9&amp;chksm=fe0627bdc971aeab48404f33de02e5e7b5da0884806b6366f244ae187151e8971525d670dd10&amp;scene=21#wechat_redirect)》，今天单独发出本文，解释阿里云如何帮我解释了PaaS云产品的分类问题。&nbsp; &nbsp;&nbsp;

![阿里云的P0故障和PaaS产品的特性_image_004.gif](阿里云的P0故障和PaaS产品的特性_image_004.gif)

**3. 真实的故障叫PaaS鉴权故障**

很多云计算爱好者，受限于个人的技能水平，看到阿里云的公开故障报告以后，只能分析出，“阿里云连真实故障原因都不说，ta都不知道怎么凑热点”。下图是随机选了个小丑，如果云厂商情真意切的又高调发出几万字的罪己诏故障复盘，不过是在对牛弹琴。（下图的东北是那个凑欧盟热度的东北，不是我国东北）。

![阿里云的P0故障和PaaS产品的特性_image_005.jpg](阿里云的P0故障和PaaS产品的特性_image_005.jpg)

我并没有必要给阿里云洗地。因为认证服务故障确实会导致灾难性故障，而淘系APP集体扑街并不代表阿里云用户会集体故障。从我的角度看，这次故障现象的严谨解释方法是：

- 
**PaaS云产品 共同依赖的 业务行为鉴权系统&nbsp;**出现了**逻辑异常**
...
