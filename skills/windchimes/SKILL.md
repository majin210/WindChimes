---
name: "windchimes"
description: "维护用户的 WindChimes GitHub Pages 站点：视觉改版、内容更新、Logo/图标调整、编辑器同步、检查与推送上线。当用户提到 WindChimes、风铃站点、主页改版、推送上线时触发。"
---

# WindChimes 站点维护

## Purpose
`majin210/WindChimes`（https://majin210.github.io/WindChimes/）的日常维护：
改视觉、改内容、换 Logo/图标、同步内容编辑器、跑检查清单、推送到 GitHub Pages 上线。

## 文件地图（唯一事实源）
- 正式文件：`~/workspace/windchimes-drift/index.html` —— 所有改动先改这里
- 内容编辑器：`~/workspace/your_files/windchimes-editor.html`（symlink 到 goal files）
- 编辑器同步脚本：`~/workspace/windchimes-drift/sync_editor.py`（把正式文件灌进编辑器 TEMPLATE，顺带 `node --check` 校验两段 script）
- 对外附件：`~/workspace/your_files/windchimes-drift-index.html`（改完复制一份过去）
- 面板附件：`~/workspace/goals/github-homepage-design-optimization/files/windchimes-drift-index.html`（与正式文件保持一致）

## 工作规矩（用户明确）
1. **视觉改动先给效果图**：用 Playwright 截小样发对话里，用户回 OK/很好/开始/好 = 确认，确认前不动正式文件。
2. **一次只做完一件事**：当前任务没完成前不抛其他改进。
3. **背景已定版**，不主动重开（drift：深紫→墨黑→深青底 + 5 大色块 screen 混合 + 纯 CSS 漂移）。海葵/触手丝缕方向彻底否决，不再提。
4. `CONTACT_EMAIL` 仍是 `you@example.com`——用户没给真实邮箱前不擅自填，表单走 mailto。
5. 调参齿轮（左下角常显）/声音按钮（右下角）上线去留用户未确认，不擅自隐藏。
6. 效果图/附件在对话里直接展示，不甩外部链接；部署只走 GitHub，不换方式。

## 设计定版参数
- 导航顺序：关于 / 联系 / 作品 / 学习；移动端导航 11px、间距 20px
- Hero 主标题：`clamp(36px, 4.6vw, 64px)`；统计行 05 精选作品 / 06 学习笔记 / 07 技术栈
- 作品标题统一 22px；分类按钮：全部/场景/特效/着色/草图（选中态半透明柔白）；技能筛选紫色 pill，可点 pill 清除；筛选切换 0.35s 淡入上浮
- 详情弹窗：遮罩 `rgba(7,7,8,0.88)`，SVG 关闭按钮 40×40（14px X，线宽 1.8），距右上各 24px，Esc 可关
- 页脚只保留 `© 2026 Wind Chimes`
- 联系区竖排：风铃卡片（图标+诗句"风铃一响，便是有风来访"+介绍）在上，表单在下，`.contact-grid` 单列 max-width 620px
- Logo = 晴天娃娃：导航用渐变圆环 + 渐变娃娃（系绳、深色头心 `#1b1b28`、钟形波浪斗篷）；联系区图标与声音按钮用**同款造型白色线条版**（无渐变、无黑底）；声音按钮保留两侧声波
- 渐变 `chimeGrad`：`#9a75e8` → `#51d1e3` → `#df87eb`（defs 放在 `<body>` 后第一个隐藏 svg 里，id 全局引用）
- 背景调参：6 滑杆（漂移速度/色块大小/模糊/浓度/色相/视差 0~2x），默认速度 4.00x，存 localStorage；移动端色块模糊 55px（桌面 90px）

## 标准工作流
1. 在 `/tmp` 建小样改 → Playwright 截图 → 对话里给用户确认
2. 确认后合入正式文件
3. `python3 ~/workspace/windchimes-drift/sync_editor.py`（自动 node --check）
4. `cp` 正式文件到 `~/workspace/your_files/windchimes-drift-index.html` 及面板附件
5. 跑检查清单（见 `references/qa-checklist.md`）
6. 推送上线（见 `references/deploy.md`，需用户批准一次写操作）
7. 等 1~2 分钟，curl 验证线上是新版

## 内容编辑器
- 三页签：作品 / 学习笔记 / 邮箱；封面按 hues 自动生成；有整站预览、导出 index.html、**一键推送到 GitHub**（用户浏览器里存 Fine-grained Token，localStorage）
- 网站大改版后需重新生成编辑器（模板同步只能同步数据结构，改布局要重建）

## Operating Rules
- `/tmp` 会被系统清理：同步脚本、检查脚本放 workspace 持久化（`~/workspace/windchimes-drift/`、`~/workspace/windchimes-preview/`）
- Playwright 用 `playwright-core` + 系统 chromium：`chromium.launch({ executablePath: '/home/hatch/.cache/ms-playwright/chromium-1243/chrome-linux64/chrome', args: ['--no-sandbox'] })`
- 测试选择器以文件实际类名为准（`.filter-btn` 不是 `.cat-btn`；弹窗是 `#workDetail.open`/`#studyDetail.open`；学习卡片是 `.study-card`；筛选靠 `style.display` 不是 `.hidden` 类）
- 沙盒到 github.io 的浏览器隧道可能不通（`ERR_TUNNEL_CONNECTION_FAILED`），属环境问题；验证线上改用 `curl`
- 推送前先 `get_file_contents` 取当前 blob SHA；`create_or_update_file` 每次都要用户批准
