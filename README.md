# web-access skill

给 Claude Code（及任何支持 skill 的 Agent）装上完整的联网能力。

Agent 原本就有 WebSearch、WebFetch、Playwright MCP 这些工具，但没有调度策略——不知道什么时候用哪个，也没有激活 CDP 登录态复用这个最强能力。这个 skill 补上的正是这一层：**策略 + 解锁**。

## 能力

- **三层通道自动调度**：WebSearch → Jina/WebFetch → 浏览器 CDP，Agent 根据任务性质自主选择最轻的直达方案
- **浏览器 CDP 模式**：直连用户日常 Chrome，天然携带登录态，无需另起浏览器，支持动态页面、交互操作、视频截帧
- **并行分治**：多个独立调研目标时，主 Agent 分发子 Agent 并行执行，总耗时约等于单个子任务时长，上下文不污染
- **图片 / 视频提取**：从 DOM 直取媒体 URL，或对视频任意时间点截帧分析

## 依赖

- **Node.js 22+**（CDP Proxy 使用原生 WebSocket）
- **Chrome**（开启 Remote Debugging）

## 安装

直接让 Claude 安装：

```
帮我安装 web-access skill：https://github.com/eze-is/web-access
```

Claude 会自动读取仓库、拷贝文件到 `~/.claude/skills/web-access/`，并在 `settings.json` 中注册 skill。

手动安装：

```bash
git clone https://github.com/eze-is/web-access ~/.claude/skills/web-access
```

## 开启 Chrome Remote Debugging

在 Chrome 地址栏打开 `chrome://inspect/#remote-debugging`，勾选 **Allow remote debugging for this browser instance**（可能需要重启浏览器）。

## 使用

安装后无需手动操作，直接让 Agent 执行联网任务即可：

- "帮我搜索 xxx 最新进展"
- "读一下这个页面的内容：[URL]"
- "登录后帮我抓取小红书上关于 xxx 的评论"
- "同时调研这 5 个产品的官网，给我对比摘要"

Agent 会自动判断通道、启动 CDP Proxy（如需要）、执行任务、清理 tab。

## 检查环境

```bash
bash ~/.claude/skills/web-access/scripts/check-deps.sh
```

## License

MIT · 作者：[一泽 Eze](https://github.com/eze-is)
