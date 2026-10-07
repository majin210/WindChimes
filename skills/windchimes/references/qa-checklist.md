# 上线前检查清单

用 Playwright 跑正式文件 `~/workspace/windchimes-drift/index.html`（桌面 1400×900 + 移动 390×844），`pageerror` 必须为空。

## 必查项
- [ ] 无 JS 报错（桌面 + 移动）
- [ ] 导航锚点：点 关于/联系/作品/学习，平滑滚动到对应区（容差 120px）
- [ ] 分类筛选：点 特效 等，`style.display` 过滤正确；点 全部 恢复
- [ ] 技能筛选：点 `.skill-chip[data-skill]` 出现紫色 `#tagPill`；点 pill 清除
- [ ] 作品详情：点 `.work-card` → `#workDetail.open`；关闭按钮含 svg；Esc 关闭
- [ ] 学习详情：点 `.study-card`（先 scrollIntoView）→ `#studyDetail.open`；Esc 关闭
- [ ] 联系表单 `.contact-form` 存在
- [ ] Logo：`.nav-orb svg circle` 存在且渐变描边渲染（截图目检）
- [ ] 联系区图标 `.chime-note svg path`、声音按钮 `#soundBtn svg path` 存在；声音开关切换 `.on`
- [ ] 移动端：`document.documentElement.scrollWidth <= 391`（无横向溢出）；`.blob` 的 filter 含 `blur(55px)`
- [ ] 调参面板：齿轮点击或按 T 呼出，6 个滑杆可拖

## 选择器对照（别写错）
| 用途 | 正确选择器 |
|---|---|
| 分类按钮 | `.filter-btn`（`data-cat`） |
| 筛选状态 | `card.style.display !== 'none'`（不是 `.hidden` 类） |
| 作品弹窗 | `#workDetail.open`，关闭钮 `.detail-close svg` |
| 学习弹窗 | `#studyDetail.open` |
| 学习卡片 | `.study-card` |
| 技能 pill | `#tagPill` |
| 技能标签 | `.skill-chip[data-skill]` |

## 上线后验证
- `curl -s https://majin210.github.io/WindChimes/ -o /tmp/live.html`，HTTP 200，文件大小与推送一致
- `grep` 关键标记（如新 class、文案）在 `/tmp/live.html` 中存在
- Playwright 打开 `/tmp/live.html` 截图目检（沙盒浏览器直连 github.io 可能隧道失败，属环境问题，用 curl 下载后本地渲染）
