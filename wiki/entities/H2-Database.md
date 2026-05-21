---
title: "H2 Database"
date: 2026-05-21
tags:
  - Java
  - 数据库
  - 本地开发
  - H2
type: entity
confidence: EXTRACTED
source: 都是_AI_Coding，为什么_Java_体验差了一个量级？五条方法论帮你构建自己的_Harness_环境
---

# H2 Database

> H2 是一个纯 Java 编写的嵌入式关系型数据库，在本地 Harness 场景下替代 TDDL+MySQL 集群，实现零外部依赖的本地数据持久化。

## 概念定义

H2 Database 是一个轻量级嵌入式数据库，支持 MySQL 兼容模式。在 Java Harness Local Setup 中用于替代云端 TDDL+MySQL 组合，让 AI 能够在本地运行完整的数据库操作。

## 在 Java Harness 中的应用

### 配置

```properties
# application-local.properties
spring.datasource.url=jdbc:h2:file:./data/local-db;AUTO_SERVER=TRUE;MODE=MySQL
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# 自动执行建表脚本
spring.sql.init.mode=always
spring.sql.init.schema-locations=classpath:schema-h2.sql
```

### 关键特性

| 特性 | 说明 |
|-----|------|
| MODE=MySQL | 兼容 MySQL 语法，基本不用改 SQL |
| AUTO_SERVER=TRUE | 支持多进程并发访问 |
| 文件持久化 | 数据存储在 `./data/local-db.mv.db` |
| 内存模式 | 也可用 `jdbc:h2:mem:db` |

### 与 TDDL+MySQL 的对比

| 维度 | TDDL + MySQL | H2 Database |
|-----|-------------|-------------|
| 部署依赖 | 需要 MySQL 集群 | 零外部依赖 |
| 启动难度 | 需要连接分配中心 | 直接启动 |
| 分布式 ID | TDDL GroupSequence | AtomicLong |
| SQL 兼容性 | MySQL | MySQL 兼容模式 |
| AI 可验证性 | 低（需要连接云数据库） | 高（本地文件即可查看） |

## 替代 TDDL GroupSequence

线上使用 TDDL GroupSequence 生成全局唯一 ID，本地用 `AtomicLong` 替代：

```java
public class LocalSequence implements Sequence {
    private final AtomicLong counter = new AtomicLong(
        System.currentTimeMillis() % 1_000_000
    );
    
    @Override
    public long nextValue() {
        return counter.incrementAndGet();
    }
}
```

## 在本地 Harness 改造中的位置

H2 是本地化改造的核心组件之一：

| 线上依赖 | 本地替代 | 改造方式 |
|---------|---------|---------|
| TDDL + MySQL | H2 | application-local.properties |
| TDDL GroupSequence | AtomicLong | 新增 LocalSequence 类 |

## 相关实体

- [[Java-Harness-Local-Setup]] — 本地 Harness 改造实践
- [[Dependency-Inversion-Principle]] — 依赖倒置原则
- [[Harness-Engineering]] — Harness 工程体系
