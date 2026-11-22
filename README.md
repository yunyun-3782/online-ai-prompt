# AI 在线版提示词

为 DeepSeek、腾讯元宝、千问、千问办公、灵光、Kimi、豆包提供请求级系统提示词与时间上下文注入的油猴脚本。

它不是在输入框里塞模板，而是在请求发出前把提示词拼进发给模型的正文；注入块会在界面上隐藏。

## 支持的站点

- DeepSeek：`https://chat.deepseek.com`
- 腾讯元宝：`https://yuanbao.tencent.com`
- 千问：`https://www.qianwen.com`
- 千问办公（QwenWork）：`https://qwenwork.cn`
- 灵光：`https://www.lingguang.com`
- Kimi：`https://www.kimi.com`、`https://kimi.moonshot.cn`
- 豆包：`https://www.doubao.com`

## 功能

- 请求级注入：提示词作为用户消息的一部分发给模型，不依赖页面上的输入框。
- 每个对话只注入一次；提示词变更后会自动完整重注入。
- 闲置超过 1 小时，下一条消息会带上上一条消息的时间和间隔；重复提示词最多每 2 小时一次。
- 每次注入都附带当前 UTC 时间、本地时区和时间上下文。
- 所有站点共用同一份提示词与全局开关。
- 可以在脚本菜单里单独停用某个 AI 助手（如「停用对 DeepSeek 的提示词支持」），随时启用回来；停用期间该站点的请求完全不会被改写。
- 注入块用 `[caellabonprompt]` / `[/caellabonprompt]` 包起来，页面渲染时隐藏。
- 旧版 DeepSeek 专用脚本的提示词与开关会自动迁移。

## 安装

1. 安装 [Tampermonkey](https://www.tampermonkey.net/) 或 [Violentmonkey](https://violentmonkey.github.io/)。
2. 安装脚本：
   - GitHub（推荐）：[在线安装 / 下载 latest](https://github.com/yunyun-3782/online-ai-prompt/releases/latest/download/online-ai-prompt.user.js)，脚本管理器会自动弹出安装。
   - Greasy Fork：发布后搜索「AI 在线版提示词」（直链待补）。
   - 或在本仓库下载 [`online-ai-prompt.user.js`](https://github.com/yunyun-3782/online-ai-prompt/blob/main/online-ai-prompt.user.js)，在脚本管理器里新建脚本并粘贴全文。
3. 打开支持的站点，在脚本管理器菜单里设置系统提示词。

## 使用

脚本菜单里先有四项全局操作：

- 设置系统提示词
- 清除系统提示词
- 切换系统提示词
- 查看当前系统提示词

后面每个支持的站点各有一项开关，按当前状态显示为「停用对 XXX 的提示词支持」或「启用对 XXX 的提示词支持」。停用和启用都是立即生效的；菜单文字要刷新页面后才会更新。

提示词和全局开关在所有支持的站点之间共用。设置后，新对话的第一条消息会带上提示词；同一对话后续消息不重复注入，除非提示词被修改，或者闲置超过 1 小时且已过 2 小时冷却。

## 工作原理

- 在 `document-start` 拦截页面的 `XMLHttpRequest`、`fetch` 与 `WebSocket`。
- DeepSeek / 腾讯元宝：读取对话请求体里的 `prompt` 字段。
- 千问：读取 `POST /api/v2/chat` 请求体里最后一条 user 消息。
- 千问办公：读取 WebSocket 里 `session/prompt` 的 prompt 文本。
- 灵光：读取 `POST /chat/stream_chat` 请求体里的 `query` 字段，只处理普通对话场景（`sceneCode` 为 `WEB`）。
- Kimi：解析 Connect-RPC 的 5 字节帧头后，修改帧内 JSON 的 `message.blocks[].text.content`；旧版 `/api/chat/<id>/completion` 走 messages 处理。
- 豆包：读取 `/chat/completion` 等对话请求里最后一条 user 消息，兼容 `content` 字符串、JSON 字符串和 `content_block` 结构。
- 只处理真正的对话请求；元宝的 `/api/chat/prechat`、`/api/chat/title`、`/api/chat/repeat` 不会被修改。

## 隐私

- 提示词、开关和对话时间记录保存在脚本管理器（GM storage）里，只在本机。
- 脚本不向第三方服务器发送数据，没有统计和上报。
- 提示词会作为对话内容发送给你正在使用的 AI 站点，并保存在该站点的会话历史里；这是使用该功能的必然结果。

## 已知限制

- 只支持上面列出的七个站点。
- 请求格式最后核对时间：2026-11-22；站点前端改版后可能失效。
- 不提供绕过、越狱或去除限制的能力，只注入你自己写的提示词和时间上下文。
- 元宝本身有「对话分组提示词」等官方能力；这个脚本的用途是跨站点共用同一份提示词，并补充时间上下文。
- 页面隐藏是尽力而为；如果站点在导出或分享时直接读取原始正文，仍可能看到注入块。
- 仅支持浏览器 + 脚本管理器，不支持手机 App。

## 许可与致谢

MIT License，见 [LICENSE](LICENSE)。

基于 NoahTheGinger 的 [DeepSeek System Prompt Injector](https://greasyfork.org/en/scripts/542424-deepseek-system-prompt-injector)（MIT）修改，新增元宝、千问、千问办公、灵光、Kimi、豆包支持，以及时间上下文、闲置提醒、多站点共用设置和站点级开关。修改部分版权归 CaelLab（虚舟实验室）与 Yunyun。

## 反馈

联系方式见 [CaelLab 联系页](https://caellab.org/who-we-are/contact/)。
