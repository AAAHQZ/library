> 原文链接：https://mp.weixin.qq.com/s/_Hu-dqeJTx2Khh0wORW_7w

研习大师兄 研习大师兄



最近 Vercel Labs 开源了个挺有意思的项目：just-bash。一个用 TypeScript 写的虚拟 bash 环境，跑在内存里，专门给 AI agent 用。

为什么需要这个？

AI 写代码、跑命令，最大的问题是什么？安全。

你敢让 AI 直接在你的服务器上执行 rm -rf 吗？不敢。

那怎么办？沙箱。

传统的沙箱方案：启动一个 Docker 容器，或者租一台虚拟机。安全是安全了，但成本高、启动慢、资源消耗大。

just-bash 走了另一条路：完全在内存里模拟一个 bash 环境。没有真正的容器，没有虚拟机，就是一个 JavaScript 进程，里面跑着虚拟的文件系统和命令行工具。

它能做什么？

支持大部分常用命令：

- 文件操作：ls、cat、cp、mv、mkdir、rm...
- 文本处理：grep、sed、awk、sort、uniq、jq...
- 甚至还能跑 Python 和 JavaScript（需要单独开启）

网络访问默认是关闭的，想开可以开，但要用白名单限制。

安全吗？

项目自己的定位很诚实：这不是 VM 级别的隔离。

它的威胁模型是：假设代码库能防住原型污染等 JS 层面的攻击，那就可以放心用。但如果你要跑不可信的二进制文件，还是老老实实用 Vercel Sandbox 或者 Docker。

谁会用这个？

- 做 AI agent 开发的：让 agent 在沙箱里试代码，不用担心搞坏系统
- 做代码执行平台的：在线 IDE、playground 之类
- 需要批量处理文本数据的：在 Node.js 里直接用 bash 工具链

一个有趣的细节

项目提供了好几种文件系统实现：

- 纯内存模式（默认）
- Overlay 模式：能读真实文件，但修改只留在内存里
- ReadWrite 模式：真的会写磁盘（慎用）
- Mountable 模式：组合多个文件系统

这个设计挺聪明的，不同场景用不同模式。

怎么用？

npm install just-bash

import { Bash } from "just-bash";  
const bash = new Bash();  
await bash.exec('echo "Hello" > greeting.txt');  
const result = await bash.exec("cat greeting.txt");  
console.log(result.stdout); // "Hello\\n"

就这么简单。

值得一试吗？

如果你在做 AI 相关的项目，或者需要一个轻量的命令执行环境，可以看看。

项目还在 beta 阶段，issue 不多，正是提反馈的好时候。

GitHub 地址：github.com/vercel-labs/just-bash


开源技术研习社



使用小程序