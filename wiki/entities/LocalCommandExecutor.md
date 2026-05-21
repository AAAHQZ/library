---
title: "LocalCommandExecutor"
date: 2026-05-21
tags:
  - Java
  - 命令执行
  - 本地开发
  - 沙箱
type: entity
confidence: EXTRACTED
source: 都是_AI_Coding，为什么_Java_体验差了一个量级？五条方法论帮你构建自己的_Harness_环境
---

# LocalCommandExecutor

> 本地命令执行器，使用 ProcessBuilder + bash 替代远程沙箱，让 AI 能够在本地验证 Bash Tool 输出。

## 概念定义

`LocalCommandExecutor` 是命令执行的本地实现，通过 `ProcessBuilder` 在本机执行 `bash -c` 命令。工作目录指向本地文件系统路径，与 `LocalStorageAdapter` 共用同一路径，实现文件操作和命令执行的闭环。

## 接口定义

```java
public interface CommandExecutor {
    CommandResult execute(String command, WorkDir workDir);
}

public class CommandResult {
    private final int exitCode;
    private final String stdout;
    private final String stderr;
    // getters...
}
```

## 实现

```java
public class LocalCommandExecutor implements CommandExecutor {
    
    @Override
    public CommandResult execute(String command, WorkDir workDir) {
        ProcessBuilder pb = new ProcessBuilder("bash", "-c", command);
        pb.directory(new File(workDir.getPath()));
        pb.redirectErrorStream(false);
        
        Process process = pb.start();
        
        String stdout = readStream(process.getInputStream());
        String stderr = readStream(process.getErrorStream());
        
        int exitCode = process.waitFor();
        
        return new CommandResult(exitCode, stdout, stderr);
    }
    
    private String readStream(InputStream is) throws IOException {
        return new String(is.readAllBytes(), StandardCharsets.UTF_8);
    }
}
```

## 与远程沙箱的对比

| 维度 | 远程沙箱 (Sandbox) | 本地命令执行 (LocalCommandExecutor) |
|-----|-------------------|-----------------------------------|
| 执行环境 | 容器云端 | 本机 |
| 文件系统 | OSS Mount | 本地文件系统 |
| 启动延迟 | 需要请求远程服务 | 即时 |
| 调试难度 | 需要登录沙箱控制台 | 直接看本地 terminal |
| AI 可验证性 | 低（需要人工反馈） | 高（本地直接验证） |

## 在 Java Harness Local Setup 中的作用

1. **替代远程沙箱**：不依赖云端容器服务
2. **与 LocalStorageAdapter 共用路径**：ReadFile/WriteFile 和 Bash 看到同一套文件
3. **AI 自主验证**：AI 改完代码后可以直接运行测试，无需人工介入

## 配置注入

```java
public class AgentRunner {
    private final CommandExecutor commandExecutor;
    private final String localFsBasePath;
    
    public AgentRunner(
            @Nullable @Qualifier("localFsBasePath") String localFsBasePath,
            CommandExecutor commandExecutor) {
        this.localFsBasePath = localFsBasePath;
        this.commandExecutor = commandExecutor;
    }
    
    public void run() {
        WorkDir workDir = localFsBasePath != null 
            ? new LocalWorkDir(localFsBasePath) 
            : new RemoteWorkDir();
        // 使用 commandExecutor 执行命令
    }
}
```

## 相关实体

- [[Java-Harness-Local-Setup]] — 本地 Harness 改造实践
- [[StorageAdapter]] — 文件存储抽象（同一层次的概念）
- [[Dependency-Inversion-Principle]] — 依赖倒置原则
- [[Harness-Engineering]] — Harness 工程体系
