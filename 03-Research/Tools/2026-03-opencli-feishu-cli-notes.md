---
title: opencli 与 feishu-cli 记录
date: 2026-03-28
tags:
  - tools
  - opencli
  - feishu-cli
  - feishu
  - openclaw
  - research
---

# opencli 与 feishu-cli 记录

## TL;DR

这两个工具的定位不一样：

- `opencli`：把网站 / Electron App / 本地工具变成 CLI，偏 **浏览器登录态复用 + agent 可调用的网站操作层**
- `feishu-cli`：飞书开放平台 CLI，偏 **飞书文档 / 日历 / 消息 / 权限 / 文件 / 搜索 等官方 API 操作层**

我的判断：

- 如果目标是 **让 agent 能直接操作 B站 / X / YouTube / Reddit / 知乎 / 微博等网站**，优先看 `opencli`
- 如果目标是 **系统化操作飞书文档、日历、消息、权限、知识库**，优先看 `feishu-cli`
- 这两个不是互斥关系，而是两层能力：`opencli` 更像网页能力桥，`feishu-cli` 更像飞书官方 API CLI

---

## 1. opencli

### 是干什么的

`opencli` 的核心思路是：

> 把网站、Electron App、甚至本地 CLI，统一包装成一个可脚本化、可被 AI agent 调用的命令行接口。

它的关键价值：

- 复用 Chrome 登录态，不需要额外重新登录
- 很多场景不走传统 API key，而是直接借助浏览器会话工作
- 对 agent 很友好，可以直接在命令行里调用
- 适合做信息抓取、搜索、浏览、部分发帖/交互动作

### GitHub 仓库

- GitHub: <https://github.com/jackwener/opencli>
- 本地 clone 路径：`/Users/wangguiping/.openclaw/workspace/external/opencli`

### 怎么下载 / 安装

#### 官方安装

```bash
npm install -g @jackwener/opencli
```

验证：

```bash
opencli --version
opencli doctor
```

### 本次本机安装结果

已经安装成功：

- 命令路径：`/usr/local/bin/opencli`
- 版本：`1.5.2`

### 使用前置条件

`opencli` 不是装完就完事，它依赖 **Chrome Browser Bridge 扩展**。

大致步骤：

1. 安装 CLI：`npm install -g @jackwener/opencli`
2. 从 release 下载 `opencli-extension.zip`
3. 到 `chrome://extensions` 打开开发者模式
4. `Load unpacked` 加载解压后的扩展目录
5. 运行 `opencli doctor` 验证

### 相关下载地址

- Releases: <https://github.com/jackwener/opencli/releases>
- 我本次使用的 release 资产：`opencli-extension.zip`

### 常见用法

#### 看 B 站热门

```bash
opencli bilibili hot --limit 10 -f json
```

#### 搜 YouTube

```bash
opencli youtube search --query "LLM tutorial"
```

#### 看 Hacker News

```bash
opencli hackernews top --limit 20 -f json
```

#### 搜 Twitter / X

```bash
opencli twitter search --query "claude AI"
```

### 实测通过的命令

本机已实测通过：

```bash
opencli bilibili hot --limit 1 -f json
```

说明当前本机的 `opencli + Chrome 扩展` 已经接通。

### 这玩意的坑点

#### 坑 1：CLI 装好了，不代表能用

最容易踩的坑就是：

- `opencli` CLI 已经装好
- 但 Chrome 扩展没装 / 没连上
- `opencli doctor` 会报扩展未连接

所以正确验收方式不是只看 `opencli --version`，而要看：

```bash
opencli doctor
```

#### 坑 2：扩展装上了，daemon 也可能没起来

本次安装过程中，扩展里出现过：

```text
WebSocket connection to 'ws://localhost:19825/ext' failed
net::ERR_CONNECTION_REFUSED
```

这说明扩展已经在尝试连接，但本地 daemon 还没就绪。

后面重新跑 `opencli doctor` 后，daemon 被拉起，问题消失。

#### 坑 3：当前这台机器/Chrome 组合上，扩展默认实现有兼容性问题

本次最关键的坑：

- 扩展虽然连接成功了
- 但实际执行命令时报：`Invalid value for state`

根因是扩展内部创建自动化窗口时用了：

```js
state: "minimized"
```

在这台机器当前 Chrome 环境里，这个值会报错，导致：

- `opencli doctor` 连通性失败
- `opencli bilibili hot ...` 之类的命令无法执行

#### 本次本机 workaround

对本地 unpacked 扩展做了一个补丁：

- 去掉 `chrome.windows.create(...)` 里的 `state: "minimized"`
- 然后在 Chrome 扩展页手动 reload 扩展

补丁位置（本地临时目录）：

- `/Users/wangguiping/.openclaw/workspace/external/opencli-extension/unpacked/dist/background.js`

#### 坑 4：这是本地 patch，不是长期稳定解法

也就是说：

- 当前机器上能用
- 但如果升级扩展、重新下载 release、或换机器，这个 patch 可能丢失
- 以后如果再装 opencli，要优先记住这个兼容性点

### 适合什么场景

- 让 agent 访问公开网页 / 半公开网页
- 借助 Chrome 登录态读取内容网站
- 做 AI 情报抓取、热榜抓取、社媒搜索
- 统一 agent 对网站的调用入口

### 不适合什么场景

- 对稳定性和长期自动化要求极高的生产链路
- 浏览器扩展不方便部署的纯服务器环境
- 对“浏览器兼容性波动”容忍度低的场景

---

## 2. feishu-cli

### 是干什么的

`feishu-cli` 是一个 **飞书开放平台 CLI**。

它不是浏览器自动化，而是直接走飞书官方 API，覆盖：

- 文档
- 知识库
- 文件
- 权限
- 消息
- 日历
- 任务
- 搜索
- 画板
- 评论
- 用户 / 部门 等

它的核心亮点之一是：

- Markdown ↔ 飞书文档双向转换
- 面向 AI Agent 的飞书操作能力比较完整

### GitHub 仓库

- GitHub: <https://github.com/riba2534/feishu-cli>
- 本地 clone 路径：`/Users/wangguiping/.openclaw/workspace/external/feishu-cli`

### 怎么下载 / 安装

#### 官方推荐安装

```bash
curl -fsSL https://raw.githubusercontent.com/riba2534/feishu-cli/main/install.sh | bash
```

#### 也可以 go install

```bash
go install github.com/riba2534/feishu-cli@latest
```

### 本次本机安装结果

本次安装结果：

- 安装脚本检测平台：`darwin/amd64`
- 安装版本：`v1.12.0`
- 实际安装落点：`/Users/wangguiping/go/bin/feishu-cli`

为了让系统直接可用，又补了一步：

```bash
ln -sf /Users/wangguiping/go/bin/feishu-cli /usr/local/bin/feishu-cli
```

所以当前直接可用命令：

```bash
feishu-cli
```

### 配置方式

需要飞书应用凭证：

- `FEISHU_APP_ID`
- `FEISHU_APP_SECRET`

两种常见方式：

#### 方式 1：环境变量

```bash
export FEISHU_APP_ID="cli_xxx"
export FEISHU_APP_SECRET="xxx"
```

#### 方式 2：配置文件

```yaml
# ~/.feishu-cli/config.yaml
app_id: "cli_xxx"
app_secret: "xxx"
```

本机当前已采用配置文件方式。

### 当前本机已验证通过的能力

#### 1）创建飞书文档

```bash
feishu-cli doc create --title "测试文档"
```

本次已成功创建测试文档。

#### 2）给文档加权限

```bash
feishu-cli perm add <doc_token> \
  --doc-type docx \
  --member-type open_id \
  --member-id <open_id> \
  --perm full_access
```

本次已成功给当前飞书账号加上文档 `full_access`。

#### 3）创建飞书日程

```bash
feishu-cli calendar create-event \
  --calendar-id <calendar_id> \
  --summary "测试会议" \
  --start "2026-03-28T15:00:00+08:00" \
  --end "2026-03-28T15:30:00+08:00"
```

本次已成功创建测试会议。

#### 4）给日程加参与人

```bash
feishu-cli calendar attendee add <calendar_id> <event_id> --user-ids <open_id>
```

本次已成功把当前用户加入测试会议。

### 常见用法

#### 创建文档

```bash
feishu-cli doc create --title "新文档"
```

#### Markdown 导入文档

```bash
feishu-cli doc import report.md --title "技术报告"
```

#### 导出文档为 Markdown

```bash
feishu-cli doc export <doc_id> -o output.md
```

#### 添加协作者权限

```bash
feishu-cli perm add <doc_token> \
  --doc-type docx \
  --member-type open_id \
  --member-id <open_id> \
  --perm edit
```

#### 获取主日历

```bash
feishu-cli calendar primary
```

#### 创建日程

```bash
feishu-cli calendar create-event \
  --calendar-id <calendar_id> \
  --summary "会议" \
  --start "2026-03-28T15:00:00+08:00" \
  --end "2026-03-28T15:30:00+08:00"
```

#### 给日程加参与人

```bash
feishu-cli calendar attendee add <calendar_id> <event_id> --user-ids <open_id>
```

### 这玩意的坑点

#### 坑 1：安装脚本装完了，不一定在 PATH 里

本次就踩到了：

- 安装脚本执行成功
- 但二进制装到了 `~/go/bin/feishu-cli`
- 当前 shell 的 PATH 没带 `~/go/bin`
- 所以会出现“装好了但命令找不到”

workaround：

```bash
ln -sf /Users/wangguiping/go/bin/feishu-cli /usr/local/bin/feishu-cli
```

#### 坑 2：配置好 App ID / Secret 后，也要看具体 API 权限

不是说填了 `app_id/app_secret` 就全都能用。

飞书开放平台应用权限如果没开够，会出现：

- 某些命令能用
- 某些命令因为 scope 不够失败

所以后面如果某个子命令报权限问题，优先检查：

- 应用有没有开对应 scope
- 是不是需要 User Access Token，而不是仅 tenant access token

#### 坑 3：calendar create-event 依赖有效 calendar_id

本次第一次创建日程失败，报：

```text
invalid calendar_id
```

根因不是 API 权限，而是从 `calendar primary` 输出里抓 ID 时，没有把前导空格清干净。

也就是说：

- `calendar primary` 人类读没问题
- 但如果在 shell 里直接粗暴解析文本输出，容易出错

经验：

- 需要自动化脚本时，优先找 `-o json` 一类输出
- 如果没有 JSON，就手动 trim 掉空格

#### 坑 4：参与人添加命令参数名叫 user-ids，但这里传的是飞书 open_id

命令名看着像 `user-ids`，但在我们这次实测里，传入当前飞书身份的 open_id 是可工作的。

这说明：

- 文档里“user id / open id / user ids”这些身份字段在不同子命令之间并不完全统一
- 自动化时最好先用一个真实账号试通，不要过度想当然

### 适合什么场景

- 系统化操作飞书文档 / 日历 / 消息 / 权限 / 文件
- 用 CLI 接入飞书工作流
- 给 agent 一个正式、结构化的飞书 API 操作层
- 做 Markdown 与飞书文档互转

### 不适合什么场景

- 没有飞书开放平台应用权限的纯临时使用者
- 不愿意处理 app 权限、scope、OAuth 配置的人
- 想直接“像浏览器那样代操作一切飞书界面”的场景（它本质还是 API CLI，不是前端 UI 自动化）

---

## opencli vs feishu-cli

| 维度 | opencli | feishu-cli |
|---|---|---|
| 核心模式 | 浏览器登录态复用 / 网站 CLI 化 | 飞书官方 API CLI |
| 主要对象 | 各类网站、Electron App、本地工具 | 飞书生态 |
| 登录方式 | 依赖 Chrome 扩展 + 浏览器状态 | 依赖 App ID / Secret / OAuth |
| 优势 | 快速接网站、对 agent 友好、跨平台站点覆盖广 | 飞书能力完整、结构化强、适合正式工作流 |
| 稳定性 | 受 Chrome / 扩展兼容性影响较大 | 取决于飞书 API 权限与配置，整体更正规 |
| 当前本机状态 | 已装好，已打本地 patch，可用 | 已装好，已配置凭证，可用 |

### 当前结论

如果后面要把能力接到 Agent / OpenClaw / Obsidian / Mailbox 体系里：

- `opencli` 更适合做 **互联网信息入口**
- `feishu-cli` 更适合做 **飞书执行层 / 工作流落地层**

一个偏“看外部世界”，一个偏“操作飞书工作台”。

---

## 本机状态摘要（2026-03-28）

### opencli

- 已安装
- 可执行命令：`opencli`
- 已完成 Chrome 扩展接入
- 已通过 `opencli doctor`
- 已实测 B 站热门命令可用
- 注意：当前机器存在扩展 `state: "minimized"` 兼容性坑，靠本地 patch 解决

### feishu-cli

- 已安装
- 可执行命令：`feishu-cli`
- 已配置 app_id / app_secret
- 已实测创建文档
- 已实测给文档加权限
- 已实测创建日程
- 已实测给日程加参与人

---

## 后续建议

### 对 opencli

建议补一条本机安装 SOP：

1. 安装 CLI
2. 下载扩展
3. 加载 unpacked 扩展
4. 若报 `Invalid value for state`，检查并移除 `state: "minimized"`
5. 跑 `opencli doctor`
6. 用一个实际命令验收

### 对 feishu-cli

建议后续再补三类内容：

1. **最小权限模板**：只为文档 / 日历 / 消息开最少 scopes
2. **常用命令清单**：文档、权限、日历、消息四类高频命令单独整理
3. **自动化接入策略**：后面评估是直接让 OpenClaw 调 `feishu-cli`，还是继续优先走现有 Feishu skill

---

## 相关链接

- opencli GitHub: <https://github.com/jackwener/opencli>
- opencli releases: <https://github.com/jackwener/opencli/releases>
- opencli skill: <https://github.com/joeseesun/opencli-skill>
- feishu-cli GitHub: <https://github.com/riba2534/feishu-cli>
- HappyClaw GitHub: <https://github.com/riba2534/happyclaw>
