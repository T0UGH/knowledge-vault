# 2026-03-30 AGM Plugin：用飞书消息替代 OpenClaw inject 的可行性判断

## 背景

今天在推进 `agent-git-mail` 的 OpenClaw plugin 验证时，已经证明了：

- AGM 本体（CLI / repo / remote）在 Docker + 真实远程 GitHub 仓库下可用
- AGM plugin 已经能检测新信，并调用 OpenClaw 宿主边界：
  - `new_mail_detected`
  - `deliver_prepare`
  - `enqueue_done`
  - `heartbeat_requested`

但“**真实 session 内可见提醒**”这一步还没有闭环，当前卡点在 Docker 内 OpenClaw 宿主的 active session runtime / system event consumption，不在 AGM 本体逻辑。

因此出现一个替代思路：

> 如果 OpenClaw inject / system event → session transcript 这条链过重，是否可以直接改成用飞书消息做通知？

---

## 本次要回答的问题

问题不是“飞书能不能发消息”，而是更具体地问：

> **一个机器人/应用，能不能用“用户身份”给另一个机器人直接发消息？**

如果可以，那么 AGM plugin 也许不需要依赖 OpenClaw inject，可以直接走 Feishu 消息通知。

---

## 查到的事实

## 1. `feishu-cli` 明确支持“用户身份发消息”

本机安装的 CLI：

- 路径：`/usr/local/bin/feishu-cli`
- 仓库：`https://github.com/riba2534/feishu-cli`

`feishu-cli msg send --help` 明确支持：

- 默认 app / bot 身份
- 可显式传：`--user-access-token`

即：

```bash
feishu-cli msg send \
  --receive-id-type email \
  --receive-id user@example.com \
  --text "你好" \
  --user-access-token <token>
```

说明它**确实支持以用户授权 token 的身份发消息**。

---

## 2. 从 `feishu-cli` 源码看，消息 API 目标仍然是“用户或群”

相关源码：

- `cmd/send_message.go`
- `internal/client/message.go`

关键点：

- 发送消息最终走的是：`im/v1/messages`
- `SendMessage(receiveIDType, receiveID, msgType, content, userAccessToken)`
- 允许注入 `userAccessToken`

但 `receive-id-type` 的可选值只有：

- `email`
- `open_id`
- `user_id`
- `union_id`
- `chat_id`

**没有**：

- `bot_id`
- `app_id`
- `webhook_id`
- 任何“另一个机器人”作为直连收件人的类型

这意味着飞书消息 API 的标准收件模型仍然是：

> **发给用户** 或 **发给群**

而不是“发给另一个机器人”。

---

## 3. 从 `feishu-cli` 文档语义看，也更像“用户/群聊上下文”

在 `feishu-cli` 的技能文档里还看到一条提示：

> `Bot/User can NOT be out of the chat`

这说明它的消息语义仍然是：

- bot 或 user 在某个 chat 里发/读消息
- 而不是“把 bot 当成一个普通可寻址用户来私聊”

也就是说，平台设计更偏：

- **用户 ↔ 用户 / 群**
- **bot ↔ 群 / 用户**

而不是：

- **bot ↔ bot 直接私聊**

---

## 当前判断

## 能做的

### A. 用用户身份给“真实用户”发通知

**可以。**

这是 Feishu 标准支持路径。

### B. 给某个群发通知

**可以。**

如果两个机器人都在同一个群里，那么“通过群消息中转”是有可能成立的。

### C. 用用户身份直接给“另一个机器人”发私聊

**从当前 CLI 和 API 证据看，不像支持，也不应默认假设支持。**

至少目前没有看到官方/CLI 暴露出“bot 作为 receive target”的标准收件模型。

---

## 工程结论

### 结论 1

> **飞书可以“以用户身份发消息”，但标准接收对象是用户或群，不是另一个机器人。**

### 结论 2

如果后续要绕开 OpenClaw inject，优先考虑的不是“bot-to-bot 私聊通知”，而是：

1. **发给真实用户**（最稳）
2. **发到双方机器人都在的群**（可尝试）

### 结论 3

当前阶段**不建议把“直接发给另一个机器人”当成默认主方案**，除非后续拿到明确平台证据或真实实验跑通。

---

## 对 AGM / OpenClaw 方向的启发

如果未来要给 AGM plugin 加一个“通知兜底策略”，推荐优先级可以是：

1. OpenClaw inject / system event（原生路径）
2. Feishu 发给真实用户
3. Feishu 发到中转群
4. 最后才考虑 bot-to-bot 直聊（当前不作为默认）

这样更符合平台能力边界，也更容易做成可维护方案。
