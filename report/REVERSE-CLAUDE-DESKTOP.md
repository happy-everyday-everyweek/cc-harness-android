# 字体文件与 @font-face 分析

## 字体文件（woff/woff2/ttf/otf）
unpacked/.vite/renderer/quick_window/assets/AnthropicSerif-Roman-Variable-2VcCjn5t.woff2
unpacked/.vite/renderer/quick_window/assets/AnthropicSans-Roman-Variable-DDVos-BJ.woff2
unpacked/.vite/renderer/quick_window/assets/AnthropicSans-Italic-Variable-CJtkx3-S.woff2
unpacked/.vite/renderer/quick_window/assets/AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2
unpacked/.vite/renderer/main_window/assets/AnthropicSerif-Roman-Variable-2VcCjn5t.woff2
unpacked/.vite/renderer/main_window/assets/AnthropicSans-Roman-Variable-DDVos-BJ.woff2
unpacked/.vite/renderer/main_window/assets/AnthropicSans-Italic-Variable-CJtkx3-S.woff2
unpacked/.vite/renderer/main_window/assets/AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2
unpacked/.vite/renderer/buddy_window/assets/AnthropicSerif-Roman-Variable-2VcCjn5t.woff2
unpacked/.vite/renderer/buddy_window/assets/AnthropicSans-Roman-Variable-DDVos-BJ.woff2
unpacked/.vite/renderer/buddy_window/assets/AnthropicSans-Italic-Variable-CJtkx3-S.woff2
unpacked/.vite/renderer/buddy_window/assets/AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2
unpacked/.vite/renderer/find_in_page/assets/AnthropicSerif-Roman-Variable-2VcCjn5t.woff2
unpacked/.vite/renderer/find_in_page/assets/AnthropicSans-Roman-Variable-DDVos-BJ.woff2
unpacked/.vite/renderer/find_in_page/assets/AnthropicSans-Italic-Variable-CJtkx3-S.woff2
unpacked/.vite/renderer/find_in_page/assets/AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2
unpacked/.vite/renderer/about_window/assets/AnthropicSerif-Roman-Variable-2VcCjn5t.woff2
unpacked/.vite/renderer/about_window/assets/AnthropicSans-Roman-Variable-DDVos-BJ.woff2
unpacked/.vite/renderer/about_window/assets/AnthropicSans-Italic-Variable-CJtkx3-S.woff2
unpacked/.vite/renderer/about_window/assets/AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2

## @font-face 声明（css）
@font-face{font-family:anthropic-sans;src:url(./AnthropicSans-Roman-Variable-DDVos-BJ.woff2)format("woff2");font-weight:300 800;font-style:normal;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-sans;src:url(./AnthropicSans-Italic-Variable-CJtkx3-S.woff2)format("woff2");font-weight:300 800;font-style:italic;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-serif;src:url(./AnthropicSerif-Roman-Variable-2VcCjn5t.woff2)format("woff2");font-weight:300 800;font-style:normal;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-serif;src:url(./AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2)format("woff2");font-weight:300 800;font-style:italic;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-sans;src:url(./AnthropicSans-Roman-Variable-DDVos-BJ.woff2)format("woff2");font-weight:300 800;font-style:normal;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-sans;src:url(./AnthropicSans-Italic-Variable-CJtkx3-S.woff2)format("woff2");font-weight:300 800;font-style:italic;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-serif;src:url(./AnthropicSerif-Roman-Variable-2VcCjn5t.woff2)format("woff2");font-weight:300 800;font-style:normal;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-serif;src:url(./AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2)format("woff2");font-weight:300 800;font-style:italic;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-sans;src:url(./AnthropicSans-Roman-Variable-DDVos-BJ.woff2)format("woff2");font-weight:300 800;font-style:normal;font-display:swap;font-feature-settings:"dlig" 0
@font-face{font-family:anthropic-sans;src:url(./AnthropicSans-Italic-Variable-CJtkx3-S.woff2)format("woff2");font-weight:300 800;font-style:italic;font-display:swap;font-feature-settings:"dlig" 0

## @font-face 声明（js 内联）
@font-face names\n   below match \u2014 so this file can't silently drift from the names the\n   rest of the suite registers. This document can't consume the central\n   @font-face sheets themselves (package-relative url()s; these faces\n   are CDN statics, and the sans faces arrive via #mcp-host-fonts at\n   runtime), so it centralizes the NAMES instead. */\n\n@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-Regular-Static.otf")\n    format("opentype");\n  font-weight: 400;\n  font-style: normal;\n  font-di
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-RegularItalic-Static.otf")\n    format("opentype");\n  font-weight: 400;\n  font-style: italic;\n  f
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-Medium-Static.otf")\n    format("opentype");\n  font-weight: 500;\n  font-style: normal;\n  font-dis
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-MediumItalic-Static.otf")\n    format("opentype");\n  font-weight: 500;\n  font-style: italic;\n  fo
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-Semibold-Static.otf")\n    format("opentype");\n  font-weight: 600;\n  font-style: normal;\n  font-d
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-SemiboldItalic-Static.otf")\n    format("opentype");\n  font-weight: 600;\n  font-style: italic;\n  
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-Bold-Static.otf")\n    format("opentype");\n  font-weight: 700;\n  font-style: normal;\n  font-displ
@font-face {\n  font-family: "anthropic-serif";\n  src: url("https://assets.claude.ai/Fonts/AnthropicSerif-Text-BoldItalic-Static.otf")\n    format("opentype");\n  font-weight: 700;\n  font-style: italic;\n  font

## 字体 URL（css url 引用）
url(./AnthropicSans-Italic-Variable-CJtkx3-S.woff2)
url(./AnthropicSans-Roman-Variable-DDVos-BJ.woff2)
url(./AnthropicSerif-Italic-Variable-Dcb-9NUS.woff2)
url(./AnthropicSerif-Roman-Variable-2VcCjn5t.woff2)

## 字体名引用（js）
anthropic-sans: 7
anthropic-serif: 12
Anthropicons: 0
cds-font: 0

## deb 内字体（extract 层）
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cd710415c-YYjJ1zSn.ttf
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cbfcbf131-CZnvNsCZ.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c649816ca-CTYiF6lA.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cfc8d24e7-DMm9YOAa.woff
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c9beb4a6c-BsDP51OF.woff
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c59d6eb1d-lZojiKrH.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c41a3c357-oD1tc_U0.woff
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c28cfec68-DYV7mfZR.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c7b6f821f-Dq_IR9rO.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c649816ca-CB_wures.ttf
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cb43dfac5-Dbsnue_I.ttf
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/ce6cfa480-T9ivnrk-.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cc27851ad-DDVos-BJ.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cfc8d24e7-BQhdFMY1.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/cfc8d24e7-DRggAlZN.ttf
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c73cc6b20-DV_iHO0x.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c41a3c357-Dy4dx90m.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/ccd43810d-DDBCnlJ7.woff2
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c3dd54df2-BF-4gkZK.woff
extract/usr/lib/claude-desktop/resources/ion-dist/assets/v1/c2f08283e-CZqJpOsc.woff2
