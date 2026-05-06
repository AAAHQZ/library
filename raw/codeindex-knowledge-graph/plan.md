## Diagram Plan

**Material**: Codeindex 文章 — 代码语义索引工具知识图谱
**Diagrams**: 1
**Type**: structural (radial topology — concentric ring layout)
**Reader need**: "After seeing this diagram, the reader understands Codeindex has 4 core capabilities, 3 application scenarios, 5 infrastructure components, and how they relate"
**Slug**: codeindex-knowledge-graph
**Output**: /mnt/d/MyLibrary/raw/codeindex-knowledge-graph.svg

---

## Layout

```
Ring 1 — CORE CAPABILITIES (R=138)
  语义化检索 (0°)
  语义化摘要 (90°)
  函数依赖图 (180°)
  增量索引 (270°)

Ring 2 — APPLICATIONS (R=285)
  CodeWiz (30°)
  AI CR Agent (150°)
  CodeWiki (270°)

Ring 3 — INFRASTRUCTURE (R=382)
  Tree-sitter (30°), SDK (102°), OpenAPI (174°), Postgres Age (246°), KuzuDB (318°)

Center — Codeindex 中心节点
Data Model Cluster — Files / Functions / FunctionCalls / Contains (near 函数依赖图)
```

## Connections

| From | To | Type |
|------|----|------|
| 语义化检索 | SDK, OpenAPI | infra |
| 语义化摘要 | Tree-sitter | infra |
| 函数依赖图 | Tree-sitter, KuzuDB, Postgres Age | infra |
| 增量索引 | SDK, OpenAPI | infra |
| 语义化检索 | CodeWiz | app |
| 函数依赖图 | AI CR Agent | app |
| 语义化摘要 | CodeWiki | app |
| 函数依赖图 | Graph Model (Files/Functions/FunctionCalls/Contains) | data |

## Color Palette

- Core: `#60A5FA` (blue)
- App: `#34D399` (teal)
- Infra: `#A78BFA` (purple)
- Data nodes: `#FBBF24` (amber)
- Data edges: `#F87171` (red)
- Background: `#0D1117`
- Accent: `#F59E0B`
