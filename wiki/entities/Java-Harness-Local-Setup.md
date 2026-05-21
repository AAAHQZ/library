---
title: "Java Harness Local Setup"
date: 2026-05-21
tags:
  - Java
  - Harness Engineering
  - 本地开发
  - AI Coding
type: entity
confidence: EXTRACTED
source: 都是_AI_Coding，为什么_Java_体验差了一个量级？五条方法论帮你构建自己的_Harness_环境
---

# Java Harness Local Setup

> Java 微服务项目的本地 Harness 改造：通过依赖倒置、Profile 隔离、CLI 工具化三条原则，让 AI 能够在本地自主验证代码，将迭代周期从 30 分钟压缩到 2 分钟。

## 概念定义

Java Harness Local Setup 是将云端依赖的 Java 微服务项目改造为本地可运行环境的工程实践。目标是让 AI Coding Agent 能够在本地完成"写代码→验证→修复"的完整闭环，不再依赖推预发部署。

## 核心问题

Java 微服务项目通常依赖：
- **HSF**：服务调用
- **TDDL**：数据库路由
- **Diamond/Switch**：配置中心
- **MetaQ**：消息队列
- **OSS**：对象存储
- **远程沙箱**：命令执行

这些云端基础设施导致 `mvn spring-boot:run` 本地启动直接失败，AI 无法自主验证。

## 三条改造原则

### 1. 依赖倒置，接口先行

抽抽象接口，上层依赖接口而非具体实现：
```
StorageAdapter (接口)
├── OssStorageAdapter (线上)
└── LocalStorageAdapter (本地，java.nio.file)

CommandExecutor (接口)
├── SandboxCommandExecutor (线上)
└── LocalCommandExecutor (本地，ProcessBuilder)
```

### 2. 零侵入，Profile 隔离

```java
@Configuration
@Profile("local")
public class LocalRepositoryConfig {
    @Bean
    CommandExecutor localCommandExecutor() { ... }
}
```

线上代码路径不变，本地配置通过 Profile 条件装配完全隔离。

### 3. 工具 AI 化：CLI 优先

将内部系统能力通过 CLI 暴露给 AI：
```bash
mw diamond get --unit online --data-id xxx
mw hsf address --unit daily --app xxx
```

## 本地替代方案清单

| 线上依赖 | 本地替代 | 实现方式 |
|---------|---------|---------|
| TDDL + MySQL | H2 文件数据库 | application-local.properties |
| TDDL GroupSequence | AtomicLong | 新增 LocalSequence 类 |
| OSS 对象存储 | 本地文件系统 | LocalStorageAdapter |
| 远程沙箱 | 本机 bash | LocalCommandExecutor |
| Switch Center | 配置文件 | fetch-switch-config.sh 脚本 |
| EagleEye/HSF/Sunfire | 排除自动配置 | spring.autoconfigure.exclude |

## 关键实践

### H2 替代 TDDL

```properties
# application-local.properties
spring.datasource.url=jdbc:h2:file:./data/local-db;AUTO_SERVER=TRUE;MODE=MySQL
spring.datasource.driver-class-name=org.h2.Driver
spring.sql.init.mode=always
```

### LocalCommandExecutor

```java
public class LocalCommandExecutor implements CommandExecutor {
    @Override
    public CommandResult execute(String command, WorkDir workDir) {
        ProcessBuilder pb = new ProcessBuilder("bash", "-c", command);
        pb.directory(new File(workDir.getPath()));
        Process process = pb.start();
        // 捕获输出、错误码
    }
}
```

### 一键启动脚本

```bash
#!/bin/bash
# scripts/start-local.sh
bash scripts/fetch-switch-config.sh  # 不存在则拉取配置
mvn compile -q                           # 编译
java -cp ... LocalApplication             # 启动
```

## 效果度量

| 指标 | 改造前 | 改造后 |
|-----|-------|-------|
| 单次迭代耗时 | 5-10 分钟 | 秒级 |
| AI 自主修复轮数 | 0 | 3-5 轮 |
| 人工介入次数 | 每轮必介入 | 仅最后 review |

## 相关实体

- [[Harness-Engineering]] — 完整的 Harness 工程体系
- [[StorageAdapter]] — 文件存储抽象接口
- [[LocalCommandExecutor]] — 本地命令执行器
- [[H2-Database]] — H2 内存数据库
- [[Dependency-Inversion-Principle]] — 依赖倒置原则
