# Claude Desktop 逆向发现（v2.9939.4，Linux amd64 .deb）

来源：happy-everyday-everyweek/cc-harness-android 的 GHA 任务 reverse-claude-desktop，下载官方 .deb → dpkg-deb 解包 → 提取 resources/app.asar → npx asar extract → 分析。

## 应用身份
- 包名：com.anthropic.Claude.desktop
- npm 名：@ant/desktop，productName：Claude
- 版本：2.9939.4
- 引擎：Node >= 22.0.0

## 架构
- Electron + Vite + Electron Forge 8.0.0-alpha.10
- 主入口：.vite/build/index.pre.js
- 主进程 bundle 按 chunk 分割（最大 index.chunk-DuaKZOPP.js 约 6.6MB）
- 渲染进程窗口：main_window、buddy_window、quick_window、about_window、find_in_page、local_exec_consent
- 子进程/worker：pty-host、mcp-runtime（directMcpHost.js 约 594KB）、file-index-worker、heavy-work-worker、shell-path-worker、stall-sampler-worker、transcript-search-worker

## 核心依赖（与插件生态直接相关）
- @anthropic-ai/claude-agent-sdk 0.3.284-rc —— Agent 循环核心
- @anthropic-ai/mcpb 2.1.2 —— MCP 打包工具
- @modelcontextprotocol/sdk、client、core —— MCP 官方 SDK
- @ant/cowork-win32-service —— Cowork 的 Windows 服务组件
- @ant/computer-use-mcp、@ant/claude-for-chrome-mcp —— Computer Use 与 Chrome 的 MCP 服务器
- @ant/claude-native、@ant/claude-swift、@ant/imagine-server、@ant/dxt-registry
- playwright-core 1.57（浏览器自动化）、node-pty 1.2.0-beta.14（终端）

## 内置资源（resources/）
- bundled-skills/manifest.json：agentSkillsRef 1790264362，skills 数组每项含 name、description（触发条件描述）、tier（always）
- 内置 6 个 skill：docx、pdf、pdf-reading、pptx、xlsx、frontend-design
- github-mcp/github-mcp-server（原生二进制）
- office365-mcp/office365-mcp-stdio.mjs、pdf.worker.mjs、pdfExtractorProcess.mjs

## .skill 文件格式（关键发现）
.skill 文件本质是 ZIP 包，解压后是 <skill-name>/ 目录：
- SKILL.md：YAML frontmatter（name、description、license）+ Markdown 正文（任务指导）
- LICENSE.txt
- scripts/：Python 脚本（如 accept_changes.py、merge_runs.py、comment.py）、模板 XML、验证器（office/validators/docx.py 等）

SKILL.md frontmatter 示例：
---
name: docx
description: "Use this skill whenever the user wants to ..."
license: Proprietary. LICENSE.txt has complete terms
---

## Agent 协议层（从主 bundle 提取）
- content block 类型：tool_use、tool_result、text、thinking、resource
- tool_use 结构（Zod schema）：{type:"tool_use", name: string, id: string, input: object, _meta?: object}
- tool_result 结构：{type:"tool_result", tool_use_id: string, content}
- 流式 delta 类型：thinking_delta、input_json_delta（tool_use 输入流式）、text
- 消息角色：user（471 次）、assistant（171）、system（130）、tool（15）
- 权限模式 permission_mode：default、auto、bypassPermissions
- 控制工具集：set_model、set_permission_mode、interrupt、stop_task、background_tasks、cancel_async_message、set_max_thinking_tokens
- MCP 配置：mcpServers 键，旧 record 格式 {"mcpServers":{name:{toolPolicy:{tool:"blocked"}}}} 已废弃，新数组格式 [{"serverName":"...","tools":[{"toolName":"...","permission":"..."}]}]；另有 agents 键；配置上限 262144 字节；配置来源 orgPluginSettings 与 .mcp.json
- 工具权限策略 toolPolicy：按工具名 allow/block，写工具受门控（gatedWriteToolCalls）
- Cowork 特有工具：SendUserMessage、PushNotification
- 会话字段：sessionType、isResume、isFirstMessage、parent_tool_use_id

## 关键字命中（JS 文件数）
code 183、desktop 93、mcp 72、plugin 54、cowork 46、skill 36、connector 26

## 对 cc-harness-android 的启示
- Agent 循环协议以 claude-agent-sdk 为准，TS 实现，Android 侧可按同协议重写
- Skills 生态格式 = SKILL.md + scripts 的 ZIP 包，Android 侧做同格式解析器即可兼容
- MCP 是标准协议，Android 可复用 @modelcontextprotocol/sdk（Node）或 Kotlin MCP 实现
- 权限模型三层：permission_mode（全局）→ toolPolicy（按 MCP 工具）→ 写工具门控
- Cowork 与 Code 共享 tool_use/tool_result 协议，Cowork 侧重 SendUserMessage/PushNotification 这类用户交互工具
