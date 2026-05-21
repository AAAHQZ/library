---
tags: [AI Coding, Java, Harness Engineering, 本地开发, 微服务, 依赖倒置]
created: 2026-05-21
updated: 2026-05-21
sources: [2026-05-21-都是_AI_Coding，为什么_Java_体验差了一个量级？五条方法论帮你构建自己的_Harness_环境.md]
---

# 都是 AI Coding，为什么 Java 体验差了一个量级？五条方法论帮你构建自己的 Harness 环境

> Java 微服务项目本地无法运行是 AI Coding 的核心瓶颈。通过依赖倒置、Profile 隔离、CLI 工具化三条改造原则，可以让 AI 在本地自主验证，将迭代周期从 30 分钟压缩到 2 分钟。

## 核心观点

### Java 微服务 AI Coding 的困境

在轻量项目（前端、CLI 工具、Python 脚本）中，AI Coding 的闭环是：
```
编辑代码 → 本地运行 → 测试验证 → AI 读取结果 → 自动修复 → 再次验证 → ...
```
AI 可以自主迭代几十轮，整个过程无人值守。

但 Java 微服务项目这个闭环是断的。AI 写完代码无法在本地验证，必须推预发、等部署、人工检查结果、再反馈给 AI。一轮下来 5-10 分钟，改的只是一个参数注入顺序的问题。

**差距不在 AI 能力，在于如何构建 AI 友好的工程环境——Harness Engineering。**

### 问题的本质

Java 微服务项目重度依赖云端基础设施：HSF（服务调用）、TDDL（数据库路由）、Diamond/Switch（配置中心）、MetaQ（消息队列）。一个 `@Autowired` 背后牵着一整套分布式系统。本地 `mvn spring-boot:run` 直接启动失败。

微服务架构天然不 AI 友好。AI 需要本地可运行的环境来验证输出，但微服务把运行时依赖全部推到了云端。

### 解决思路

改造目标：让 Java 微服务项目在本地能跑起来，让 AI 拥有自主验证能力。

## 三条改造原则

### 1. 依赖倒置，接口先行

上层逻辑依赖抽象接口，不依赖具体实现。云端和本地只是接口的不同实现。

**改造前（直接依赖云端实现）：**
```
FilesystemService → OssStorageAdapter (直接调 OSS SDK)
AgentWorkspace → SandboxCommandExecutor (调远程沙箱 API)
```

**改造后（抽象接口+多实现）：**
```
StorageAdapter (接口)
├── OssStorageAdapter (线上，走 OSS SDK)
└── LocalStorageAdapter (本地，走 java.nio.file)

CommandExecutor (接口)
├── SandboxCommandExecutor (线上，调远程沙箱 API)
└── LocalCommandExecutor (本地，ProcessBuilder + bash -c)
```

上层代码完全不用改，切换运行环境就是换一个接口实现。

### 2. 零侵入，Profile 隔离

本地改造不能让线上代码路径多走一行额外的代码。

**Spring Profile 隔离方案：**
```java
@Configuration
@Profile("local")
public class LocalRepositoryConfig {
    @Bean
    CommandExecutor localCommandExecutor() { ... }
    
    @Bean("sessionSequence")
    Sequence sessionSequence() { return new LocalSequence(); }
}
```

**侵入性分级：**
| 侵入级别 | 改动类型 | 示例 |
|---------|---------|------|
| 零侵入 | 新建文件 | 新接口、新的本地实现类 |
| 极低 | 加一行声明 | 线上实现类加 implements 接口 |
| 低 | 类型上提 | 字段类型从实现类改为接口 |
| 中等 | 新增可选参数 | 构造器加可选的本地路径参数 |

检验标准：删掉所有本地相关代码后，线上行为完全不变。

### 3. 工具 AI 化：CLI 优先

AI Agent 的能力边界 = 它能调用的工具的边界。GUI 对 AI 不可见，CLI 才是 AI 能用的东西。

**工具 AI 化优先级：**
| 优先级 | 工具形态 | AI 可用性 |
|-------|---------|----------|
| 1 | CLI | 直接可用 |
| 2 | MCP Server | 协议适配 |
| 3 | Skill / Tool | 自定义封装 |
| 4 | GUI | 不可用 |

**示例：**
```bash
# 通过 mw-cli 查询 Diamond 运行时配置
mw diamond get --unit online --data-id application.properties --group DEFAULT_GROUP

# 通过 mw-cli 查询 HSF 服务地址
mw hsf address --unit daily --app my-application
```

## 实践案例：从"推预发验证"到"本地闭环"

### 改造前的问题

| 问题 | 说明 |
|-----|------|
| 文件操作 | AgentFs → OSS，本地没有 OSS 服务 |
| 命令执行 | 远程 Sandbox，本地没有沙箱 |
| 文件共享 | Sandbox 通过 OSS Mount 挂载，本地文件系统割裂 |

### 改造方案

一个接口，两套实现，Profile 条件装配。

**本地 `@Profile("local")` 激活时：**
- 文件系统工厂检测到本地参数，自动创建 `LocalStorageAdapter`，文件存到 `/tmp/agentfs/{sessionId}/...`
- Bash 走 `LocalCommandExecutor`，用 `ProcessBuilder` 执行 `bash -c` 命令
- ReadFile/WriteFile 和 Bash 看到同一套文件，闭环打通

### 配套改造清单

| 线上依赖 | 本地替代 | 改动方式 |
|---------|---------|---------|
| OSS 对象存储 | 本地文件系统 | 新增接口 + 本地实现 |
| 远程沙箱 | 本机 bash 执行 | 新增本地实现 |
| TDDL + MySQL | H2 文件数据库 | application-local.properties |
| TDDL GroupSequence | AtomicLong 本地序号 | 新增本地实现 |
| Switch Center | switch-config-local.properties | 脚本从预发拉取 |
| Diamond 配置中心 | application-local.properties | Spring Profile |
| EagleEye / HSF / Sunfire | 排除自动配置 | spring.autoconfigure.exclude |
| OpenTelemetry | 排除观测包 | ComponentScan excludeFilters |

### 改造效果

| 对比项 | 改造前 | 改造后 |
|-------|-------|-------|
| 文件操作验证 | 推预发，通过 OSS 控制台查看 | 本地直接 `ls` 查看 |
| Bash 执行验证 | 推预发，登录沙箱查看 | 本地 Terminal 直接看 |
| AI 自主验证 | 做不到 | ReadFile → 验证 WriteFile 结果 |
| 单次迭代耗时 | 5-10 分钟（含部署等待） | 秒级 |
| AI 自主修复轮数 | 0（每轮都要人工介入） | 平均 3-5 轮后自行收敛 |

## 五条方法论

1. **找到最小可运行子集**：不需要把所有线上能力都搬到本地，只需要核心链路跑通所需的最小依赖集合

2. **替代而非模拟**：H2 是真实关系型数据库，LocalCommandExecutor 执行真实 bash 命令，不是返回 mock 数据

3. **脚本化一切人工操作**：需要人登录管理台、复制配置、点击按钮的操作，都应该有对应脚本

4. **分层隔离，逐层验证**：先让项目能编译，再让它能启动，再让核心接口能调通，再跑端到端测试

5. **让 AI 成为改造的参与者**：一旦 AI 能跑测试、能看到报错，它的效率就上来了，形成正向循环

## Harness Engineering Checklist

- **可运行性**：项目能否在本地通过一条命令启动？
- **依赖替代**：外部中间件是否有本地替代？（H2 替代 MySQL、内存队列替代 MetaQ 等）
- **接口抽象**：外部依赖是否通过接口抽象？能否通过 Profile 切换实现？
- **可测试性**：AI 能否在本地运行测试并读取结果？
- **工具 AI 化**：团队运维工具是否有 CLI 入口？配置管理是否可通过命令行查询？
- **隔离性**：本地改造是否通过 `@Profile` 隔离？删除所有本地相关代码后，线上行为是否完全不变？

## 关键结论

本地 Harness 跑起来后，AI 不光能跑测试，还能理解测试为什么失败：
```
AI 修改代码 → mvn test → 解析 Surefire 报告 → 定位失败用例 → 读取相关源码 → 分析原因 → 修复 → 再次测试 → ...
```

目标：让 AI 在本地能完全自主地跑完"发现问题→定位原因→修复代码→验证修复"整个循环，跟人类开发者拥有一样的本地开发能力。

## 相关实体

- [[Harness-Engineering]] — Harness 工程化的完整实践体系
- [[StorageAdapter]] — 文件存储抽象接口，依赖倒置的具体实现
- [[LocalCommandExecutor]] — 本地命令执行器，替代远程沙箱
- [[H2-Database]] — H2 内存数据库，替代 TDDL+MySQL
- [[Dependency-Inversion-Principle]] — 依赖倒置原则，本地 Harness 改造的理论基础
- [[Java-Harness-Local-Setup]] — Java 项目本地 Harness 改造的实践指南
