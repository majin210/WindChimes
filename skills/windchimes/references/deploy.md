# 推送上线流程

仓库：`majin210/WindChimes`，分支 `main`，GitHub Pages 自动构建。

## 前置
- 先读 `/opt/hatch/skills/github/SKILL.md`，`github status` 确认 `authenticated: true`
- 写操作走 `github call-tool --name create_or_update_file`，**每次都要用户批准**（批准卡由 runtime 弹出，不能代点）

## 步骤
1. 取当前 blob SHA（已有文件必须带 sha）：
   ```
   github call-read-tool --name get_file_contents \
     --arguments-json '{"owner":"majin210","repo":"WindChimes","path":"index.html","ref":"main"}'
   ```
   从返回文本里取 `SHA: <hex>`。
2. 组装参数写 `/tmp/push_args.json`（content 传原文，server 端做 base64）：
   ```json
   {"owner":"majin210","repo":"WindChimes","path":"index.html","branch":"main",
    "sha":"<上一步的sha>","message":"<中文 commit message>","content":"<文件全文>"}
   ```
   大文件用 `$(cat /tmp/push_args.json)` 传参，避免命令行转义问题。
3. 执行：
   ```
   github call-tool --name create_or_update_file --arguments-json "$(cat /tmp/push_args.json)"
   ```
4. 返回的 commit sha 即成功。等 1~2 分钟让 Pages 构建。
5. 验证：`curl -s https://majin210.github.io/WindChimes/ -o /tmp/live.html`，
   确认大小与推送文件一致、`grep` 到本次改动的标记。

## 新文件
- 不存在的 path 直接推送（不带 sha），如 `windchimes-editor.html`。

## 注意事项
- commit message 用中文短句，如"站点更新：新 Logo 上线，联系区/声音按钮造型统一"
- 用户说"同步到 GitHub"即走此流程
- 推送前必须已跑完检查清单且用户确认过效果
