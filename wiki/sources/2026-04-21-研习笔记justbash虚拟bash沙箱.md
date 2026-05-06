---
tags: [AI工具, 沙箱, Bash, TypeScript]
created: 2026-04-21
updated: 2026-04-21
sources: [../raw/研习笔记justbash虚拟bash沙箱.md]
---

# 研习笔记：justbash 虚拟 bash 沙箱

> Vercel 开源的轻量级虚拟 bash 环境，为 AI Agent 提供安全的命令执行沙箱

## 核心问题

AI 写代码、跑命令最大的问题是安全——直接让 AI 在服务器上执行 `rm -rf` 是不可接受的。传统方案（Docker/虚拟机）虽然安全，但成本高、启动慢、资源消耗大。

## 解决方案

just-bash 走了另一条路：**完全在内存里模拟一个 bash 环境**。没有真正的容器，没有虚拟机，就是一个 JavaScript 进程，里面跑着虚拟的文件系统和命令行工具。

## 主要能力

**支持大部分常用命令**：
- 文件操作：ls、cat、cp、mv、mkdir、rm
- 文本处理：grep、sed、awk、sort、uniq、jq
- 甚至还能跑 Python 和 JavaScript（需要单独开启）

**网络访问**：默认关闭，可以用白名单开启。

## 多种文件系统模式

- **纯内存模式**（默认）
- **Overlay 模式**：能读真实文件，但修改只留在内存里
- **ReadWrite 模式**：真的会写磁盘（慎用）
- **Mountable 模式**：组合多个文件系统

## 安全边界

项目自己承认：这不是 VM 级别的隔离。它的威胁模型是假设代码库能防住原型污染等 JS 层面的攻击。如果要跑不可信的二进制文件，还是老老实实用 Vercel Sandbox 或 Docker。

## 适用场景

- AI agent 开发：让 agent 在沙箱里试代码，不用担心搞坏系统
- 代码执行平台：在线 IDE、playground
- 批量文本处理：在 Node.js 里直接用 bash 工具链

## 使用示例

```javascript
import { Bash } from "just-bash";
const bash = new Bash();
await bash.exec('echo "Hello" > greeting.txt');
const result = await bash.exec("cat greeting.txt");
console.log(result.stdout); // "Hello\n"
```

## 相关页面

- [[什么是pi下一代Agent架构|sources/2026-05-05-什么是pi下一代Agent架构]]
- [[Agent隔离安全|sources/2026-04-15-Agent隔离安全]]
