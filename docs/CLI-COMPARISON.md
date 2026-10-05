# 四款 AI 编码 CLI 对比：Codex CLI / Grok Build / Z Code / Claude Code

对比维度：厂商模型、架构形态、工具集、代理协议、权限沙箱、上下文、多代理、插件生态、开源许可、定价平台。

## 总览表

| 维度 | Codex CLI | Grok Build | Z Code | Claude Code |
| --- | --- | --- | --- | --- |
| 厂商 | OpenAI | xAI (SpaceXAI) | 智谱 Z.ai | Anthropic |
| 模型 | GPT-5.2/5.3-Codex | Grok 4.7 | GLM-5 / GLM-5.2 | Claude Opus/Sonnet |
| 首发 | 2025 | 2026 年中 | 2025-12-26（Alpha） | 2025 |
| 架构 | 云端编排 | 本地 TUI | 统一桌面 + 多 Agent | 本地优先 |
| 形态 | CLI | 全屏 TUI | GUI 桌面 + CLI | CLI + TUI |
| 实现语言 | TypeScript | Rust（99.6%） | 未公开 | TypeScript |
| 上下文 | 未公开大窗 | 未公开 | GLM-5 窗 | 200K token |
| 多代理 | Agents SDK 多代理 | headless/ACP 嵌入 | 一键切换多个 Agent | Agent Teams（Lead/Teammate） |
| 代理协议 | 自有 + MCP | Agent Client Protocol (ACP) | 复用底层 Agent 协议 | tool_use/tool_result + MCP |
| Web 搜索 | 有 | 有 | 有 | WebSearch/WebFetch 工具 |
| 沙箱 | 云端沙箱 | 本地执行 | 本地执行 | 本地 + 权限门控 |
| 开源 | 开源 | 开源（9 天 2.1 万星） | 开源 | 闭源（协议公开） |
| 定价 | $0.20-1.50/会话 | SuperGrok/X Premium | GLM API 计费 | $0.50-3.00/会话 |

## Codex CLI（OpenAI）

云端编排型。GPT-5.2/5.3-Codex 驱动，terminal-native，开源。核心是自主任务编排，可派生多个 agent 异步分析、评审、实现。沙箱执行，git 集成顺滑。token 效率约为 Claude Code 的 3 倍，单会话成本更低（$0.20-1.50）。迭代风格是快速出草稿再多次精炼，适合探索式编程和原型。HumanEval 90.2%，SWE-bench 69.1%。约 8 个文件后开始出现上下文衰减。适合终端快速任务、成本敏感的自动化、云端多代理编排。

## Grok Build（xAI）

本地全屏 TUI。Grok 4.7 驱动，99.6% Rust 实现，开源（9 天斩获 2.1 万 GitHub 星，xai-org/grok-build）。理解代码库、编辑文件、执行 shell、搜索网页、管理长任务。三种运行模式：交互式 TUI、无头模式（脚本/CI）、编辑器嵌入（Agent Client Protocol 协议，可接入 VS Code/Cursor 等）。有官方 Claude Code 插件（xai-org/grok-build-plugin-cc），可把 review、rescue、会话迁移委托给 Grok Build。生态活跃，有 grok-app（Tauri 桌面 GUI）、grok-build-vscode 等第三方。适合喜欢全屏终端体验、需要编辑器嵌入、看重开源和 Rust 性能的开发者。

## Z Code（智谱 Z.ai）

统一桌面 + 多 Agent 切换。2025 年 12 月 26 日发布（Alpha，Mac/Windows），定位是降低 Claude Code/Codex/Gemini 这类命令行工具的门槛。2026 年 2 月基于 GLM-5，6 月 Z Code 3.0 切换至自研 ZCode Agent 内核，深度适配 GLM-5.2。核心理念是一个 API key 丝滑切换体验多个 Agent 编程工具（可调用 Claude Code、Codex、Gemini 等），提供统一可视化桌面。终端 CLI 形态（ZCode）也能读项目、操作文件、执行命令、Git。注意：2026 年 9 月曝出静默窃码争议（被指未经明确同意读取代码），选型需评估其数据行为。适合想统一管理多个 Agent、用 GLM 模型、偏好图形桌面的开发者。

## Claude Code（Anthropic）

本地优先 CLI。Claude Opus/Sonnet 驱动，200K token 上下文，读整个代码库、编辑、跑测试、就地迭代。协议是 tool_use/tool_result content block（content block 类型：tool_use、tool_result、text、thinking、resource），工具集含 Read/Write/Edit/Bash/Glob/Grep/TodoWrite/Task/WebFetch/WebSearch/LS/NotebookEdit，控制工具含 set_model/set_permission_mode/interrupt/stop_task/background_tasks。权限三层：permission_mode（default/auto/bypassPermissions）→ toolPolicy（blocked/ask-session）→ 写工具门控。插件生态 11 种类型（commands/agents/output-styles/skills/workflows/routines/themes/rules/session-env/uploads/mcp-skills），Skills 是 SKILL.md + scripts 的 ZIP 包。MCP 标准协议支持。Agent Teams（Lead/Teammate 角色）。HumanEval 92%，SWE-bench 72.7%。accuracy-first：迭代少、代码质量高、边界错误少，大型多文件重构（15000 行 Python monorepo 跨 12 文件）保持连贯。单会话 $0.50-3.00。闭源但协议格式公开。适合大型多文件项目、复杂重构、高精度编码、Anthropic 生态团队。

## 功能异同

相同点：都是终端优先的 AI 编码代理，都能读代码库、编辑文件、执行命令、Git 集成都支持，都支持 Web 搜索，都支持 MCP 或插件生态，都有开源或协议公开的实现。

架构差异：Codex CLI 偏云端编排，把任务交给云端 agent 异步跑；Grok Build 和 Claude Code 偏本地执行，直接在你的机器上操作；Z Code 是统一桌面层，本身不实现代理内核，而是调用底层多个 Agent（Claude Code/Codex/Gemini）。这是 Z Code 最独特的点——它是 Agent 的调度器而非 Agent 本身。

协议差异：Claude Code 用 Anthropic 的 tool_use/tool_result content block 协议（与 Claude Desktop/Cowork 同源），MCP 做工具扩展；Grok Build 用 Agent Client Protocol（ACP）嵌入编辑器；Codex CLI 用自有协议 + MCP；Z Code 复用底层 Agent 的协议（取决于调用哪个）。

多代理差异：Codex CLI 是松耦合自主代理（Agents SDK）；Claude Code 是结构化 Agent Teams（Lead/Teammate 角色定义）；Grok Build 是 headless 并行 + ACP 嵌入；Z Code 是人工切换多个 Agent（非自动协作）。

生态差异：Claude Code 插件类型最丰富（11 种，Skills 格式统一）；Grok Build 开源生态增长最快（9 天 2.1 万星）；Codex CLI 与 OpenAI 生态绑定；Z Code 主打多 Agent 统一入口。

成本差异：Codex CLI 最便宜（token 效率 3 倍），Claude Code 最贵但精度最高，Grok Build 走订阅（SuperGrok/X Premium），Z Code 走 GLM API 计费。

安全差异：Codex CLI 云端沙箱隔离；Claude Code 三层权限门控；Grok Build 本地执行需自觉；Z Code 有静默窃码争议，数据行为需警惕。

## 对 cc-harness-android 的启示

协议层最值得参考 Claude Code 的 tool_use/tool_result content block 设计（与 Claude Desktop 同源，逆向已拿到完整格式）。插件生态参考 Claude Code 的 11 种类型和 Skills ZIP 格式。多代理参考 Claude Code 的 Agent Teams 角色模型。权限参考 Claude Code 的三层门控。Grok Build 的 ACP 是编辑器嵌入的可选协议。Z Code 的多 Agent 统一调度思路可用于 Android harness 的模型切换层。

信息来源：2026 年公开资料（Bing 搜索、GitHub 仓库、百度百科），具体数字以官方最新文档为准。
