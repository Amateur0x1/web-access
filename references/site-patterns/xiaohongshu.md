---
domain: xiaohongshu.com
aliases: [小红书, xhs, RED]
updated: 2026-03-19
---
## 平台特征
SPA 架构，反爬严格，程序化请求容易被拦。公开内容也需 CDP 访问。

## 有效模式
- 搜索：直接构造 `/search_result?keyword={query}` 可到达搜索结果页，支持 `&type=user` 筛选用户
- 用户主页：`/user/profile/{id}`
- 搜索结果中的用户卡片在 `a[href*="/user/profile/"]` 中
- 查找特定用户：直接 `/new` 打开 `search_result?keyword={name}&type=user` → 提取 profile URL → 导航

## 创作者平台发布图文

发布入口：`https://creator.xiaohongshu.com/publish/publish`（需扫码登录，与主站登录态不通）

### 流程
1. 切换到「上传图文」tab：`.creator-tab`（文字匹配"上传图文"），用 JS `el.click()` 即可
2. 上传图片：用 `/setFiles` 设置 `input[type=file].upload-input`，支持多文件
3. 填标题：`input.d-text[placeholder*="标题"]`，用 `nativeSetter.call(input, text)` + `dispatchEvent(new Event("input"))` 触发 React 更新。填完后读取页面上的字数计数器（如 `N/20`）确认是否超限，不要自行计算字符数（中英文、空格的计算规则以页面显示为准）
4. 填正文：`.tiptap.ProseMirror`（TipTap 编辑器），`focus()` 后 `document.execCommand("insertText")` 写入。同样以页面字数计数器（`N/1000`）为准判断是否超限
5. 话题标签：**尚未解决**。正文末尾追加 `#标签名` 文字不会触发话题关联——编辑器的话题检测可能依赖逐字键盘事件而非 insertText 批量写入。当前只能显示为纯文本，不是真正的话题标签。待验证：逐字 `Input.dispatchKeyEvent` 或通过话题搜索框操作
6. 发布按钮：`button.bg-red.d-button:not(.upload-button)`，用 `/click`（dispatchMouseEvent）或 `/eval` + `el.click()`

### 注意事项
- 字数限制以页面计数器显示为准（标题 `N/20`、正文 `N/1000`），不要自行计算——平台的中英文、空格计算规则可能与 JS `.length` 不同
- 图片上传不能用 JS click 触发文件对话框（按钮内部二次 `.click()` 隐藏 file input 不算用户手势），`/setFiles`（DOM.setFileInputFiles）是唯一可靠方式

## 已知陷阱
- 短时间密集打开页面会触发风控
- 创作者平台与主站登录态独立，需单独扫码登录
