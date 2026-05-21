# MemBridge（通忆）

MemBridge（通忆）是一个面向 AI 协同编程的项目记忆中继站。

它解决的核心问题：在 AI 开发、人类审查、AI 继续开发的循环中，信息如何在人、AI、不同 session、不同 agent 之间无损传递。

---

## 为什么需要 MemBridge？

在 AI 辅助编程的日常中，你很可能遇到过这些问题：

- 同一个项目换了 AI 对话窗口，新 AI 对项目结构一无所知，得从头解释
- AI 改了一下午代码，人类审查时不知道它动了哪些文件、有没有踩红线
- 项目越来越大，AI 的上下文窗口装不下，开始"遗忘"关键约束
- 团队里不同成员用不同 AI 工具，信息各自孤岛，无法共建

MemBridge 把项目知识沉淀为结构化的 SQLite 知识库，让 AI 能按需查询精确信息，让人类能快速审查变更，让不同 session、不同 agent 能共享同一份项目记忆。

---

## 三种工作模式

| 模式 | 格式 | 使用者 | 场景 |
|------|------|--------|------|
| **开发模式** | SQLite + CONTEXT.md | AI | 日常开发时按需查询精确信息 |
| **审查模式** | 变更摘要 + 审查清单 | 人类 | 快速查看 AI 做了什么，有没有踩红线 |
| **交接模式** | 完整 Markdown | 人类/其他AI/新session | 项目交接、团队共建、新人上手 |

---

## 核心架构

```
项目目录/
├── .ai/
│   ├── project.ai.db        # SQLite 知识库（开发模式核心）
│   ├── CONTEXT.md           # L1 导航文件（AI 首次读取）
│   ├── build_db.py          # 构建脚本
│   ├── query_db.py          # AI 查询脚本
│   ├── export_md.py         # 导出脚本（SQLite → Markdown）
│   └── init_project.sh      # 快速初始化
└── docs/
    └── PROJECT_SPEC.md      # 交接模式输出
```

---

## 快速开始

### 1. 初始化项目

```bash
# 将脚本复制到你的项目下并首次构建
bash init_project.sh /path/to/your-project
```

这会在你的项目下创建 `.ai/` 目录，扫描源码并生成 SQLite 知识库和 `CONTEXT.md`。

### 2. AI 开发循环

```bash
cd your-project/.ai

# 开发一段代码后，增量更新知识库
python3 build_db.py /path/to/your-project --incremental --author 'AI-session-1'

# AI 按需查询项目信息
python3 query_db.py "用户创建 token 的流程"
python3 query_db.py --sql "SELECT rule FROM constraints WHERE severity='forbidden'"
```

### 3. 交接给人类审查

```bash
# 生成变更摘要
python3 export_md.py your-project.ai.db --diff -o CHANGELOG.md

# 生成人类审查清单
python3 export_md.py your-project.ai.db --review -o REVIEW.md
```

### 4. 版本完成时导出完整文档

```bash
python3 export_md.py your-project.ai.db --output docs/PROJECT_SPEC.md
```

---

## 开发循环工作流

MemBridge 设计为支撑"大循环套小循环"的 AI 协同开发模式：

```
版本迭代（大循环）
├── AI 开发 → build_db.py --incremental → 知识库更新
├── 人类审查 → export_md.py --review → REVIEW.md 审查清单
├── AI 继续开发 → build_db.py --incremental → 知识库更新
├── 人类审查 → export_md.py --review → REVIEW.md 审查清单
├── ... 重复 ...
└── 版本完成 → export_md.py → 完整 PROJECT_SPEC.md
```

---

## 脚本说明

### build_db.py — 构建/更新知识库

```bash
python build_db.py <项目路径> [--output <db路径>] [--incremental] [--force] [--author <作者>]
```

- 自动检测项目语言（Go / Python / JavaScript / TypeScript）
- 扫描源码提取模块、符号、API 路由、数据库表定义
- 收集代码中的决策注释和约束
- **增量模式**只扫描变更文件，保留手工维护的 ADR 和约束

### query_db.py — AI 查询接口

```bash
# 自然语言查询
python query_db.py <db路径> "用户创建 token 的流程"

# SQL 直传（AI 精确查询）
python query_db.py <db路径> --sql "SELECT ..."

# 指定输出格式
python query_db.py <db路径> "列出所有 API" --format json|table|md
```

### export_md.py — 导出 Markdown

```bash
# 完整文档
python export_md.py <db路径> --output docs/PROJECT_SPEC.md

# 变更摘要（diff 模式）
python export_md.py <db路径> --diff -o CHANGELOG.md

# 审查清单
python export_md.py <db路径> --review -o REVIEW.md
```

---

## 适用场景

- 复杂项目的 AI 辅助开发
- 跨 session / agent 的工作交接
- 团队共建项目（不同人负责不同模块）
- 新人（人类或 AI）快速上手现有项目
- 代码审查前的结构了解
- 项目文档自动化维护

---

## 项目背景

MemBridge 诞生于我们对"AI 融合编程"（AI-fused programming）的实践探索。与传统意义上的"AI 辅助写代码"不同，AI 融合编程关注 AI 作为运行时协作方，深度参与开发循环中的决策、实现、审查、迭代全过程。

在这个过程中，我们发现信息传递是最大的瓶颈。MemBridge 就是这个瓶颈的解决方案。

---

## 许可证

MIT License
