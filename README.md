# AI 在线版提示词

为 DeepSeek、腾讯元宝提供请求级系统提示词与时间上下文注入的油猴脚本。

它不是在输入框里塞模板，而是在请求发出前把提示词拼进发给模型的正文；注入块会在界面上隐藏。

## 支持的站点

- DeepSeek：`https://chat.deepseek.com`
- 腾讯元宝：`https://yuanbao.tencent.com`

## 功能

- 请求级注入：提示词作为用户消息的一部分发给模型，不依赖页面上的输入框。
- 每个对话只注入一次；提示词变更后会自动完整重注入。
- 闲置超过 1 小时，下一条消息会带上上一条消息的时间和间隔；重复提示词最多每 2 小时一次。
- 每次注入都附带当前 UTC 时间、本地时区和时间上下文。
- DeepSeek 和元宝共用同一份提示词与开关。
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

脚本菜单里有四项：

- 设置系统提示词
- 清除系统提示词
- 切换系统提示词
- 查看当前系统提示词

提示词和开关在所有支持的站点之间共用。设置后，新对话的第一条消息会带上提示词；同一对话后续消息不重复注入，除非提示词被修改，或者闲置超过 1 小时且已过 2 小时冷却。

## 工作原理

- 在 `document-start` 拦截页面的 `XMLHttpRequest` 与 `fetch`。
- DeepSeek：读取 `/api/v0/chat/completion` 等请求体里的 `prompt` 字段。
- 元宝：读取 `POST /api/chat/<会话 id>` 请求体里的 `prompt` 字段；`displayPrompt` 保持原样，所以界面显示不受影响。
- 只处理真正的对话请求；元宝的 `/api/chat/prechat`、`/api/chat/title`、`/api/chat/repeat` 不会被修改。

## 隐私

- 提示词、开关和对话时间记录保存在脚本管理器（GM storage）里，只在本机。
- 脚本不向第三方服务器发送数据，没有统计和上报。
- 提示词会作为对话内容发送给你正在使用的 AI 站点，并保存在该站点的会话历史里；这是使用该功能的必然结果。

## 已知限制

- 只支持上面两个站点。
- 站点前端改版后可能失效；最后验证时间：2026-11-22。
- 不提供绕过、越狱或去除限制的能力，只注入你自己写的提示词和时间上下文。
- 元宝本身有「对话分组提示词」等官方能力；这个脚本的用途是跨站点共用同一份提示词，并补充时间上下文。
- 页面隐藏是尽力而为；如果站点在导出或分享时直接读取原始 `prompt`，仍可能看到注入块。
- 仅支持浏览器 + 脚本管理器，不支持手机 App。

## 许可与致谢

MIT License，见 [LICENSE](LICENSE)。

基于 NoahTheGinger 的 [DeepSeek System Prompt Injector](https://greasyfork.org/en/scripts/542424-deepseek-system-prompt-injector)（MIT）修改，新增元宝支持、时间上下文、闲置提醒和多站点共用设置。修改部分版权归 CaelLab（虚舟实验室）与 Yunyun。

## 反馈

联系方式见 [CaelLab 联系页](https://caellab.org/who-we-are/contact/)。
