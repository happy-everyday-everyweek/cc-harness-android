# cc-harness-android

Android-first cross-platform agent harness, compatible with the Claude Code / Claude Cowork plugin ecosystem (MCP, Skills, Plugins, Connectors).

## 目标
- 兼容 Claude Code 与 Claude Cowork 的插件生态
- 跨平台，当前聚焦 Android
- 逆向分析产物在 docs/，下载、解包、分析任务全部在 GitHub Actions 内执行

## 仓库结构
- .github/workflows/ — 逆向任务（GHA 执行）
- report/ — GHA 生成的分析报告
- docs/ — 设计文档
