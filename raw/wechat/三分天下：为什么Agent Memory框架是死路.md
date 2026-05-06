> 原文链接：https://mp.weixin.qq.com/s/bWpka2OzrJFyrbmrecIQUw

# 三分天下：为什么Agent Memory框架是死路

几个月前，老冯写过一篇 [https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491016&idx=1&sn=85714f8f7311ad43319a59a94ca49661&scene=21#wechat_redirect](AI Agent 的操作系统时刻)。那篇文章里我做了个判断：Agent 基础设施的下一场热闹会发生在“记忆”这个方向，围绕着“Agent 该怎么记住东西”会冒出一大批创业公司和开源项目，资本会涌进来，架构图会画得越来越花。

果不其然，Mem0 又融了一轮，MemGPT 改名 Letta 继续融，Zep、Cognee、Hindsight、MemoryScope、Memobase、SuperMemory、Graphiti、LangMem、EverMemOS——一抓一大把。

每家的技术博客上都挂着差不多的架构图：底下一个 episodic 层，中间一个 semantic 层，顶上一个 reflection 或 procedural 层，层与层之间箭头来回穿梭，写着 consolidation、retrieval、forgetting。GitHub star 在涨，arXiv 论文在刷榜，技术大会每场都有一个 Agent Memory 的 track。热闹是真的热闹。

但这次我想泼个冷水： **这条赛道火归火，但也许两年后就不存在了** 。

这不是论证，而是直觉。但这句话得先说清楚，我不是说 Agent 不需要记忆——恰恰相反，记忆是整场 Agent 革命里最重的筹码，是终局壁垒所在。我说的是： **Agent 需要记忆，但不需要今天这种被称作“Memory 框架”的东西** 。

这两句话听起来像同一件事，其实差两个字，差一条赛道的生死。

下面讲讲为什么 —— 当然，这只是老冯的个人观点。
## 一、终局是三分天下

要看清今天的赛道，得先把终局画出来。

这里先交代一句：下面说的“终局”指的是 **严肃的企业级 Agent，以及任何把数据当成核心资产的组织与个人** 。消费端也许另一幅图景——普通用户用一个 ChatGPT、一个 Gemini，厂商顺手把记忆也做了。

老冯对 AI Agent 的终局判断很简单 —— **三分天下** 。一个成熟的 Agent，在终局状态下，大体就是这样一副架子：
```
MODEL_URL=https://api.anthropic.com/v1DB_URL=postgres://user:pass@host:5432/memory
```

![img](三分天下为什么AgentMemory框架是死路_image_001.jpg)

一个 URL 提供智力，一个 URL 提供记忆，中间由 Harness 负责把模型套起来、驾驭它去完成具体任务——加载 Skills、组织 context、调用工具、处理循环。想换模型厂商？换 `MODEL_URL`。想把数据迁到别家？换 `DB_URL`。想本地自建？两个都换成 localhost。三层彻底解耦：智力层由模型厂商提供，记忆层由数据库厂商提供， **驾驭执行的那一层由 Harness 承担** ——Claude Code、Cursor、Devin 这些今天还在爆发式演化的产品，本质上都是 Harness。

这套格局不是架构师一厢情愿的设计品味，它背后有一套很朴素的动力学。

终局里真正的壁垒，不在算力，也不在模型。算力短期关键长期摊平，和“永恒的电力垄断不存在”是一回事。模型中期关键长期平权，开源模型一年一个台阶往上追，GPT-5 和 DeepSeek V4 的差距比 GPT-4 时代已经小了一大截。再过两年，Agent 能用的模型大概率是个丰俭由人的菜市场。真正拿得住的壁垒只有一样—— **私有数据** 。

而严肃的企业用户不会允许自己的核心数据被“一锅端”，也不会允许它和别家的数据无序混在同一个服务商的黑箱里。一旦被锁住，这家服务商对你就有永恒的议价权——过去三十年从数据库到云的采购史，都是这条逻辑写的。于是博弈的终点就是上面那副架子—— **模型厂商管智能，Harness 管驾驭执行，数据库厂商管记忆** ，三方互不吞并、互相制衡。

三分天下的图画完了—— **模型、Harness、数据库，各自独立，各自为王** 。

现在可以回头问那个关键问题：今天市面上这些 Memory 框架，坐在哪一块上？
## 二、Memory 框架是什么？

先对 Memory 框架做一次颗粒度区分，免得一竿子打翻一船人。

今天被塞进“Memory 框架”这个筐里的项目，其实不是同一种东西，大致可以分成四类，每一类的命运不一样。

**第一类，数据库套壳 SDK** 。代表是早期的 Mem0、LangMem、MemoryScope、SuperMemory 这一批。它们的核心能力就是在一个数据库（通常是 PG+pgvector 或 SQLite）上封装一套“extract / store / retrieve / update”的 API，把 episodic 和 semantic 分两张表，加几条重要性打分和时间衰减规则。这一类离“数据库薄薄一层皮”这个描述最贴切， **技术上没有壁垒，产品上有一点用户心智** 。

**第二类，知识图谱 / 时序图谱构建器** 。代表是 Graphiti、Cognee、Hindsight。它们做的事比第一类要重一些——bi-temporal knowledge graph、增量实体消歧、冲突检测与失效、hybrid 检索（语义 + 关键词 + 图遍历）。这类项目的策略层确实有工程含量，不是“几条 SQL”能一笔带过的。但它们的命运是—— **策略层会被模型吸收（模型自己会做实体消歧、冲突判断），存储层会归回数据库（图能力通过 PG 扩展或专用图数据库承载），独立赛道依然不成立** 。

**第三类，Agent Runtime / Agent OS** 。代表是 Letta/MemGPT。它们做的根本不是 Memory 框架该做的事——把 context window 当 RAM、把外部存储当 Disk、让模型自己通过 tool call 在两者之间 swap——这是操作系统意义上的虚拟内存管理。它门槛不低，但它的准确名字是 **Agent Runtime** ，是 Harness 下位的执行引擎层。其实，它应该归到 Runtime / Harness 赛道，不是 Memory 赛道。

第一类会被 Skill + 模型自己写 SQL 直接替代；第二类的策略层会被模型吸收，存储层会归回数据库；
## 三、它没有壁垒

回到第一类（数据库套壳 SDK）——这一类占了市面上的大多数，也是这篇文章主要的打击对象。

把它们拆到最细，干的事情就两件： **替 Agent 设计几张表的 schema，替 Agent 封装几条 SQL** 。

所谓 episodic 和 semantic 分表，是 schema 设计。所谓重要性打分、时间衰减、反思压缩，是写入时跑的几段规则。所谓向量召回加 BM25 加 cross-encoder 重排加 RRF 融合，是查询组合。所有 PR 稿的术语、所有类脑箭头图剥掉，底下就是 **建表和 SQL** 。

建表和 SQL 有壁垒吗？这是程序员第一周就会的东西。那框架凭什么值钱？凭它们“替 Agent 想好了该怎么建表、怎么写查询”。

那“替 Agent 想好”这件事，值多少？

老冯前阵子琢磨过这个——用 PG 加几个扩展加一组存储过程，把 Mem0 干的事从头糊一遍，几天够了。最后没做。为什么没做？ **没意思，没壁垒** 。任何一个懂点 PG 的工程师，周末抽一下午就能写出个基础版 Mem0，功能上九成相似。剩下那一成是 UI、是 SaaS 控制台、是发布节奏、是开发者关系——那是运营和产品的壁垒，不是技术壁垒。

那“教会 Agent 怎么用这套东西”又要多少？ **一个 Skill，一张 markdown** 。

几百个 token 的一段指令，告诉模型“你有一个 PostgreSQL 数据库连接在 `DATABASE_URL`，用户说话时你自己判断哪些事实值得存，每次回答前做向量加全文的混合检索，发现新旧冲突就 UPDATE 旧的”——就这样。Mem0 那套 ADD / UPDATE / DELETE / NOOP 流水线、Cognee 的图谱构建、Graphiti 的时序图——这些“认知架构”能做的事，现在的模型自己写 SQL 就能做到，而且写得比你干净。

Claude 的 Skills 机制已经把这条路走通一半。用户写个 `memory-skill.md`，描述清楚“记忆怎么存、怎么查”，Claude 在需要时自动调用，不需要任何外部 Memory 框架。哪天 Anthropic 或 OpenAI 把一个官方 memory skill 作为最佳实践发出来，这一整批项目从模型侧就被架空了。

**你以为的护城河，其实是一张写着几百字的 markdown** 。生产环境里这张 markdown 背后自然会接上受控工具和固化的数据库 pipeline——但那些位置该归 Harness 的归 Harness，该归数据库的归数据库，依然没有独立 Memory 框架的位置。
## 四、苦涩的教训

上一节是从产业结构上说 Memory 框架没位置。再往深一层，从方法论上也有一把刀—— **The Bitter Lesson** 。

Sutton 2019 年那篇一千多字的博客，讲的事很简单：过去七十年，AI 领域反复上演同一个剧本——研究者把自己对某个领域的精心理解编码进系统，短期看效果不错，长期必输给“让模型自己学”的通用方法。国际象棋的评估函数输给搜索，围棋的棋谱先验输给自我对弈，语音识别的音素建模输给统计方法，CV 的 SIFT 输给深度学习。每一次，靠“领域理解”的路线都输了，赢的是看起来“没有智慧”、只是能吃算力和数据的方法。

这把刀落到 Memory 框架上要小心——它 **不打** 所有系统抽象。操作系统、数据库、编译器都是人类设计的抽象，它们没有被端到端学习吃掉，也不会被吃掉，因为它们提供的是可靠的底层积木，不是替 AI 做决策。Sutton 打的是后者。

Memory 框架的问题在于它 **站在后者那一边** 。它硬编码的那些东西——什么信息值得记、记在哪一层、什么时候触发反思、怎么组合向量和全文——每一项都是“替 Agent 做认知决策”的意见，而不是通用积木（向量存储、全文检索、事务、索引这些真正的积木，早就被数据库提供了）。今天 Agent 需要这些意见，只是因为模型还不够强；等模型强到能自己判断——这件事已经在发生——这些手工认知策略会像 SIFT 遇见 AlexNet 那样，一夜之间变成废铁。

产业结构上它没位置，方法论上它也撑不住。两条线在这里合拢。
## 五、真正的壁垒

那什么 **有** 壁垒？

三分天下那张图里，严格来说只有两个位置上的壁垒是确定的，另一块的壁垒还在成形。

**模型那块** 会打血战。闭源和开源拉锯、价格一年一腰斩、厂商排名每半年洗一次牌。这块有壁垒，但壁垒属于少数几家头部模型厂商，且局势仍在剧烈变动。

**Harness 那块** 还没定型。Claude Code / Codex 现在跑在最前面，但其他的也开始露头：OpenClaw，Hermes；Letta/MemGPT 那支 Agent Runtime 方向如果真能做成也挺有意思。Harness 这块今天刚长出些壁垒，又被 Claude Code 开源给掀翻拉平到一个水平线。

剩下那块—— **数据库** ——是全局里 **确定性最高** 的位置。

确定性来自一个结构性事实： **数据库不在 AI 冲击的范围内** 。

什么东西会被 AI 冲击？价值来自“信息加工”的东西——文案、设计、初级编程、法律文书、客服、PPT。它们的本质是把信息 A 映射到信息 B，而 LLM 做的就是这件事。LLM 有多强，它们被压缩得有多狠。

那什么不在这个前线上？ **物理世界的持久化层** 。数据库干的事情是在真实的磁盘上、通过真实的操作系统和文件系统、对抗真实的断电和崩溃、在多节点间用真实的网络达成共识，让字节在二十年后还能被准确读回来。这件事的本质不是信息加工，是 **物理世界的可靠性保证** 。LLM 再聪明变不出一块磁盘，保证不了 fsync 的语义，也不会代替两阶段提交。

Agent 越强大，它越需要一个可靠的物理世界锚点。Agent 革命不会削弱数据库的价值， **只会放大它** 。

所以三分天下的终局里，模型那块会打到流血，Harness 那块还在摸索， **只有数据库这块地基，三十年前就定了，三十年后还会在** 。
## 六、数据库的尽头是 PG

那具体到记忆层，会是哪个数据库？

这里要分阶段看。

**现阶段** ——对一个跑在本地的个人 Agent、或者单机场景下的轻量 Agent 来说， **SQLite 甚至文件系统也完全够用** 。SQLite 零运维、文件形态、本地跑、原生支持 JSON 和向量扩展，单体 Agent 的记忆需求它完全扛得住。相当多的 Agent 应用在本地持久化上就是直接用 SQLite。这个阶段讲“记忆层需要 PG”是过度工程。

**但往前演化一步——一旦 Agent 需要跨 Agent 协作、跨设备持久化、跨组织可迁移、面对多租户和并发——也就是需要“通用可迁移的记忆层”——局势就会收敛** 。收敛到哪？

**PostgreSQL** 。

原因有三层。

**第一层，事实已经在发生** 。相当一部分认真做 Agent 基础设施的严肃项目都在向 PG 或 PG 兼容后端靠拢——Letta 官方支持 PG + pgvector，Hindsight 明确只支持 PG + pgvector，Tiger Data 直接把产品线命名为 Agentic Postgres。这不是 PG 生态自我证明（Supabase、Neon 这种本来就是 PG 家族的不算），是 **原本站在其他路线上的项目在往 PG 收敛** 。

**第二层，线缆协议（Wire Protocol）是事实标准** 。PG 协议之于通用记忆层，就像 HTTP 之于应用层——够老、够稳、够通用、够开放，没有哪家厂商能拥有它，也没有哪家厂商能替换它。模型在训练语料里见过几百万次 SQL 和 psql，它天然会说这门语言，不需要额外训练。私有协议的数据库在 AI 时代已经输了一半，因为模型不熟它。

**第三层，扩展生态已经把记忆层需要的所有检索原语覆盖了** 。向量有 pgvector，全文有 tsvector 加 GIN，图有 AGE 以及新一代基于 PG 的图扩展，时序有 TimescaleDB，地理有 PostGIS，水平扩展有 Citus。这些都是扩展插进来的，不是重写整个系统——因为 PG 三十年前就做了个关键决定：不替上层预设语义。这是 PG 真正牛逼的地方，三十年后任何新工作负载都能插进来。

所以这条演化路径是清楚的—— **在“通用可迁移的记忆层”这件事上，除了 PG 协议没别的选** 。图数据库、对象存储、端侧 SQLite、专用搜索系统——它们会在各自的专用场景里继续存在，这不冲突。但在“通用 Agent 记忆”这个场景里，PG 就是那个终局。

[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491654&idx=1&sn=410bf56b4013e8ee6cdad065e6e8874b&scene=21#wechat_redirect](把 Agent 的状态放进数据库)

SQLite 和 PG 在架构纪律上是一族人——都是通用持久化层、都不预设上层语义、都有几十年的可靠性积累、都不在 AI 冲击的前线上、都有灵活的扩展极致。SQLite 是端侧和本地场景的 PG，PG 是服务端和协作场景的 SQLite。它们是同一条路上的两个 size。

真正被三分天下分食掉的，是那种“介于数据库和应用之间的中间件”——也就是今天的 Memory 框架。

回到三分天下这张图。模型层在流动，Harness 层在成形，记忆层在沉淀。三块地盘各自为王，没有哪一块是今天“Memory 框架”的位置。它们不是某一方的对手——它们是过渡态下代管了三方工作的中间商，而真空一旦被填满，中间商就无处可去。

十年后再回望 2026 年这场 Memory 框架喧哗，会看到一件很平淡的事——那些号称“给 Agent 设计认知”的框架，最后真正留下来的代码，是它们最朴素的那一部分： **把数据老老实实塞进 PostgreSQL 的那几行 SQL** 。

其他的，都会被端到端学习吃掉。
*数据库老司机**点一个关注 ⭐️，精彩不迷路*

***对 PostgreSQL， Pigsty，下云，AI 感兴趣的朋友***

***欢迎加入 PGSQL x Pigsty 交流群  QQ 619377403***

******

******[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491904&idx=1&sn=b848329e7d72bdf01247049c0c2793e6&scene=21#wechat_redirect](赛博经藏：当宗教智慧与 AI Agent 碰撞)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491875&idx=1&sn=34e87d9668bb59dbf1bd5f53ab16fc24&scene=21#wechat_redirect](一天烧几亿 Token，然后呢？)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491866&idx=1&sn=65c7ff42c4a295dcb835ac770aa1f089&scene=21#wechat_redirect](AI 时代，PostgreSQL 凭什么赢了？)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491844&idx=1&sn=0237bd818c234299a31d3bbfe91ee8f6&scene=21#wechat_redirect](AGI已经来了，但你有船票吗？)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491832&idx=1&sn=cc104442f9d040ba453ada9220966d65&scene=21#wechat_redirect](专家能被蒸馏吗？)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491807&idx=1&sn=c216699fd4af89ca0794195fb3aa3ea4&scene=21#wechat_redirect](你的 SaaS，别人的开关)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491792&idx=1&sn=f47f070a9762ecf541d1bba8d4fd60fa&scene=21#wechat_redirect](大模型有情绪：Anthropic 首次在 Claude 内部发现可干预的“情绪向量”)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491748&idx=1&sn=f53619dafa5b57c763704b8f7c9f954f&scene=21#wechat_redirect](当 AI 获得瘫痪一座城市交通的权力)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491717&idx=1&sn=ff940de0d8f28f4204c58e0e75015e06&scene=21#wechat_redirect](智能的本质：最小自由能原理)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491661&idx=1&sn=a4207fd8ae4afa2fbe600a6b83768393&scene=21#wechat_redirect](从 KV Cache 到 AI 内存系统：大模型推理架构的演进)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491654&idx=1&sn=410bf56b4013e8ee6cdad065e6e8874b&scene=21#wechat_redirect](把 Agent 的状态放进数据库)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247489293&idx=1&sn=4d538e8ea2808e61ccccfca763473834&scene=21#wechat_redirect](PGFS：将数据库作为文件系统)

**[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247490783&idx=1&sn=1abcd036645a6fd097c3c142336c012f&scene=21#wechat_redirect](Agent的数字躯体：一场不在内核的数据库革命)**

**[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247489865&idx=1&sn=e09b423e8b64094df85b6ea6a0acede2&scene=21#wechat_redirect](别争了，AI时代数据库已经尘埃落定)**

**[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491644&idx=1&sn=33c01962afee9383a7c021bad765dc7f&scene=21#wechat_redirect](Pigsty出海记：百万流量，“颗粒无收”)**

**[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491632&idx=1&sn=f638178b1cb3c20e6c714059428b7352&scene=21#wechat_redirect](Vibe Coding 应当翻译为“写意编程”)**[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491603&idx=1&sn=76554514c39309bcad66a259d190ee6b&scene=21#wechat_redirect](Palantir专利一句话，本体论就是数据建模)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491243&idx=1&sn=bfaebd03b63eeaa21c6613b6a1d4fa89&scene=21#wechat_redirect](Palantir 的 “本体论骗局)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491559&idx=1&sn=5990fff59885042205cbbc194e0f03c7&scene=21#wechat_redirect](腾讯云替龙虾之父“减负” 180GB)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491548&idx=1&sn=c7a838e46986f557e040e4a464d3fa71&scene=21#wechat_redirect](InsForge：为 Vibe Coding 而生的 Supabase)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491537&idx=1&sn=3432bc7106a20e37a3a73ce28ba43084&scene=21#wechat_redirect](AI 说：我有智慧，但没有人生)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491500&idx=1&sn=b9ff3fd1957ce8bd442e5b182c7c06d5&scene=21#wechat_redirect](OpenClaw小龙虾炒作：生产力革命上的浮沫)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247490846&idx=1&sn=1083f1f55a22ac88e3aab24e0907b102&scene=21#wechat_redirect](Claude Code 免翻上手指南)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247490909&idx=1&sn=d221083bb51d2f950640b3924d83c35c&scene=21#wechat_redirect](如何办外卡买 GPT/Claude)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491486&idx=1&sn=02e579c7a7e8632f5390991f51305e29&scene=21#wechat_redirect](464个扩展开箱即用：新版 PG 扩展目录发布)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491459&idx=1&sn=598bdbd525ac130be357159fe7648957&scene=21#wechat_redirect](Pigsty v4.2.1 紧急发布，PG 13 正式下线)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491396&idx=1&sn=db1796eb86174ab3b1eb8c7f37220def&scene=21#wechat_redirect](花一天翻完了 PG 生态三大组件的文档)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491383&idx=1&sn=0015823f67ce521774a8b2f285e4d301&scene=21#wechat_redirect](Pigsty v4.2：12 款PG内核任君选择)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491383&idx=2&sn=aef67d9c4b2f95799cf474059ac9214c&scene=21#wechat_redirect](一觉醒来，又上HN头条了)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491348&idx=1&sn=19e4d076c2013b354fca86af83b404b3&scene=21#wechat_redirect](特朗普下令全面封杀人工智能公司 Anthropic)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491335&idx=1&sn=a02aa5c6657d7cf9562a87d4937addde&scene=21#wechat_redirect](用AI当由头裁了4000人，但程序员的需求还涨了)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491297&idx=1&sn=110ea033f0a1148f4b43d5048b00baca&scene=21#wechat_redirect](一个人春节，能用 AI 干多少事？)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491297&idx=2&sn=fc873021fb9a6b172333d538e882ad63&scene=21#wechat_redirect](2028 全球智能危机)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491269&idx=1&sn=43bf9f271195373288c12d1d4736149f&scene=21#wechat_redirect](两小时复活 MinIO，然后网站炸了)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491243&idx=1&sn=bfaebd03b63eeaa21c6613b6a1d4fa89&scene=21#wechat_redirect](Palantir 的 “本体论骗局”)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491230&idx=1&sn=bbd991ccf75025986435cdc27fa3f8a5&scene=21#wechat_redirect](重新设计数据密集型应用)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491217&idx=1&sn=abdfd146fea80e3685b9055174dfb884&scene=21#wechat_redirect](用麦克卢汉的手术刀解剖AI)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491208&idx=1&sn=4d36ee074fe753668f074df788416da6&scene=21#wechat_redirect](新年，聊聊AI将带来的变化)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491187&idx=1&sn=005af2d12f6f4d258040efbe4faf08bb&scene=21#wechat_redirect](MinIO 已死，MinIO 复生)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491173&idx=1&sn=6894fc450a6f8de209e8f4dbc70d7833&scene=21#wechat_redirect](Pigsty v4.1 x PG 18.2 天下武功，唯快不破)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491149&idx=1&sn=904e0e87e8707ef01ffea17425febe25&scene=21#wechat_redirect](Agent 的护城河：强龙不压地头蛇)[https://mp.weixin.qq.com/s?__biz=MzU5ODAyNTM5Ng==&mid=2247491134&idx=1&sn=abc9089d16faae9215d653aa3633e0d9&scene=21#wechat_redirect](AI撕掉了软件的皮)
