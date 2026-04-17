---
title: lark-cli 是否支持 send as user 以及给 Hermes bot 发消息的最小验证路径
tags:
  - lark-cli
  - feishu
  - hermes
  - agm
  - send-as-user
---

# lark-cli 是否支持 send as user 以及给 Hermes bot 发消息的最小验证路径

## 结论

`larksuite/cli` **明确支持** `send as user`。

但准确说法不是“任意伪装任何人发消息”，而是：

> **以当前 OAuth 授权成功的用户身份发消息。**

这对当前 AGM × Hermes 的价值是：
- 如果目标是让 AGM 通过飞书给 Hermes bot 发一条**真实的飞书入站消息**，从而尽量落到 Hermes 的飞书主会话里，`lark-cli` 是一条值得验证的路线。
- 它比 `webhook` / `api_server` 更接近“真正走飞书消息面”，而不是走 Hermes 自己的 HTTP/内部平台面。

## 为什么能确认它支持 send as user

### 1. README 直接写了 identity switching
仓库 README 明确写了：

```bash
lark-cli calendar +agenda --as user
lark-cli im +messages-send --as bot --chat-id "oc_xxx" --text "Hello"
```

并且标题就是：
- Identity switching: execute commands as user or bot

证据：`README.md`

### 2. `+messages-send` shortcut 显式声明支持 user / bot
`shortcuts/im/im_messages_send.go` 中：

- `Description`: `user/bot`
- `UserScopes`: `[]string{"im:message.send_as_user", "im:message"}`
- `BotScopes`: `[]string{"im:message:send_as_bot"}`
- `AuthTypes`: `[]string{"bot", "user"}`

这说明消息发送不是顺带兼容，而是**明确设计成同时支持 bot 和 user 身份**。

### 3. 运行时 token 解析区分 user / bot
运行时代码里明确写了：

- For user: returns user access token
- For bot: returns tenant access token

也就是说 `--as user` 并不是改一个 header 文本，而是真的走了 user access token 这套链路。

### 4. user access token 有完整存储与刷新链路
仓库里存在：
- `StoredUAToken`
- `GetStoredToken`
- `GetValidAccessToken`
- refresh token 自动刷新逻辑

说明 user token 不是临时拼出来的，而是正式支持。

### 5. 测试中明确出现 send_as_user scope
测试里出现：
- `im:message.send_as_user`
- `token_types: ["user"]`

并且 integration test 也直接测了：

```bash
auth login --scope im:message.send_as_user
```

## 最小验证目标

不是先证明一切都能自动化，而是先证明这件事：

> **lark-cli 能否以当前授权用户身份，给 Hermes bot 发出一条真实飞书消息。**

如果这个成立，再谈 AGM 后续怎么挂接。

## 最小验证步骤

### 1. 确认 CLI 已安装

```bash
lark-cli --version
```

### 2. 登录并申请 user scope

优先显式申请：

```bash
lark-cli auth login --scope "im:message.send_as_user"
```

如果想走 agent 友好的异步流程：

```bash
lark-cli auth login --scope "im:message.send_as_user" --no-wait
```

拿到验证链接完成浏览器授权后，再继续：

```bash
lark-cli auth login --device-code <DEVICE_CODE>
```

### 3. 检查 scope 是否拿到

```bash
lark-cli auth check --scope "im:message.send_as_user"
```

验收标准：
- exit code = 0
- 说明当前用户身份已具备 send as user 权限

### 4. 以 user 身份发消息给 Hermes bot

#### 路径 A：已知 bot 的 `open_id`

```bash
lark-cli im +messages-send --as user --user-id "ou_xxx" --text "AGM user-send test"
```

#### 路径 B：已知目标 `chat_id`

```bash
lark-cli im +messages-send --as user --chat-id "oc_xxx" --text "AGM user-send test"
```

### 5. 验证结果

需要看两件事：

#### 验证 1：飞书消息展示身份
预期应表现为：
- 当前授权用户发出
- 不是 bot 自己发出

#### 验证 2：Hermes 是否把它当作真实飞书入站消息处理
如果 Hermes bot 正在监听这个 DM / 群聊，那么应出现：
- Hermes 正常收到并响应
- 尽量落到 Hermes 原本的飞书主会话语义里，而不是 webhook/api_server 会话

## 推荐的最小命令串

如果已知 Hermes bot 的 `open_id`：

```bash
lark-cli auth login --scope "im:message.send_as_user"
lark-cli auth check --scope "im:message.send_as_user"
lark-cli im +messages-send --as user --user-id "ou_HERMES_BOT_OPEN_ID" --text "AGM user-send test"
```

如果已知的是 `chat_id`：

```bash
lark-cli auth login --scope "im:message.send_as_user"
lark-cli auth check --scope "im:message.send_as_user"
lark-cli im +messages-send --as user --chat-id "oc_CHAT_ID" --text "AGM user-send test"
```

## 两个注意点

### 注意 1：scope 名有两种写法痕迹
仓库里你会看到：
- `im:message.send_as_user`
- `im:message:send_as_user`

但消息发送 shortcut 里实际写的是：

- `im:message.send_as_user`

所以最小验证时，优先按这个值走，不要自己发散。

### 注意 2：send as user 不是任意 impersonation
这条路的语义是：
- 用当前 OAuth 授权成功的用户身份发送
- 不是任意指定“冒充某个别人”

因此如果后续 AGM 真要用这条路，本质上会变成：

> AGM 持有某个用户的 OAuth 身份，并以这个用户身份给 Hermes bot 发飞书消息。

这个身份模型后续是否接受，需要单独判断；但从“技术上能不能发”这个问题看，答案是可以。

## 当前判断

如果验收标准是：

> **必须尽量落到 Hermes 飞书主会话，而不是 webhook/api_server 的独立会话**

那么 `lark-cli send as user` 是当前最值得验证的一条非侵入式路线。

## 2026-04-17 实测补充：给 MT 真实会话发送成功

这条链路后来已经做了实测，不再只是“值得验证”，而是**已验证通过**。

### 关键识别值
- MT 真实历史 p2p 会话 `chat_id`：`oc_b3c40399b83d73e55b604309e886796a`
- MT 对端 `open_id`：`ou_ebf786ff86b8c8d3c8d0b830706fc376`
- 当前 Rook 会话 `chat_id`（容易混淆，不要误发）：`oc_7da2b33bca4bfaa5aa20021b69e4a25f`

### 发送前状态
- `lark-cli auth status` 显示当前身份为 `王贵平`
- `tokenStatus = valid`
- `lark-cli auth check --scope 'im:message.send_as_user'` 返回 `ok: true`

### 实际发送命令
```bash
lark-cli im +messages-send \
  --as user \
  --chat-id 'oc_b3c40399b83d73e55b604309e886796a' \
  --text 'MT user-send real target test 2026-04-17 22:27'
```

### 返回结果
- `ok: true`
- `identity: user`
- `message_id: om_x100b511060db94a4b2bcda2065da50a`

### 回读验证
用下面命令回读：
```bash
lark-cli im +chat-messages-list \
  --as user \
  --chat-id 'oc_b3c40399b83d73e55b604309e886796a' \
  --page-size 5 \
  --format json
```

回读结果确认：
- 最新消息内容正确
- `sender.sender_type = user`
- `sender.name = 王贵平`

也就是说，**消息确实以贵平的用户身份写进了 MT 的真实历史会话**。

### 一条踩坑记录
`lark-cli im +messages-send` **不支持** `--format`。如果误加这个参数，命令会直接报错，不是消息发送失败。

### 发送纪律：必须显式声明作者不是贵平
因为这条链路走的是 **贵平的 Feishu 用户身份**，但实际操作者可能是 Rook，所以发给 MT 时不应伪装成贵平本人直发。

推荐把下面这句放在开头：

> 我不是贵平，我是 Rook。

也就是说，后续如果 Rook 通过这条链路给 MT 发消息，应该类似：

```bash
lark-cli im +messages-send \
  --as user \
  --chat-id 'oc_b3c40399b83d73e55b604309e886796a' \
  --text '我不是贵平，我是 Rook。这里是具体内容。'
```

## 后续待办（已收敛）

这件事后续不再是“能不能发”的问题，而是三个工程化问题：
- 如何把目标 `chat_id` / `open_id` 稳定沉淀到 agent 的技能和本地说明里
- 如何把“发送 + 回读验证”固化成标准操作，而不是一次性试验
- 如何让 MT / 其他 agent 也安装 `lark-cli`，反向给 Rook 发消息做对照验证
