---
title: "StorageAdapter"
date: 2026-05-21
tags:
  - Java
  - 接口设计
  - 依赖倒置
  - 文件存储
type: entity
confidence: EXTRACTED
source: 都是_AI_Coding，为什么_Java_体验差了一个量级？五条方法论帮你构建自己的_Harness_环境
---

# StorageAdapter

> 文件存储操作的抽象接口，通过依赖倒置实现本地/云端存储切换。

## 概念定义

`StorageAdapter` 是文件系统操作的抽象接口，定义上传、下载、判断存在、删除、列举等统一行为。其核心作用是将上层代码与具体存储实现解耦。

## 接口设计

```java
public interface StorageAdapter {
    void upload(String key, InputStream data);
    InputStream download(String key);
    boolean exists(String key);
    void delete(String key);
    List<String> list(String prefix);
}
```

## 实现类

### OssStorageAdapter（线上）

```java
public class OssStorageAdapter implements StorageAdapter {
    private final OssClient ossClient;
    
    @Override
    public void upload(String key, InputStream data) {
        ossClient.putObject(bucket, key, data);
    }
    // 其他方法调用 OSS SDK
}
```

### LocalStorageAdapter（本地）

```java
public class LocalStorageAdapter implements StorageAdapter {
    private final String basePath;  // 如 /tmp/agentfs
    
    @Override
    public void upload(String key, InputStream data) {
        Path path = Paths.get(basePath, key);
        Files.createDirectories(path.getParent());
        Files.copy(data, path);
    }
    
    @Override
    public InputStream download(String key) {
        return Files.newInputStream(Paths.get(basePath, key));
    }
    // 其他方法使用 java.nio.file
}
```

## 工厂类选择实现

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

## 在 Java Harness Local Setup 中的作用

StorageAdapter 是依赖倒置的具体实现：

1. **上层逻辑不依赖具体类**：只依赖 `StorageAdapter` 接口
2. **运行时决定实现**：工厂类根据参数自动选择本地或云端实现
3. **本地闭环打通**：ReadFile/WriteFile 和 Bash 操作同一套本地文件系统

## 相关实体

- [[Java-Harness-Local-Setup]] — 本地 Harness 改造实践
- [[LocalCommandExecutor]] — 本地命令执行器（同一层次的概念）
- [[Dependency-Inversion-Principle]] — 依赖倒置原则
- [[Harness-Engineering]] — Harness 工程体系
