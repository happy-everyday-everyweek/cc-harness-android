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
- 会话指标：turnToolCallCount、turnHadSendUserMessage、had_send_user_message、error_max_turns
- 会话标志：hasHtmlArtifacts、hasWritingDraft、imagineElicitationEnabled、localPlugins

## MCP 配置与权限
- mcpServers 键，旧 record 格式 {"mcpServers":{name:{toolPolicy:{tool:"blocked"}}}} 已废弃
- 新数组格式 [{"serverName":"...","tools":[{"toolName":"...","permission":"..."}]}]，record 与数组互转
- 另有 agents 键；配置上限 262144 字节；配置来源 orgPluginSettings 与 .mcp.json
- 权限值：blocked、ask-session
- toolPolicy 过滤：被 block 的工具调用返回 "Tool 'X' is not permitted" 错误
- 写工具门控：gatedWriteToolCalls

## 插件生态（从主 bundle 提取）
- 插件类型清单：commands、agents、output-styles、skills、workflows、routines、themes、rules、session-env、uploads、mcp-skills
- 插件目录映射：commands/、agents/、hooks/、mcpServers（.mcp.json 根级）
- API 路径（v1）：agents、environments/bridge、deployments、workspaces
- 目录键映射 xE：commands→commands、agents→agents、hooks→hooks、mcpServers→"."（根级）

## Cowork 特有行为
- Cowork 工具：SendUserMessage、PushNotification
- 检测逻辑：tool_use 且 name 为 SendUserMessage 或 PushNotification
- agent 完成后通过 SendUserMessage 向用户报告结果
- sessionType==="agent" 时 turnHadSendUserMessage 强制为 undefined（agent 会话不直接发用户消息）
- 元通知机制：enqueueMetaNotification

## UI 设计系统与字体
- 主字体：Anthropic Sans（--font-sans → --font-anthropic-sans → "anthropic-sans"），fallback ui-sans-serif / -apple-system / system-ui
- 衬线：Anthropic Serif（--font-anthropic-serif），fallback Georgia / Times New Roman
- 等宽：ui-monospace / SFMono-Regular / Menlo / Monaco / Consolas
- 图标字体：Anthropicons（--font-anthropicons，Anthropicons-Variable）
- 设计系统变量：--cds-font-sans、--cds-font-sans-display、--_cds-title-family（CDS，Claude Design System）
- 问候语：首页 Good morning/afternoon/evening 为按时间段生成的 greeting 属性；用户输入问候用正则 /^(hi|hey|hello|good (morning|afternoon|evening))/ 识别

## 字体文件（asar 内，每个渲染窗口 assets/ 下各一份）
- AnthropicSans-Roman-Variable-DDVos-BJ.woff2（Roman 正体，可变字体）
- AnthropicSans-Italic-Variable-CJtkx3-S.woff2（Italic 斜体，可变字体）
- AnthropicSerif-Roman-Variable-2VcCjn5t.woff2
- AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2
- @font-face 声明：font-family:anthropic-sans;src:url(./AnthropicSans-Roman-Variable-DDVos-BJ.woff2)format("woff2");font-weight:300 800;font-style:normal;font-display:swap
- 可变字体覆盖 weight 300-800，Android 侧可直接用这 4 个 woff2 文件
- CDN 静态版（JS 注释提及）：assets.claude.ai/Fonts/AnthropicSerif-Text-{Regular,Medium,Semibold,Bold}{,Italic}-Static.otf；Sans 字体运行时经 #mcp-host-fonts 加载

## 关键字命中（JS 文件数）
code 183、desktop 93、mcp 72、plugin 54、cowork 46、skill 36、connector 26

## 对 cc-harness-android 的启示
- Agent 循环协议以 claude-agent-sdk 为准，TS 实现，Android 侧可按同协议重写
- Skills 生态格式 = SKILL.md + scripts 的 ZIP 包，Android 侧做同格式解析器即可兼容
- MCP 是标准协议，Android 可复用 @modelcontextprotocol/sdk（Node）或 Kotlin MCP 实现
- 权限模型三层：permission_mode（全局）→ toolPolicy（按 MCP 工具，blocked/ask-session）→ 写工具门控
- 插件类型 11 种，Android 侧至少实现 skills + commands + mcp-skills 三种即可覆盖核心生态
- Cowork 与 Code 共享 tool_use/tool_result 协议，Cowork 侧重 SendUserMessage/PushNotification 这类用户交互工具
- UI 字体 Anthropic Sans/Serif 为 Anthropic 自研，Android 侧打包这 4 个 woff2 即可还原官方视觉效果
