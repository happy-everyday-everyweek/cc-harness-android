# Claude Desktop 逆向分析

## 包来源
https://downloads.claude.ai/claude-desktop/apt/stable/pool/main/c/claude-desktop/claude-desktop_2.9939.4_amd64.deb

## asar 清单
extract/usr/lib/claude-desktop/resources/app.asar

## 解包后顶层结构
total 32
drwxr-xr-x 6 runner runner 4096 Oct  4 15:03 .
drwxr-xr-x 8 runner runner 4096 Oct  4 15:03 ..
drwxr-xr-x 4 runner runner 4096 Oct  4 15:03 .vite
drwxr-xr-x 2 runner runner 4096 Oct  4 15:03 compile-cache
drwxr-xr-x 6 runner runner 4096 Oct  4 15:03 node_modules
-rw-r--r-- 1 runner runner 5024 Oct  4 15:03 package.json
drwxr-xr-x 5 runner runner 4096 Oct  4 15:03 resources

## 目录树（3 层）
unpacked
unpacked/.vite
unpacked/.vite/build
unpacked/.vite/build/file-index-worker
unpacked/.vite/build/heavy-work-worker
unpacked/.vite/build/mcp-runtime
unpacked/.vite/build/pty-host
unpacked/.vite/build/shell-path-worker
unpacked/.vite/build/stall-sampler-worker
unpacked/.vite/build/transcript-search-worker
unpacked/.vite/renderer
unpacked/.vite/renderer/about_window
unpacked/.vite/renderer/buddy_window
unpacked/.vite/renderer/find_in_page
unpacked/.vite/renderer/local_exec_consent
unpacked/.vite/renderer/main_window
unpacked/.vite/renderer/quick_window
unpacked/compile-cache
unpacked/node_modules
unpacked/node_modules/@ant
unpacked/node_modules/@ant/claude-native
unpacked/node_modules/agent-base
unpacked/node_modules/agent-base/dist
unpacked/node_modules/node-pty
unpacked/node_modules/node-pty/lib
unpacked/node_modules/node-pty/prebuilds
unpacked/node_modules/ws
unpacked/node_modules/ws/lib
unpacked/resources
unpacked/resources/bundled-skills
unpacked/resources/github-mcp
unpacked/resources/office365-mcp

## package.json
{
  "name": "@ant/desktop",
  "productName": "Claude",
  "desktopName": "com.anthropic.Claude.desktop",
  "version": "2.9939.4",
  "author": "Anthropic PBC",
  "description": "Desktop application for Claude.ai",
  "main": ".vite/build/index.pre.js",
  "private": true,
  "engines": {
    "node": ">=22.0.0"
  },
  "installConfig": {
    "hoistingLimits": "workspaces"
  },
  "keywords": [],
  "devDependencies": {
    "@ant/alt-tool-runner": "workspace:*",
    "@ant/app-cu-helper": "workspace:*",
    "@ant/browser-tool-schemas": "*",
    "@ant/cds": "workspace:*",
    "@ant/chrome-native-host": "*",
    "@ant/claude-ssh": "*",
    "@ant/cowork-win32-service": "*",
    "@ant/disclaimer": "*",
    "@ant/dist-guards": "workspace:*",
    "@ant/dxt-registry": "*",
    "@ant/icons": "*",
    "@ant/ipc-codegen": "*",
    "@ant/managed-config": "*",
    "@ant/oxlint-config": "*",
    "@ant/private-api-client": "*",
    "@ant/rfb-client": "*",
    "@ant/sbom": "workspace:*",
    "@ant/security": "*",
    "@ant/session-halo-helper": "workspace:*",
    "@ant/ssh-askpass": "*",
    "@ant/typography": "*",
    "@ant/utils": "*",
    "@anthropic-ai/claude-agent-sdk": "0.3.284-rc.20260927.t043816.sha16cbb4d",
    "@anthropic-ai/claude-agent-sdk-future": "npm:@anthropic-ai/claude-agent-sdk@0.3.283-dev.20260924.t125113.sha53ccb13",
    "@anthropic-ai/electron-devtools-mcp": "workspace:*",
    "@anthropic-ai/mcpb": "2.1.2",
    "@anthropic-ai/sdk": "catalog:",
    "@electron-forge/cli": "8.0.0-alpha.10",
    "@electron-forge/maker-base": "8.0.0-alpha.10",
    "@electron-forge/maker-deb": "patch:@electron-forge/maker-deb@npm:8.0.0-alpha.10#~/.yarn/patches/@electron-forge-maker-deb-npm-8.0.0-alpha.10-1457f432a7.patch",
    "@electron-forge/maker-dmg": "8.0.0-alpha.10",
    "@electron-forge/maker-msix": "8.0.0-alpha.10",
    "@electron-forge/maker-pkg": "patch:@electron-forge/maker-pkg@npm:8.0.0-alpha.10#~/.yarn/patches/@electron-forge-maker-pkg-npm-8.0.0-alpha.10-12af860aea.patch",
    "@electron-forge/maker-rpm": "8.0.0-alpha.10",
    "@electron-forge/maker-squirrel": "8.0.0-alpha.10",
    "@electron-forge/maker-zip": "8.0.0-alpha.10",
    "@electron-forge/plugin-base": "8.0.0-alpha.10",
    "@electron-forge/plugin-fuses": "8.0.0-alpha.10",
    "@electron-forge/plugin-vite": "8.0.0-alpha.10",
    "@electron-forge/publisher-gcs": "8.0.0-alpha.10",
    "@electron-forge/publisher-static": "8.0.0-alpha.10",
    "@electron-forge/shared-types": "8.0.0-alpha.10",
    "@electron/fuses": "^2.1.3",
    "@electron/get": "^5.0.0",
    "@electron/notarize": "^3.1.0",
    "@formatjs/intl": "4.1.15",
    "@google-cloud/storage": "^7.18.0",
    "@malept/cross-spawn-promise": "^2.0.0",
    "@modelcontextprotocol/client": "catalog:",
    "@modelcontextprotocol/core": "catalog:",
    "@modelcontextprotocol/sdk": "catalog:",
    "@noble/hashes": "2.3.0",
    "@sentry/electron": "^7.12.0",
    "@sentry/vite-plugin": "^4.3.0",
    "@tailwindcss/forms": "^0.5.3",
    "@tailwindcss/postcss": "catalog:",
    "@types/fs-extra": "^11.0.4",
    "@types/js-yaml": "^4.0.9",
    "@types/jsonwebtoken": "^9.0.10",
    "@types/node": "catalog:",
    "@types/plist": "^3",
    "@types/react": "catalog:",
    "@types/react-dom": "catalog:",
    "@types/semver": "^7.7.0",
    "@types/ssh2": "^1.15.5",
    "@types/yauzl": "^2.10.3",
    "@typescript/typescript6": "catalog:",
    "@vitejs/plugin-react": "catalog:",
    "bcrypt-pbkdf": "^1.0.2",
    "clsx": "catalog:",
    "conf": "^10.2.0",
    "cookie": "1.1.1",
    "cronstrue": "^3.9.0",
    "cross-env": "^7.0.3",
    "electron": "catalog:",
    "electron-devtools-installer": "^4.0.0",
    "electron-store": "^8.2.0",
    "electron-window-state": "^5.0.3",
    "fflate": "^0.8.2",
    "filenamify": "^7.0.2",
    "form-data": "^4.0.4",
    "fs-extra": "^11.3.0",
    "js-yaml": "^4.1.1",
    "jsonc-parser": "^3.3.1",
    "jsonwebtoken": "9.0.3",
    "knip": "catalog:",
    "magic-string": "^0.30.21",
    "mime": "^4.0.7",
    "oxfmt": "catalog:",
    "oxlint": "catalog:",
    "oxlint-tsgolint": "^7.0.2001",
    "p-queue": "^8.0.0",
    "playwright-core": "1.57.0",
    "plist": "^3.1.0",
    "postcss": "catalog:",
    "react": "catalog:",
    "react-dom": "catalog:",
    "react-intl": "catalog:",
    "rxjs": "^7.8.1",
    "semver": "^7.7.2",
    "sharp": "catalog:",
    "ssh2": "^1.16.0",
    "tailwindcss": "catalog:",
    "tar": "catalog:",
    "terser": "^5.47.1",
    "ts-morph": "^27.0.0",
    "tsx": "catalog:",
    "typescript": "catalog:",
    "vite": "catalog:",
    "vitest": "catalog:",
    "winston": "^3.17.0",
    "winston-transport": "^4.9.0",
    "yauzl": "^3.2.0",
    "zod": "catalog:"
  },
  "dependencies": {
    "@ant/claude-for-chrome-mcp": "*",
    "@ant/claude-native": "*",
    "@ant/claude-swift": "*",
    "@ant/computer-use-mcp": "*",
    "@ant/imagine-server": "*",
    "@loc/electron-window": "1.1.0",
    "https-proxy-agent": "^7.0.6",
    "ws": "^8.18.0"
  },
  "optionalDependencies": {
    "node-pty": "1.2.0-beta.14"
  }
}

## 关键字命中文件数（JS 文件）
Cowork: 34 files
cowork: 46 files
MCP: 38 files
mcp: 72 files
Skill: 24 files
skill: 36 files
Plugin: 34 files
plugin: 54 files
Connector: 18 files
connector: 26 files
desktop: 93 files
code: 183 files

## 主进程协议模式（index.pre.js 命中次数）
tool_use: 0
tool_result: 0
tool_choice: 0
permission_mode: 0
allowedTools: 0
disallowedTools: 0
input_schema: 0
mcpServers: 0
model: 0
context_window: 0
system_prompt: 0

## 内置工具名定义命中（所有 build JS）

## 消息角色命中
user: 0
assistant: 0
system: 0
tool: 0

## 最大的 20 个 JS 文件
6600826 unpacked/.vite/build/index.chunk-DuaKZOPP.js
1595761 unpacked/.vite/build/index.chunk-B9SZqsi8.js
1251838 unpacked/.vite/build/index.chunk-BEotU2NU.js
1125678 unpacked/.vite/build/index.chunk-vRLpacfH.js
1051034 unpacked/.vite/build/index.pre.js
834824 unpacked/.vite/build/file-index-worker/fileIndexWorker.js
631831 unpacked/.vite/build/index.chunk-CKt-cwRV.js
593839 unpacked/.vite/build/mcp-runtime/directMcpHost.js
541578 unpacked/.vite/build/index.chunk-vKHHEwY1.js
433463 unpacked/.vite/build/heavy-work-worker/dist-CI9lzb1v.js
352646 unpacked/.vite/build/index.chunk-DYbdAm12.js
342326 unpacked/.vite/renderer/quick_window/assets/main-CcJZQl2t.js
342021 unpacked/.vite/build/heavy-work-worker/heavyWorkWorker.js
323193 unpacked/.vite/renderer/about_window/assets/main-rKKlJFrg.js
323182 unpacked/.vite/renderer/buddy_window/assets/main-DaiFXWfK.js
323173 unpacked/.vite/renderer/find_in_page/assets/main-DFc6D1kL.js
304489 unpacked/.vite/build/index.chunk-D6vCltAs.js
288472 unpacked/.vite/build/index.chunk-CD_xAMwd.js
257139 unpacked/.vite/build/index.chunk-B9nHsrmb.js
197031 unpacked/.vite/build/index.chunk-BfINPqEi.js

## 文件总数与总大小
367
63M	unpacked
