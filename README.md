# MemBridge
MemBridge（通忆）是一个面向 AI 协同编程的项目记忆中继站。

  它解决的核心问题：在 AI 开发、人类审查、AI 继续开发的循环中，
  信息如何在人、AI、不同 session、不同 agent 之间无损传递。

  工作方式：
  1. AI 开发完 → 增量更新 SQLite 知识库（记录变更、约束、API、符号）
  2. 交接给人类 → 导出审查清单（REVIEW.md）和变更摘要（CHANGELOG.md）
  3. 人类确认后 → 新 AI 读 CONTEXT.md 建立心智模型，按需查询继续开发
  4. 版本完成 → 导出完整项目说明书（PROJECT_SPEC.md）

  三种模式：
  - 开发模式：AI 直接查询 SQLite，精确拉取上下文（500 token 内定位）
  - 审查模式：人类快速查看 AI 做了什么、是否踩红线
  - 交接模式：完整 Markdown 文档，跨 session / agent / 团队传递

  触发场景：
  "项目知识库"、"AI 上下文"、"代码交接"、"版本迭代"、
  "不同 AI 之间怎么共享项目信息"、"让新 AI 快速接手老项目"
