---
title: AGM × Hermes × 飞书触发路径比较与当前暂缓结论
tags:
  - agm
  - hermes
  - feishu
  - lark-cli
  - feishu-cli
  - trigger-design
---

# AGM × Hermes × 飞书触发路径比较与当前暂缓结论

## 当前结论

当前这轮探索里，已经明确几件事：

1. **Hermes 现成没有一个优雅的 mailbox ingress / inject-to-existing-session 命令入口**
2. `webhook` / `api_server` 虽然都能接外部触发，但**都不会自然落到飞书主会话**
3. `lark-cli` 与本机已有 `feishu-cli` 都能走到“以用户身份发飞书消息”的方向，但这条路当前看起来**不够优雅**
4. 因此当前最合理的状态不是继续硬接，而是：

> **先暂停这条实现路线，后面重新想更优雅的方案。**

## 为什么暂停

不是因为完全做不到，而是因为当前候选路径都带着明显妥协：

- `webhook`：本质是 webhook session，不是飞书主会话
- `api_server`：本质是 API session，不是飞书主会话
- `lark-cli send as user`：技术上可行，但变成“让外部工具模拟一条用户飞书消息”，工程气质不够干净
- `feishu-cli`：也能走 user token 路，但身份模型更偏补丁式，不是特别适合当长期方案

核心不满意点不是“有没有路”，而是：

> **这些路都不像一个结构清楚、长期可维护的宿主接入方案。**

## 已确认的判断

### 1. Hermes 现有入口判断

#### `webhook`
- 可以非侵入式接入
- 外部 POST 后能转成 `MessageEvent`
- 但默认是 **每个 webhook delivery 一个独立 session**
- 不适合作为“落飞书主会话”的正式方案

#### `api_server`
- 可以非侵入式接入
- 可以用固定 `session_id` / `X-Hermes-Session-Id` 维持 API 侧会话连续性
- 但它形成的是 **API session**，不是 Hermes 的飞书主会话
- 更像后台 worker session，不像真实飞书宿主线程

#### `send_message` tool
- Hermes 内部确实有完整的跨平台发送能力
- 但它是 **Hermes 向外发消息** 的 tool，不是给 AGM 这类外部系统做 ingress 的入口
- 没有官方 `hermes send ...` 或 `/send` 这种产品化命令

### 2. `lark-cli` 判断

已确认：`lark-cli` **明确支持** `send as user`

关键证据：
- README 明写 `--as user / --as bot`
- `im +messages-send` 显式声明：
  - `AuthTypes: user, bot`
  - `UserScopes: im:message.send_as_user`
  - `BotScopes: im:message:send_as_bot`
- user access token 存储、刷新、scope 测试链完整

所以从“技术上能不能以当前授权用户身份发飞书消息”这个问题看，答案是可以。

### 3. 本机 `feishu-cli` 判断

本机已有 `feishu-cli`（来源：`github.com/riba2534/feishu-cli`）也支持 user token 路径：

- `msg send` 命令存在 `--user-access-token`
- 内部会走 `resolveOptionalUserToken(cmd)`
- `internal/auth/resolve.go` 里有完整的 user access token 解析与刷新逻辑

但它的身份模型更像：

> 默认 app/bot token，必要时插入 user access token 覆盖

而不是像 `lark-cli` 那样把 `user/bot` 身份切换做成一等模型。

## `lark-cli` vs `feishu-cli` 当前判断

| 维度 | `lark-cli` | `feishu-cli` |
|---|---|---|
| send as user 支持 | 强，显式设计 | 有，但偏覆盖式 |
| 身份模型 | `--as user / --as bot` | 主要靠 user token 覆盖 |
| 对当前实验贴合度 | 高 | 中 |
| 文档/Markdown 能力 | 有，但不是主卖点 | 强 |
| 当前更适合做 send-as-user 验证 | 是 | 备选 |

当前判断：

> 如果只是为了验证 send-as-user，这轮优先应选 `lark-cli`；但由于整体触发路径依然不够优雅，目前不继续往前落实现。 

## 当前不满意的本质

当前最关键的不满，不在某个具体 CLI 好不好用，而在于：

> **我们在用“外部用户消息模拟”去补一个本该属于宿主 runtime 接口的问题。**

这会带来几个长期隐患：
- 身份模型别扭
- 后续调试路径绕
- 权限与 token 生命周期会进入系统主路径
- 方案看起来能跑，但边界不够干净

所以现在停下来是对的。

## 当前状态交接

### 目标
找到 AGM 触发 Hermes 的更优雅方案，最好既不侵入 Hermes，又不需要靠模拟飞书用户消息兜底。

### 当前状态
- 已确认 Hermes 没有现成 mailbox ingress 命令
- 已确认 `webhook` / `api_server` 都不是飞书主会话方案
- 已确认 `lark-cli` / `feishu-cli` 都有可走的 user-message 路，但当前不满意其优雅性
- 当前决定：**暂停这条实现路线，后续重想**

### 最近动作
- 调研 Hermes `webhook` / `api_server` / `send_message` 能力
- 拉取并分析 `larksuite/cli`
- 对比本机 `feishu-cli` 与 `lark-cli`
- 形成当前“技术上可行但方案不优雅，先暂停”的判断

### 阻塞 / 风险
- 没有现成优雅入口把 AGM 事件挂到 Hermes 飞书主会话
- 如果继续硬推现有路线，会把 workaround 推成正式方案

### 下一步
- 后续重新想：是否存在更薄的宿主桥接层、外围编排层或会话代理方式
- 暂不继续在 `send as user` 路线上投入实现成本

### 验收标准
后续新方案至少应满足：
- 不明显破坏宿主边界
- 尽量不依赖“模拟用户消息”作为正式主路径
- 会话语义清楚
- 验证路径简单，不需要额外复杂 token 运营
