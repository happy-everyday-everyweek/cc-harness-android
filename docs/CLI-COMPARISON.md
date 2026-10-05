# 四款 AI 编码 CLI 的 Harness 功能对比（基于源码）

方法：直接读 GitHub 源码。Codex 读 openai/codex 的 codex-rs（Rust，100+ crate）；Grok Build 读 xai-org/grok-build（Rust，crates/build+codegen+common）；Claude Code 闭源，功能来自对 Claude Desktop 官方 deb 的逆向（asar 主 bundle grep）；Z Code 在 GitHub 未找到公开仓库，harness 代码级信息缺失，只保留定位描述。厂商、模型、定价不在本文范围。

## Codex CLI（openai/codex，codex-rs）

harness 是 Rust monorepo，codex-rs 下 100 余个 crate。

编辑机制：apply_patch（codex-rs/core/src/apply_patch.rs + codex-rs/apply-patch crate）。不是直接写文件，而是提交补丁操作 ApplyPatchAction，文件变更分 Add{content}、Delete{content}、Update{unified_diff, move_path, new_content} 四种，用 unified diff 描述。提交前走 assess_patch_safety，结果三态：SafetyCheck::AutoApprove（自动放行）、AskUser（交给工具运行时弹审批）、Reject{reason}（拒绝并回模型）。补丁策略用 PatchPolicyMatcher 匹配。

执行机制：exec.rs + exec/ + exec-server/ + exec-server-protocol。命令执行走沙箱，参数含 command、cwd、env、approval_policy、sandbox、network、sandbox_permissions。沙箱实现分散在 sandboxing/、linux-sandbox/、windows-sandbox-rs/、windows-sandbox-service/、mxc-sandbox/、bwrap/。网络用 network-proxy/，进程加固 process-hardening/。unified_exec/ 统一执行入口，shell.rs + shell_snapshot.rs 管理 shell 状态快照。

工具层：tools crate（tool_definition.rs 定义、tool_discovery.rs 发现、tool_executor.rs 执行、tool_spec.rs 规格、tool_search.rs 搜索、tool_config.rs 配置、dynamic_tool.rs 动态工具、mcp_tool.rs MCP 工具、code_mode.rs 代码模式、output_schema.rs 输出 schema）。动态工具发现是特色：有 TOOL_SEARCH_TOOL_NAME、LIST_AVAILABLE_PLUGINS_TO_INSTALL_TOOL_NAME、REQUEST_PLUGIN_INSTALL_TOOL_NAME 三个内置工具，运行时搜索和安装插件。

核心工具 handlers（core/src/tools/handlers/）：apply_patch、current_time、dynamic、extension_tools、get_context_remaining、list_available_plugins_to_install、mcp、mcp_resource、multi_agents（v1+v2）、new_context_window、plan、request_permissions、request_plugin_install、request_user_input（同步+异步）、send_message_to_user_async、shell、sleep、test_sync、tool_search、unified_exec、view_image、wait_for_environment。

多代理：multi_agents.rs + multi_agents_v2.rs + agent_communication.rs + agent_message_board.rs + agent-roles/ + agent-graph-store/ + agent-message-board-client/，有消息板机制。

模型无关：model-provider/ + model-provider-info/ + models-manager/，另有 ollama/、lmstudio/，支持本地模型和多 provider。

上下文管理：compact.rs + compact_model_fallback.rs + compact_remote_v2.rs + compact_token_budget.rs，context_manager/ + context-fragments/，rollout.rs + rollout_budget.rs 会话录制与预算，thread_manager.rs 线程管理。

权限：approval_policy、permission_profile、patch_policy、exec_policy、network_policy_decision 五层。safety.rs + guardian/ + guardian_review.rs 安全审查。

其他：skills/、hooks/（hook_runtime.rs + hook_mcp_executor.rs）、plugins/ + core-plugins/ + plugin/、connectors/、memories/、worktree/ + git-utils/、file-watcher/、state/、tasks/、voice-host/、realtime-webrtc/、mcp 用 rmcp-client/。

协议：protocol/ + codex-api/ + app-server-protocol/ + code-mode-protocol/ + exec-server-protocol/，MCP 走 rmcp-client。

## Grok Build（xai-org/grok-build）

harness 是 Rust，仓库描述写明 "coding agent harness and TUI"。crates 分 build（xai-proto-build，protobuf 编译）、codegen、common 三组。common 下是核心。

内置工具（xai-tool-types）：Glob（GlobToolInput）、Grep（GrepToolInput, GrepOutputMode, GrepSearchOutput）、Read（ReadLineCounts, ReadLineRange）、Task、WebSearch（WebSearchToolInput, WebSearchOutput）。注意 tool-types 里没有 Write/Edit，文件编辑未在工具类型层出现，可能通过 computer-hub（Computer Use 操作编辑器）或 shell 命令实现，这一点我未在代码里确证。

Task 工具即子代理系统（task.rs）：PLAN_SUBAGENT（规划）、EXPLORE_SUBAGENT（探索）、GENERAL_PURPOSE_SUBAGENT（通用）、BUILTIN_SUBAGENTS（内置子代理集合）、BackgroundSubagent（后台子代理），配套 WaitTasks/KillTask/TaskOutput/MultiTaskOutput 任务管理工具，有 SubagentIsolationMode（子代理隔离模式）、SubagentCapabilityMode、HandedOffSubagentState（交接状态）。

工具定义（definition.rs）：标准 function calling 格式，ToolDefinition{kind: ToolType::Function, function: FunctionTool{name, description, parameters}}，parameters 是 JSON Schema Value。

tool-protocol（xai-tool-protocol crate）：自定义工具协议，有 envelope（信封）、frames（帧）、handshake（握手）、methods（方法）、registration（注册）、session_event（会话事件）、hook + turn_hook（钩子）、capabilities（能力）、connection、error_codes + error_wire、ids、notification_wire、output_wire、registry_error。这是 Grok Build 自己的工具协议，类似 MCP 的定位。

tool-runtime（xai-tool-runtime）：context、dispatch（分发）、error、mcp_structured_content（MCP 结构化内容）、notification、render、search、streaming、tool。

computer-hub（Computer Use）：xai-computer-hub-core（bot_tools、local、remote、registry、resolver、transport）、xai-computer-hub-sdk（admission、auth、cancel、connection、demux、discovery、handshake、harness、oidc_provider、pool、server、metrics、observability）、xai-computer-hub-mcp-adapter（bridge、transport、types）。这是 Grok Build 的 Computer Use 实现，操作电脑桌面。

其他：xai-grok-compaction（压缩）、xai-interjection-core（注入）、xai-message-delivery-core（消息传递）、xai-circuit-breaker（熔断器）、xai-tracing（追踪）、xai-test-utils。

## Claude Code（Anthropic，闭源，功能来自逆向）

协议：tool_use/tool_result content block。tool_use 结构 {type:"tool_use", name, id, input, _meta}，tool_result 结构 {type:"tool_result", tool_use_id, content}，经 Zod schema 校验。content block 类型还有 text、thinking、resource。流式 delta：thinking_delta、input_json_delta、text。

工具集（逆向自 bundle）：Read、Write、Edit、Bash、Glob、Grep、TodoWrite、Task、WebFetch、WebSearch、LS、NotebookEdit。控制工具：set_model、set_permission_mode、interrupt、stop_task、background_tasks、cancel_async_message、set_max_thinking_tokens。

权限三层：permission_mode（default/auto/bypassPermissions）→ toolPolicy（按 MCP 工具，权限值 blocked/ask-session，被 block 返回 "Tool 'X' is not permitted"）→ 写工具门控 gatedWriteToolCalls。

插件生态 11 种类型：commands、agents、output-styles、skills、workflows、routines、themes、rules、session-env、uploads、mcp-skills。目录映射 commands/、agents/、hooks/、mcpServers（.mcp.json 根级）。

Skills 格式：.skill = ZIP 包，内含 <name>/SKILL.md（YAML frontmatter: name/description/license + Markdown 正文）+ scripts/（Python 脚本、模板、验证器）。

MCP：标准协议，配置新格式 [{serverName, tools:[{toolName, permission}]}]，旧 record 格式已废弃。

Agent Teams：Lead/Teammate 角色协作。

## Z Code（智谱）

GitHub 未找到公开 harness 仓库（search 返回 0），代码级功能无法确证。已知定位：统一可视化桌面，一个 API key 切换调用 Claude Code/Codex/Gemini 等底层 Agent，ZCode Agent 内核，GLM-5/5.2。harness 本身是否自研工具集、编辑机制、执行模型，均无代码可考。2026 年 9 月有静默窃码争议，数据行为需谨慎评估。

## 横向对比

编辑机制：Codex 用 apply_patch（unified diff，Add/Delete/Update/Move，补丁安全评估三态）；Claude Code 用 Write/Edit 直接操作文件；Grok Build 的工具类型层无 Write/Edit，编辑路径未确证（疑走 computer-hub 或 shell）；Z Code 未知。

执行机制：Codex 最重，五层权限（approval/permission/patch/exec/network）+ 多沙箱实现（linux/windows/mxc/bwrap）+ 网络代理 + 进程加固；Grok Build 有 circuit-breaker 熔断 + computer-hub；Claude Code 三层权限门控。

工具发现：Codex 最独特，tool_search/list_available_plugins/request_plugin_install 运行时动态发现和安装插件；Grok Build 用 registration/handshake 静态注册；Claude Code 插件类型静态 11 种。

多代理：Codex 有 multi_agents v1+v2 + 消息板；Grok Build 的 Task 内置 PLAN/EXPLORE/GENERAL_PURPOSE 子代理 + 后台子代理 + 任务管理；Claude Code Agent Teams（Lead/Teammate）；Z Code 是人工切换多 Agent。

协议：Codex 用 Responses API 工具格式 + 自有 protocol + MCP（rmcp-client）；Grok Build 用标准 function calling + 自定义 tool-protocol（envelope/frames/handshake）+ MCP adapter + computer-hub；Claude Code 用 tool_use/tool_result content block + MCP。

模型无关性：Codex 最开放（model-provider + ollama + lmstudio，本地模型）；Grok Build 和 Claude Code 主要走自家 API 但都支持 MCP 扩展；Z Code 调用多家。

上下文压缩：Codex 有 compact（含 token budget、remote v2、model fallback）；Grok Build 有 grok-compaction；Claude Code 逆向未见独立压缩 crate（可能在 agent-sdk 内）。

## 对 cc-harness-android 的启示

编辑机制参考 Codex 的 apply_patch（unified diff 比直接写文件更安全，可审计、可回滚，适合移动端弱网环境）。工具发现参考 Codex 的 tool_search 动态发现，比静态注册更适合移动端按需加载。多代理参考 Grok Build 的 Task 子代理（PLAN/EXPLORE/GENERAL_PURPOSE 分工明确）和 Codex 的消息板。协议层 Claude Code 的 tool_use/tool_result 最简洁（逆向已拿到完整格式），适合移动端重写。权限参考 Codex 五层模型，但移动端可先实现三层。Computer Use（Grok Build 的 computer-hub）对移动端价值有限，可暂不实现。

所有结论均来自上述代码文件路径或逆向 grep 结果，Z Code 部分明确标注为信息缺失。
