---
domain: x.com
aliases: [推特, Twitter, X]
updated: 2026-03-19
---
## 平台特征
SPA 架构（React），富文本编辑器基于 Draft.js。已登录用户的 Chrome 天然携带登录态，无需额外处理。

## 有效模式
- 发帖页：`/compose/post`，直接 `/new` 打开即可
- 编辑器选择器：`[data-testid="tweetTextarea_0"]`
- 发帖按钮：`[data-testid="tweetButton"]`（用 `/eval` + `element.click()`）
- 个人主页：`/{username}`

## 长文本输入
Draft.js 编辑器对程序化文本输入有兼容问题：
- `document.execCommand('insertText')` 长文本会截断，只保留最后一段
- `ClipboardEvent` 模拟粘贴会丢失开头段落
- **可靠方案**：`pbcopy` 写入系统剪贴板 → `osascript` 模拟 `Cmd+V` 粘贴（macOS）。需要先 `editor.focus()` 确保焦点在编辑框内

## 已知陷阱
- `/click` 端点现已支持（Input.dispatchMouseEvent），但推特发帖按钮用 `/eval` + `querySelector().click()` 即可
