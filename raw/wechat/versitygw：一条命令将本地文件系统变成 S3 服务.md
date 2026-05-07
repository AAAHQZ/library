> 原文链接：https://mp.weixin.qq.com/s/2PLFikImGjccdyIi9uisxw

# versitygw：一条命令将本地文件系统变成 S3 服务

在云原生时代，S3 API 已经成为对象存储的事实标准。但很多时候，我们手头只有本地文件系统，或者想统一管理多个存储后端，这时候就需要一个能"翻译"S3 接口的工具。

最近发现一个挺有意思的开源项目：versitygw。它是一个用 Go 写的 S3 网关，核心思路很简单——把 S3 API 请求翻译成后端存储系统支持的调用。简单到一个命令就能把本地目录变 S3，复杂到能生产部署、做负载均衡。

啥是 versitygw？

官方定位是："Versity Gateway, a simple to use tool for seamless inline translation between AWS S3 object commands and storage systems."

翻译成大白话：它就是个中间层，帮你把 S3 协议的请求转成各种后端能懂的操作。这样一来，原本只认 S3 的应用，照样能跑在本地文件系统、Azure Blob，甚至其他 S3 服务上。

项目代码完全开源，地址在 GitHub：versity/versitygw。许可证是 Apache 2.0，商业友好。

能干啥？

看了下 README，几个核心功能点：

1. 本地文件系统秒变 S3

这个是最实用的。一条命令：

./versitygw posix /tmp/mydata

/tmp/mydata 目录立刻就能当 S3 bucket 用，AWS SDK、s3cmd、MinIO mc 这些客户端都能直接连。对开发者来说，本地测试 S3 应用不用再搭 MinIO 了，简单直接。

2. 多后端支持

除了 POSIX（本地文件系统），还支持：
- Azure Blob Storage
- 其他 S3 服务器（可以当代理层）
- ScoutFS（Versity 自家的分布式文件系统）

架构是模块化的，官方说未来会扩展更多后端。

3. 无状态 + 集群部署

网关本身无状态，所有配置和认证都能外部化。这意味着可以横向扩展，前面挂负载均衡，多个实例并行跑，吞吐量线性增长。

4. S3 协议基本完整

项目用了 AWS 官方的 Go SDK v2 来实现兼容，API 兼容性做得比较扎实。支持版本控制、IAM 认证、权限管理等标准特性。

5. WebGUI 管理界面

有个可选的 Web 管理界面，方便查看 bucket、对象，做些基础管理操作。适合不习惯命令行的用户。

技术上咋实现的？

看了下代码结构和文档，几个技术点值得提：
- Web 框架用的是 Fiber：这是 Go 生态里以性能著称的轻量框架，比标准库快不少。
- 底层用 aws-sdk-go-v2：官方 SDK 保证了协议细节的正确性，比如签名算法、错误码这些。
- 测试覆盖挺全：单元测试、集成测试、协议兼容性测试都有，还跑了 govulncheck 做安全扫描。README 里专门强调每个 PR 必须通过完整测试套件才能被 review。代码审查环节也有人在 Loop，不单纯依赖 AI。

这些细节说明项目不是那种"能跑就行"的试验品，是奔着生产去的。

适合啥场景？

想了想几个典型用法：
- 开发测试：本地起个 S3 服务，写代码调试不用云端折腾
- 混合云架构：本地存储 + S3 API，兼顾成本和兼容性
- 存储迁移：把对象存储从 A 平台迁到 B 平台，中间加个网关做适配
- 协议统一：多种存储后端统一暴露 S3 API，应用层不用改

怎么跑起来？

最简单的方式：

Bash
# 下载二进制（以 Linux amd64 为例）
wget https://github.com/versity/versitygw/releases/latest/download/versitygw-linux-amd64
chmod +x versitygw-linux-amd64

# 启动服务，把 /data 目录当 S3
ROOT_ACCESS_KEY=admin ROOT_SECRET_KEY=pass123 \
./versitygw-linux-amd64 --port :9000 posix /data

然后就能用 AWS CLI 访问了：

Bash
aws --endpoint-url http://localhost:9000 s3 ls

如果用 Docker，更简单：

Bash
docker run -d -p 9000:9000 versity/versitygw:latest posix /data

Kubernetes 用户可以直接用官方 Helm Chart 一键部署。

一点思考

versitygw 的定位很清晰：解决 S3 协议和存储后端的解耦问题。

过去，想用 S3 API 只能选云厂商的对象存储，或者自建 MinIO/Ceph。现在有了网关层，存储可以是任何东西——本地磁盘、NAS、云上的 Blob Storage。对应用来说，看到的都是 S3。

对开发者而言，这意味着更灵活的架构选择。比如：
- 数据敏感，不能上云？本地 NAS + versitygw 就行
- 想用 Azure Blob 但应用只认 S3？网关做适配
- 需要缓存层或加速层？网关正好是中间节点

当然，这个思路的代价是网络延迟和额外运维。适不适合，得看具体场景。

小结

versitygw 是个挺实用的开源工具，核心价值就一句话：让任何存储都能说 S3 的语言。

项目成熟度高，文档完善，测试覆盖足，社区活跃度也不错（有讨论区、邮件列表）。如果你正好遇到 S3 兼容性问题，或者想本地快速搭个对象存储服务，值得试试。

项目地址 github.com/versity/versitygw，感兴趣的可以去翻翻代码和文档。
