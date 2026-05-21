---
title: "Dependency Inversion Principle"
date: 2026-05-21
tags:
  - 设计原则
  - 面向对象
  - 依赖倒置
  - SOLID
type: entity
confidence: EXTRACTED
source: 都是_AI_Coding，为什么_Java_体验差了一个量级？五条方法论帮你构建自己的_Harness_环境
---

# Dependency Inversion Principle

> 依赖倒置原则：上层模块不应依赖下层模块，两者都应依赖抽象。在 Java Harness Local Setup 中，这是打通本地运行能力的关键设计原则。

## 概念定义

SOLID 原则中的 "D"，定义：
1. 高层模块不应依赖低层模块，两者都应依赖抽象
2. 抽象不应依赖细节，细节应依赖抽象

## 在 Java Harness Local Setup 中的意义

### 改造前：直接依赖云端实现

```
FilesystemService → OssStorageAdapter (直接调 OSS SDK)
AgentWorkspace → SandboxCommandExecutor (调远程沙箱 API)
```

上层直接依赖具体云端实现，本地没有 OSS/Sandbox 就完全无法运行。

### 改造后：依赖抽象接口

```
FilesystemService → StorageAdapter (接口)
AgentWorkspace → CommandExecutor (接口)

StorageAdapter (接口)
├── OssStorageAdapter (线上)
└── LocalStorageAdapter (本地)

CommandExecutor (接口)
├── SandboxCommandExecutor (线上)
└── LocalCommandExecutor (本地)
```

**关键效果**：上层代码完全不用改，切换运行环境就是换一个接口实现。

## 具体实现示例

### StorageAdapter 接口

```java
public interface StorageAdapter {
    void upload(String key, InputStream data);
    InputStream download(String key);
    boolean exists(String key);
    void delete(String key);
    List<String> list(String prefix);
}
```

### 工厂类根据参数选择实现

```java
public class FileSystemFactory {
    @Autowired
    @Qualifier("localFsBasePath")
    private String localFsBasePath;  // 本地路径，线上为 null
    
    public StorageAdapter createStorageAdapter() {
        if (localFsBasePath != null) {
            return new LocalStorageAdapter(localFsBasePath);
        }
        return new OssStorageAdapter();
    }
}
```

## 依赖倒置 vs 依赖注入

| 概念 | 说明 |
|-----|------|
| 依赖注入 (DI) | 将依赖从外部传入，控制权在外部 |
| 依赖倒置 (DIP) | 依赖抽象而非具体，控制权在高层模块 |

在 Spring 框架中，通过 `@Autowired` + 接口类型字段 + 多实现类的组合，既实现了依赖注入，也实现了依赖倒置。

## 本地 Harness 改造中的 DIP 应用

| 场景 | 改造前 | 改造后（DIP） |
|-----|-------|--------------|
| 文件存储 | 直接用 OssStorageAdapter | 用 StorageAdapter 接口 |
| 命令执行 | 直接调 Sandbox API | 用 CommandExecutor 接口 |
| 数据库 | 直接用 TDDL | 用 DataSource 接口 |
| 配置读取 | 直接调 Diamond SDK | 用 ConfigAdapter 接口 |

## 相关实体

- [[Java-Harness-Local-Setup]] — 本地 Harness 改造实践
- [[StorageAdapter]] — 文件存储抽象接口
- [[LocalCommandExecutor]] — 本地命令执行器
- [[H2-Database]] — H2 数据库
- [[Harness-Engineering]] — Harness 工程体系
