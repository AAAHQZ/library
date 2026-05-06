---
tags: [versitygw, S3, 云原生, 对象存储, Go, 开源]
created: 2026-05-05
updated: 2026-05-05
sources: [../raw/wechat/versitygw：一条命令将本地文件系统变成 S3 服务.md]
---

# versitygw：一条命令将本地文件系统变成 S3 服务

> 一个用 Go 写的 S3 网关，把 S3 API 请求翻译成各种后端存储系统支持的操作。让任何存储都能说 S3 的语言。

关于 [[S3对象存储]] 的核心特征和生态影响力，请参阅实体页面。

## 核心功能

1. **本地文件系统秒变 S3**：一条命令 `./versitygw posix /tmp/mydata` 即可
2. **多后端支持**：POSIX、Azure Blob Storage、其他 S3 服务器、ScoutFS
3. **无状态 + 集群部署**：可横向扩展，前面挂负载均衡
4. **S3 协议完整**：使用 AWS 官方 Go SDK v2，支持版本控制、IAM 认证、权限管理
5. **WebGUI 管理界面**：可选的 Web 管理界面

## 典型使用场景

- **开发测试**：本地起 S3 服务，写代码调试不用云端
- **混合云架构**：本地存储 + S3 API，兼顾成本和兼容性
- **存储迁移**：对象存储跨平台迁移，中间加网关做适配
- **协议统一**：多种存储后端统一暴露 S3 API

## 技术特点

- **Web 框架**：Go Fiber（轻量高性能）
- **底层 SDK**：aws-sdk-go-v2（官方 SDK 保证协议正确性）
- **测试覆盖**：单元测试、集成测试、协议兼容性测试，govulncheck 安全扫描

## 快速启动

```bash
# 下载二进制
wget https://github.com/versity/versitygw/releases/latest/download/versitygw-linux-amd64
chmod +x versitygw-linux-amd64

# 启动服务
ROOT_ACCESS_KEY=admin ROOT_SECRET_KEY=pass123 \
  ./versitygw-linux-amd64 --port :9000 posix /data

# 使用 AWS CLI 访问
aws --endpoint-url http://localhost:9000 s3 ls
```

## Docker 方式

```bash
docker run -d -p 9000:9000 versity/versitygw:latest posix /data
```

## 项目信息

- **GitHub**：github.com/versity/versitygw
- **许可证**：Apache 2.0（商业友好）
- **官方定位**：Seamless inline translation between AWS S3 object commands and storage systems
