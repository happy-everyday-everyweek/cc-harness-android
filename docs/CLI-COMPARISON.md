# 四款 AI 编码 CLI 的 Harness 功能对比（基于源码）

方法：直接读 GitHub 源码。Codex 读 openai/codex 的 codex-rs（Rust，100+ crate）；Grok Build 读 xai-org/grok-build（Rust，crates/build+codegen+common）；ZCode 读 zai-org/ZCode（TypeScript monorepo，核心在 apps/zcode-cli/packages/core）；Claude Code 闭源，功能来自对 Claude Desktop 官方 deb 的逆向（asar 主 bundle grep）。厂商、模型、定价不在本文范围。

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

## Z Code（zai-org/ZCode，TypeScript）

官方仓库 zai-org/ZCode（TypeScript monorepo，7410 星，Apache-2.0），另有 zai-org/zcode-plugins 插件市场。核心 harness 在 apps/zcode-cli/packages/core。入口三种：桌面（Electron，packages/desktop）、Web（packages/web）、终端 zcode（TUI/CLI/Web 三合一，无参数进 TUI，--web 起 Web，其他参数交给 Agent CLI）。

Agent 循环：agent/turn-machine.ts 是状态机。阶段 ProcessingInput → AwaitingModelResponse → Streaming → SchedulingTools → AwaitingPermission 或 ExecutingTools → AggregatingResults → Completing，失败走 Error。工具调用有状态机：scheduled/running/waiting_permission/completed/failed/permission_denied。权限决策 resolvePermission 可带 modifiedInput，即用户可改输入后放行。流式用 receiveModelResponse/addStreamingContent 累积。

工具集（tool/handlers/）：read.ts（read-text/read-pdf/read-image/read-video/read-session-context 拆分）、write.ts、edit.ts（edit-matchers 匹配器）、bash.ts（bash-readonly-policy-* 只读策略族，细分 argv/flags/git-subcommands/commands/callbacks/io/process/text 等维度）、glob.ts、grep.ts、todo.ts、webfetch.ts（webfetch-egress-guard 出站防护）、websearch.ts、agent.ts、ask-user-question.ts、send-message.ts、skill.ts、node-repl.ts、cron.ts、off-peak.ts、escalate.ts、plan-mode.ts、list-models.ts、task-output/task-stop.ts、submit-result.ts、respond-to-coordinator.ts、model-reference.ts、tool-perf.ts。

Workflow（核心特色，用户指的工作流）：dynamic-workflow + dynamic-workflow-runtime 两个包。机制是模型写 TypeScript 脚本，编译器恢复严谨性——类型检查（虚拟主机，嵌入式 TS stdlib，无磁盘访问）、schema 合成（ask<T> → JSON Schema）、依赖推断、站点标识。分析管线做污点分析（taint fixpoint）、时间走查（temporal walk），再投影出 site-graph、causality-graph、phase-graph、flow-graph、handoff-graph、actor-graph。lowering 阶段做类型剥离 + 站点 ID 注入到 __host，作为沙箱输入。运行时是子进程 + vm cell + NDJSON 主机桥（dynamic-workflow-runtime），生产驱动在 bootstrap（actor 会话、SQLite journal、工具接线）。引擎有 WorkflowEngine + AskScheduler（序号、journal 回放、修复/提示、用量统计）。ask/actor/world-read/join/fan-out 是脚本里的一等概念。

子代理（subagent/）：explore.ts（探索）+ general-purpose.ts（通用），和 Claude Code/Grok Build 同构。另有 profile-frontmatter.ts（子代理配置文件 frontmatter，类似 SKILL.md）、persistent-memory.ts（持久记忆）、computer-use-policy.ts（CUA 策略）、coordinator-response.ts（协调器响应）、context-builder.ts（上下文构建）、message-steering.ts（消息引导）、runner.ts（运行器）、system-prompt.ts。

权限（permission/）：broker.ts + plan-mode-policy.ts（Plan Mode 只读）+ rule-matching.ts + service.ts + workflow-draft-path.ts。executor 层有 approval-gate.ts + permission-flow.ts + permission-rules.ts + permission-capability.ts + permission-suggestions.ts + permission-input-recheck.ts，权限规则可持久化（permission-rules-persistence）。

记忆（memory/）：memory-agent-loop.ts（记忆 agent 循环）、recall/（召回）、extraction.ts、directory.ts、project-root.ts、origin-session.ts。

钩子（hooks/）：configured-runner.ts + runner.ts + session-mailbox.ts + workspace-hook-*（policy/review-flow/runtime-admission/telemetry/trust-coordinator/trust-domain/trust-evaluation/trust-records），工作区钩子有完整的信任评估链。

CUA：packages/zcode-cua（broker、pip-session、frame-contract、host-display-contract、request-access-contract），Computer Use Agent。

其他：model/ + model-option-map + provider/provider-node（Provider 公共能力 + Node 实现，多模型）、compact/ + agent/compact-session.ts（压缩）、session-context/ + agent/message-history.ts（会话）、runtime/ + runtime-task/ + repl/ + node-repl-host（运行时）、services/（持久化）、telemetry/（遥测）。

2026 年 9 月有静默窃码争议，数据行为需谨慎评估。

## Claude Code（Anthropic，闭源，功能来自逆向）

协议：tool_use/tool_result content block。tool_use 结构 {type:"tool_use", name, id, input, _meta}，tool_result 结构 {type:"tool_result", tool_use_id, content}，经 Zod schema 校验。content block 类型还有 text、thinking、resource。流式 delta：thinking_delta、input_json_delta、text。

工具集（逆向自 bundle）：Read、Write、Edit、Bash、Glob、Grep、TodoWrite、Task、WebFetch、WebSearch、LS、NotebookEdit。控制工具：set_model、set_permission_mode、interrupt、stop_task、background_tasks、cancel_async_message、set_max_thinking_tokens。

权限三层：permission_mode（default/auto/bypassPermissions）→ toolPolicy（按 MCP 工具，权限值 blocked/ask-session，被 block 返回 "Tool 'X' is not permitted"）→ 写工具门控 gatedWriteToolCalls。

插件生态 11 种类型：commands、agents、output-styles/skills、workflows、routines、themes、rules、session-env、uploads、mcp-skills。目录映射 commands/、agents/、hooks/、mcpServers（.mcp.json 根级）。

Skills 格式：.skill = ZIP 包，内含 <name>/SKILL.md（YAML frontmatter: name/description/license + Markdown 正文）+ scripts/（Python 脚本、模板、验证器）。

MCP：标准协议，配置新格式 [{serverName, tools:[{toolName, permission}]}]，旧 record 格式已废弃。

Agent Teams：Lead/Teammate 角色协作。

## 横向对比

编辑机制：Codex 用 apply_patch（unified diff，Add/Delete/Update/Move，补丁安全评估三态）；Claude Code 用 Write/Edit 直接操作文件；Grok Build 的工具类型层无 Write/Edit，编辑路径未确证（疑走 computer-hub 或 shell）；ZCode 用 edit.ts（edit-matchers 匹配器）+ write.ts，另有 diff.ts。

执行机制：Codex 最重，五层权限（approval/permission/patch/exec/network）+ 多沙箱实现（linux/windows/mxc/bwrap）+ 网络代理 + 进程加固；Grok Build 有 circuit-breaker 熔断 + computer-hub；ZCode 用 bash-readonly-policy-* 只读策略族（细分 argv/flags/git-subcommands/commands 等维度）+ webfetch-egress-guard 出站防护；Claude Code 三层权限门控。

工具发现：Codex 独特——tool_search/list_available_plugins/request_plugin_install 运行时动态发现和安装插件；Grok Build 用 registration/handshake 静态注册；ZCode 有 tool-visibility（工具可见性）+ provider-visible-order；Claude Code 插件类型静态 11 种。

多代理：Codex 有 multi_agents v1+v2 + 消息板；Grok Build 的 Task 内置 PLAN/EXPLORE/GENERAL_PURPOSE 子代理 + 后台子代理 + 任务管理；ZCode 有 explore + general-purpose 子代理 + coordinator-response（协调器）+ respond-to-coordinator + message-steering（消息引导）；Claude Code Agent Teams（Lead/Teammate）。

工作流：ZCode 独有——dynamic-workflow 编译器系统（模型写 TS 脚本 → 类型检查 + 污点分析 + 多图投影 → lowering → 沙箱执行），其他三个无对应机制。Codex/Grok Build/Claude Code 的 workflow 是插件类型或静态配置，ZCode 的 workflow 是编译器 + 沙箱执行引擎。

协议：Codex 用 Responses API 工具格式 + 自有 protocol + MCP（rmcp-client）；Grok Build 用标准 function calling + 自定义 tool-protocol（envelope/frames/handshake）+ MCP adapter + computer-hub；ZCode 用 contracts 包（ModelMessageContent/SessionId/TraceId/ToolCallId/TurnId）+ MCP（mcp/）；Claude Code 用 tool_use/tool_result content block + MCP。

模型无关：Codex 最开放（model-provider + ollama + lmstudio，本地模型）；ZCode 有 provider/provider-node + model-option-map + list-models（多模型）；Grok Build 和 Claude Code 主要走自家 API 但都支持 MCP 扩展。

上下文压缩：Codex 有 compact（含 token budget、remote v2、model fallback）；Grok Build 有 grok-compaction；ZCode 有 compact/ + agent/compact-session.ts；Claude Code 逆向未见独立压缩 crate（可能在 agent-sdk 内）。

记忆：ZCode 独有——memory/（memory-agent-loop 记忆 agent 循环 + recall 召回 + extraction）；Codex 有 memories/；Grok Build 和 Claude Code 逆向未见独立记忆模块。

权限细节：ZCode 最细——plan-mode-policy（Plan Mode 只读）+ rule-matching（规则匹配）+ approval-gate + permission-flow + permission-rules（可持久化）+ permission-capability + permission-suggestions + permission-input-recheck，权限决策可修改输入（modifiedInput）；Codex 五层但偏静态策略；Grok Build 有 circuit-breaker；Claude Code 三层门控。

## 对 cc-harness-android 的启示

编辑机制参考 Codex 的 apply_patch（unified diff 比直接写文件更安全，可审计、可回滚，适合移动端弱网环境），ZCode 的 edit-matchers 也可参考。工具发现参考 Codex 的 tool_search 动态发现，比静态注册更适合移动端按需加载。多代理参考 Grok Build 的 Task 子代理（PLAN/EXPLORE/GENERAL_PURPOSE 分工明确）、Codex 的消息板、ZCode 的 coordinator 协调器。协议层 Claude Code 的 tool_use/tool_result 最简洁（逆向已拿到完整格式），适合移动端重写；ZCode 的 TurnMachine 状态机是清晰的 agent 循环参考。权限参考 ZCode 的分层（plan-mode 只读 + rule-matching + approval-gate + 可持久化规则），比 Codex 五层更细且移动端可裁剪。Computer Use（Grok Build 的 computer-hub、ZCode 的 zcode-cua）对移动端价值有限，可暂不实现。ZCode 的 dynamic-workflow 编译器最独特但也最重，移动端可先做简化版（类型检查 + schema 合成，跳过污点分析和多图投影）。

所有结论均来自上述代码文件路径或逆向 grep 结果。ZCode 的 dynamic-workflow 编译器、ZCode 的窃码争议、Grok Build 的文件编辑路径（无 Write/Edit）是三个仍需进一步确认的点。
