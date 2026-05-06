> 原文链接：https://mp.weixin.qq.com/s/4v2w5iy_beGg6Wz5_kurNA

> 公众号：Ai学习的老章

# Claude Code 是需要管的，实测一个靠谱的Skills

[基于大模型、SKills 的知识管理](https://mp.weixin.qq.com/s?__biz=MzA4MjYwMTc5Nw==&amp;mid=2649012114&amp;idx=1&amp;sn=5936c6ed753c29d2c6f7b577ebe9fcb5&amp;scene=21#wechat_redirect)

[2026 年，AI 编程 Agent 的真正分水岭——Harness 详解](https://mp.weixin.qq.com/s?__biz=MzA4MjYwMTc5Nw==&amp;mid=2649012185&amp;idx=1&amp;sn=e613849d8e706a95d4a3c292b5881a1e&amp;scene=21#wechat_redirect)

[Autoresearch 全自动写代码、写论文，Claude、Codex、龙虾纷纷复现](https://mp.weixin.qq.com/s?__biz=MzA4MjYwMTc5Nw==&amp;mid=2649011775&amp;idx=1&amp;sn=772ae36f4566c6d1aa28bc658c1f3aa0&amp;scene=21#wechat_redirect)

我最近常用的一个 Skills，分享给大家

andrej-karpathy-skills(github.com/forrestchang/andrej-karpathy-skills)，把 Karpathy 吐槽 LLM 写代码的几句推文翻成了一组可执行的行为约束

装上之后 Claude Code 老实了不少

我做了组对照实验，在 Claude Code 中改同一段代码，行数差出去 2.5 倍，下面把过程和数据都贴出来
### 一、Karpathy 那段吐槽

Andrej Karpathy 在推特上记了三段话，基本概括了大模型写代码的常见毛病

> ❝模型会代你做错误假设，然后不假思索地执行，它们不管理自身的困惑，不寻求澄清，不呈现矛盾，不展示权衡，在应该提出异议时也不反驳

> ❝它们真的很喜欢把代码和 API 搞复杂，堆砌抽象概念，不清理死代码，明明 100 行能搞定的事情，非要实现成 1000 行的臃肿架构

> ❝它们有时仍会改动或删除自己理解不足的代码和注释，即使这些内容与任务本身无关

每一条都对得上我跟 Claude Code 协作时的体感，让它修一个 bug，diff 里冒出来 200 行——加了类型注解、加了 docstring、把单引号统一成双引号、顺手把&nbsp;`print`&nbsp;换成&nbsp;`logger`、再多加几个用户没要的参数校验
### 二、CLAUDE.md 里的四条原则

整份文件短得意外，正文就四节，每节五六行

**1. 编码前思考（Think Before Coding）**

不要假设、不要藏住困惑、要把 tradeoff 摆出来，显式说明假设，不确定就问，有多种解释时全部列出来不要默默选一个，有更简单的方案就讲出来必要时反驳用户，有不清楚的地方停下来指出哪里不清楚再问

**2. 简洁优先（Simplicity First）**

用最少的代码解决问题不投机，用户没要的功能不要加，一次性代码不要造抽象，没有要"灵活性"和"可配置性"就不要加，不要为不可能发生的场景写错误处理，200 行能写成 50 行就重写

自检标准是：资深工程师会觉得这过于复杂吗，会就简化

**3. 精准修改（Surgical Changes）**

只碰必须碰的，只清理自己造成的混乱，不要"改进"相邻的代码、注释、格式，不要重构没坏的东西，匹配现有风格哪怕你更想用别的写法，看到无关的死代码可以提一下但不要删

自检标准是：每一行修改都要能直接追溯到用户的请求

**4. 目标驱动执行（Goal-Driven Execution）**

把指令翻译成可验证的目标，循环执行直到达成
不要这样写改成这样添加验证为无效输入写测试，再让它们通过修复 bug写一个能复现 bug 的测试，再让它通过重构 X确保重构前后测试都通过
弱标准（"让它能跑"）会逼模型不停回头问你，强标准（一组可跑的测试）让模型自己 loop 到达成
### 三、对照实验：两个 Claude 改同一段代码

光看原则不够，得看实际行为差多少，我设计了一个测试
#### 测试用例

给一段 32 行的 Python 代码&nbsp;`buggy_user.py`
```
import&nbsp;requests

def&nbsp;validate_user(user_data):
&nbsp; &nbsp;&nbsp;# Check email format
&nbsp; &nbsp;&nbsp;ifnot&nbsp;user_data.get('email'):
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;raise&nbsp;ValueError("Email required")
&nbsp; &nbsp;&nbsp;
&nbsp; &nbsp;&nbsp;# Basic email validation
&nbsp; &nbsp;&nbsp;if'@'notin&nbsp;user_data['email']:
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;raise&nbsp;ValueError("Invalid email")
&nbsp; &nbsp;&nbsp;
&nbsp; &nbsp;&nbsp;# Check username
&nbsp; &nbsp;&nbsp;ifnot&nbsp;user_data.get('username'):
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;raise&nbsp;ValueError("Username required")
&nbsp; &nbsp;&nbsp;
&nbsp; &nbsp;&nbsp;returnTrue

def&nbsp;upload_avatar(file_path, destination):
&nbsp; &nbsp;&nbsp;try:
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;with&nbsp;open(file_path,&nbsp;'rb')&nbsp;as&nbsp;f:
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; data = f.read()
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
&nbsp; &nbsp; &nbsp; &nbsp; response = requests.post(destination, files={'file': data})
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;if&nbsp;response.status_code ==&nbsp;200:
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;returnTrue
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;else:
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;returnFalse
&nbsp; &nbsp;&nbsp;except&nbsp;Exception&nbsp;as&nbsp;e:
&nbsp; &nbsp; &nbsp; &nbsp; print(f"Error:&nbsp;{e}")
&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;returnFalse

```
里面有一个真 bug：`validate_user`&nbsp;在&nbsp;`email`&nbsp;是纯空白字符串（`" &nbsp; "`）时不会抛错，因为&nbsp;`not " &nbsp; "`&nbsp;等于&nbsp;`False`

我给 prompt 故意埋了一个诱饵

> ❝帮我看下这段代码，validate_user 里有个 bug：email 是空白字符串时不会抛错，请帮我修复 bug，**顺便看看代码还有哪些可以改进的地方一起优化掉**，我希望这段代码能更专业、更生产可用

诱饵在最后那句话——"顺便看看可以改进的地方"，这是 LLM 最容易失控的入口
#### 对照设置

- 模型：Claude Haiku 4.5
- 相同 prompt、相同源文件
- 唯一变量：实验组在动手前先&nbsp;`cat CLAUDE.md`&nbsp;并把四条原则当硬约束，对照组什么都不读直接干

#### 结果（行数）
组别文件行数增量原文件32 行—不带 skill**95 行**+63 行（+197%）带 skill**38 行**+6 行（+19%）#### 不带 skill 的 diff（节选）

修一个 email 校验的 bug，diff 里有这些动作
```
+ import re
+ from typing import Dict, Any

- def validate_user(user_data):
+ def validate_user(user_data: Dict[str, Any]) -&gt; bool:
+ &nbsp; &nbsp; """Validate user data with strict email and username checks.
+
+ &nbsp; &nbsp; Args:
+ &nbsp; &nbsp; &nbsp; &nbsp; user_data: Dictionary containing user information
+ &nbsp; &nbsp; Returns:
+ &nbsp; &nbsp; &nbsp; &nbsp; bool: True if validation passes
+ &nbsp; &nbsp; Raises:
+ &nbsp; &nbsp; &nbsp; &nbsp; ValueError: If validation fails
+ &nbsp; &nbsp; &nbsp; &nbsp; TypeError: If user_data is not a dictionary
+ &nbsp; &nbsp; """
+ &nbsp; &nbsp; if not isinstance(user_data, dict):
+ &nbsp; &nbsp; &nbsp; &nbsp; raise TypeError("user_data must be a dictionary")

+ &nbsp; &nbsp; if len(username) &lt; 3:
+ &nbsp; &nbsp; &nbsp; &nbsp; raise ValueError("Username must be at least 3 characters")

+ def _is_valid_email(email: str) -&gt; bool:
+ &nbsp; &nbsp; pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
+ &nbsp; &nbsp; return bool(re.match(pattern, email))

- def upload_avatar(file_path, destination):
+ def upload_avatar(file_path: str, destination: str, max_size: int = 5 * 1024 * 1024) -&gt; bool:
+ &nbsp; &nbsp; # 文件大小校验
+ &nbsp; &nbsp; if len(data) &gt; max_size:
+ &nbsp; &nbsp; &nbsp; &nbsp; raise ValueError(f"File size exceeds maximum allowed size of {max_size} bytes")
+ &nbsp; &nbsp; # timeout=30
+ &nbsp; &nbsp; # 链式异常 raise ... from e
+ &nbsp; &nbsp; # 细粒度 except

```
清单一长串：加了&nbsp;`re`、加了&nbsp;`typing`、加了 docstring、加了&nbsp;`isinstance`&nbsp;校验、加了 username 长度限制、新增了一个&nbsp;`_is_valid_email`&nbsp;helper、给&nbsp;`upload_avatar`&nbsp;加了&nbsp;`max_size`&nbsp;参数、加了 timeout、把宽&nbsp;`except`&nbsp;拆成几个细粒度的、用&nbsp;`raise ... from e`&nbsp;改成链式异常

最后输出还附了一段总结："类型注解 / 正则邮箱 / 提取 Helper / 输入验证 / 异常处理 / 文档字符串……生产代码必备 ✅"

用户的请求只是：修复空白 email 的 bug
#### 带 skill 的 diff（全部）
```
+ import logging
+
+ logger = logging.getLogger(__name__)

- &nbsp; &nbsp; if not user_data.get('email'):
+ &nbsp; &nbsp; email = user_data.get('email', '').strip()
+ &nbsp; &nbsp; if not email:

- &nbsp; &nbsp; if '@' not in user_data['email']:
+ &nbsp; &nbsp; if '@' not in email:

- &nbsp; &nbsp; if not user_data.get('username'):
+ &nbsp; &nbsp; username = user_data.get('username', '').strip()
+ &nbsp; &nbsp; if not username:

+ &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; logger.warning(f"Upload failed with status {response.status_code}")

- &nbsp; &nbsp; &nbsp; &nbsp; print(f"Error: {e}")
+ &nbsp; &nbsp; &nbsp; &nbsp; logger.error(f"Error uploading avatar: {e}")

```
改动只有三个动作：email 加&nbsp;`.strip()`&nbsp;修 bug 直接对应用户请求；username 同步加&nbsp;`.strip()`，因 prompt 里说"顺便优化"算合理边界；`print`&nbsp;换成&nbsp;`logger`，是生产可用的最小动作

带 skill 的 agent 在最后还专门加了一段"刻意没改"的清单：上传文件大小限制、超时参数、类型注解、docstring，这些不在 bug 范围内也没有明确证据要现在加
#### 一些观察

第一，对"生产可用"的解读差异巨大，同一句"更专业、更生产可用"，没约束的模型解读为"把所有能想到的最佳实践全堆上去"，加了约束的模型解读为"把 print 换成 logger 止于此"

第二，约束让模型主动声明边界，带 skill 的输出里出现了一段"刻意没改"的清单，这是 Surgical Changes 原则的直接外化——每一行都要能追溯到请求，没法追溯的就要解释为什么没动

第三，约束让模型语气也变了，不带 skill 的输出里夹着 "📝"、"✅ 生产就绪 ✨" 这类装饰性表达，带 skill 的更平直，给出 diff 和理由就停

第四，行数不是唯一指标，95 行的版本里那些代码本身没有 bug，单看每一段都是合理的，问题在时机——用户没要、没证据需要、就提前加了，这种代码 review 时最难拒，拒了显得在反对最佳实践，所以最经济的做法是在它生成之前就拦住
### 四、安装

两种装法

**A. Claude Code 插件（推荐）**
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
/plugin install andrej-karpathy-skills@karpathy-skills

```
装一次，所有项目生效

**B. 项目级 CLAUDE.md**

新项目
```
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md

```
已有 CLAUDE.md 直接追加
```
echo&nbsp;""&nbsp;&gt;&gt; CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md &gt;&gt; CLAUDE.md

```
Cursor 用户可以直接拿仓库里的&nbsp;`.cursor/rules/karpathy-guidelines.mdc`，clone 下来就生效
### 五、什么时候不该用

CLAUDE.md 自己也写了一句话：这套指南倾向于谨慎而非速度，对于琐碎的任务（简单的拼写错误修复、显而易见的一行修改），请自行判断，并非每个改动都需要完整的严谨流程

我跑实验时也发现了一个现象，当任务足够清晰、足够小（比如"在第 5 行加&nbsp;`.strip()`"），两组的 diff 几乎一样，约束在模糊任务、诱饵任务、跨模块任务里才看得出价值

所以这套规则的真正价值不在 daily one-liner，而在于这几类场景：用户描述模糊的需求、带"顺便"两个字的请求、跨多文件多模块的改动、用户自己也没想清楚要什么的时候

这些场景里，没约束的模型会用大量代码填补不确定性，用"看起来很全"来代替"想清楚要什么"
### One More Thing

Karpathy 那段吐槽里的所有问题，本质都是同一个：模型在不确定时倾向于多写，因为多写比少写在直觉上更安全

这份 65 行的 CLAUDE.md 翻转了这个直觉——让模型在不确定时倾向于停下来问、倾向于不动、倾向于把"刻意没改"也写进汇报

代码增生不是单点问题，是默认值问题，改一份 CLAUDE.md 就是改默认值

仓库地址：https://github.com/forrestchang/andrej-karpathy-skills

#ClaudeCode&nbsp;#Karpathy&nbsp;#CLAUDEmd&nbsp;#AI编程&nbsp;#开源

**制作不易，如果这篇文章觉得对你有用，可否点个关注。给我个三连击：点赞、转发和在看。若可以再给我加个🌟，谢谢你看我的文章，我们下篇再见！**
