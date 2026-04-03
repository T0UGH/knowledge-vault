---
title: AGM × OpenClaw 集成进展（2026-04-02）
date: 2026-04-02
tags:
  - agent-git-mail
  - openclaw
  - integration
  - progress
status: in-progress
summary: 记录 AGM 在 OpenClaw 场景下从 mailbox 模型修正、Feishu DM 通知路由排查，到 bind_session_key、AGM skill 与更硬系统消息落地的一整轮进展。
---

# AGM × OpenClaw 集成进展（2026-04-02）

## 1. mailbox 模型已纠偏并验证

本轮最关键的架构纠偏已经完成：

- `send` 改成 **双写**：
  - sender 写自己的 `outbox/`
  - recipient 写自己的 `inbox/`
- `daemon` 改成 **只盯自己的 inbox**
- 明确放弃“轮询别人 outbox”的 observer / event-feed 模型

这次纠偏的核心原因不是 transport 跑不通，而是之前的 remote-only 实现破坏了 mailbox 语义：

- 发信只写 sender 自己 repo，会退化成“自言自语”
- daemon 扫 contacts outbox，不符合收件箱直觉
- inbox 被错误降级成可有可无的 materialization

当前正式模型已经重新收口为：

> `send` 写自己的 `outbox`，也写对方的 `inbox`；`daemon` 只看自己的 `inbox`。

### E2E 验证

`atlas ↔ boron` 已完成 mailbox 模型下的双向 E2E：

- sender outbox 有文件 ✅
- recipient inbox 有文件 ✅
- 文件名一致 ✅
- sender commit = `agm: send` ✅
- recipient commit = `agm: deliver` ✅
- `reply_to` 字段验证通过 ✅

相关提交：

- `4a4d718` — test(agm): redefine send/reply around mailbox semantics
- `659e153` — feat(agm): correct mailbox model - send/reply dual-write, daemon watches local inbox
- `81bfaf9` — fix(agm): CLI commands respect --config-path argument

---

## 2. npm 发布与兼容修复

围绕 AGM/OpenClaw 接入，这一轮陆续发过多个关键版本：

### CLI / core
- `@t0u9h/agent-git-mail@0.1.5`
  - 修复 macOS 下全局 `agm` symlink/shebang 入口问题
- `@t0u9h/agent-git-mail@0.1.6`
  - 发布 mailbox model correction
- `@t0u9h/agent-git-mail@0.1.7`
  - waterline 按 agent 隔离（`refs/agm/last-seen/<agent>`）
- `@t0u9h/agent-git-mail@0.1.8`
  - 正式支持 `notifications.bind_session_key`
- `@t0u9h/agent-git-mail@0.1.9`
  - 配合 skill / 更硬系统消息的最新一轮发布

### OpenClaw plugin
- `@t0u9h/openclaw-agent-git-mail@0.1.4`
  - 补 `openclaw.plugin.json`，兼容旧 OpenClaw 宿主
- `@t0u9h/openclaw-agent-git-mail@0.1.6`
  - Feishu direct session binding 优先，不再被 `main` 抢绑
- `@t0u9h/openclaw-agent-git-mail@0.1.7`
  - Session binding 持久化恢复（`~/.config/agm/session-bindings.json`）
- `@t0u9h/openclaw-agent-git-mail@0.1.8`
  - 配合 waterline agent-scope 修复
- `@t0u9h/openclaw-agent-git-mail@0.1.9`
  - 正式支持 `bind_session_key`
- `@t0u9h/openclaw-agent-git-mail@0.1.10`
  - AGM skill + 更硬系统消息打包发布

---

## 3. Feishu DM 通知链路排查结论

Leo 的飞书 DM 收不到 AGM 通知，期间至少踩了 4 类问题：

### 3.1 session binding key 错位

早期实现里，plugin 在 `session_start` 事件中从 `sessionKey` 里拆 agentId：

- `agent:main:feishu:direct:ou_xxx`
- 被错误解析成 `main`

但 poll 时又按 `config.self.id`（比如 `leo`）查 binding，导致 key 空间不一致。

修复方向：

- 不再从 `sessionKey` 猜 agentId
- 直接用 `config.self.id` 作为 binding key

### 3.2 Feishu direct 被 main 抢绑

Leo 既有：
- `agent:main:main`
- 也有 Feishu DM session

所以 plugin 要求：
- 如果存在 `feishu:direct`，优先绑定它
- `main` 不应覆盖已经存在的 Feishu binding

### 3.3 重启后 binding 丢失

后来确认即使修了 Feishu 优先，plugin/gateway 一重启，内存里的 binding 还是会丢。

修复方向：

- `SessionBindingStore` 启动时自动从：
  - `~/.config/agm/session-bindings.json`
  读取绑定
- 解决“需要重新等 session_start 重放”这个问题

### 3.4 waterline ref 路径错误

Leo 侧还出现过：
- `git update-ref refs/agm/last-seen ...` 失败

根因不是锁或权限，而是 waterline ref 设计没跟 mailbox 模型升级：

- 错误：`refs/agm/last-seen`
- 正确：`refs/agm/last-seen/<agentId>`

最终修成按 agent 隔离。

---

## 4. `bind_session_key` 已正式收口

由于 `session_start` 对 Feishu DM 是否稳定触发仍然不可靠，本轮新增并正式收口了一个更简单、更硬的逃生口：

```yaml
notifications:
  default_target: main
  bind_session_key: agent:main:feishu:direct:ou_xxx
```

设计判断：

- 这是一个**硬绑定**能力
- 适合用户明确知道自己希望 AGM 通知固定送到哪条 session
- 对 Leo 这种“主聊天发生在 Feishu DM”场景尤其重要

本次明确拍板：

- 后续统一用 `bind_session_key`
- 不再继续对外强调 `forced_session_key`

当前 plugin 路由优先级已经调整为：

1. `AGM_FORCED_SESSION_KEY` 环境变量
2. `config.notifications.bind_session_key`
3. session binding
4. `default main`

---

## 5. AGM 接入层的新判断：不是单点 bug，而是还不够硬

本轮还新暴露了一层更深的问题：

> AGM 集成层不是“能收到通知”就够，而是要确保 agent **一收到通知就走正确的 mailbox 动作入口**。

这次具体踩出的结论：

- 问题不是把系统消息误认成人类消息
- 而是 agent 知道“要去看信”，但**没有第一时间走 AGM 这个正确入口**
- 本质是 **tool routing / action routing 错位**

从这点反推，接下来真正该补的是两层：

1. **AGM 专用 skill**
2. **更硬的 action-oriented 系统消息**

---

## 6. AGM skill + 更硬系统消息已经落地

### 6.1 更硬的系统消息

plugin 原来的系统消息过于轻：

```text
New agent git mail: from=..., file=...
```

现在已经改成更硬、更具动作指向性的版本，例如：

```text
[AGM ACTION REQUIRED]
New mail delivered to your inbox.
from=<from>
file=<filename>

Use AGM commands, not generic chat reply.
Next step: agm read <filename>
Then decide whether to agm reply or agm archive.
```

设计目标：

- 把 agent 从“普通提醒”拉回 AGM 专属入口
- 第一反应就是 `agm read`
- 避免继续靠通用推理临场想到 mailbox 工具

### 6.2 AGM skill 随 plugin 一起打包

已新增：

- `packages/openclaw-plugin/skills/agm-mail/SKILL.md`

skill 内容聚焦于收到 AGM 通知后的默认流程：

- `agm read <filename>`
- 然后决定：
  - `agm reply ...`
  - 或 `agm archive ...`

明确要求：

- AGM 通知不是普通聊天消息
- 不要先自由回复聊天再补 mail
- 先走 AGM mailbox workflow

### 6.3 skill 通过 plugin / bootstrap 一次性安装

不是让用户再单独装第二遍 skill，而是：

- plugin 包 tarball 已带 `skills/agm-mail/SKILL.md`
- `scripts/bootstrap.sh` 已补 AGM skill 安装逻辑
- 默认会把 skill 装进：
  - `~/.openclaw/workspace/skills/agm-mail/SKILL.md`

这意味着：

> 安装 AGM plugin / 走 AGM bootstrap 时，AGM skill 就会一起到位。

### 6.4 README / Quick Start 已同步

以下文档已更新：

- `README.md`
- `packages/agm/README.md`
- `packages/openclaw-plugin/README.md`

已补内容包括：

- `bind_session_key` 配置说明
- AGM skill 随 plugin 安装
- AGM 通知不是普通提醒，而是动作型系统消息
- 默认 mailbox handling flow

---

## 7. 当前阶段的整体判断

到 2026-04-02 这轮结束，AGM × OpenClaw 集成已经从“能跑的原型”推进到“语义、路由、接入层都开始收紧”的状态。

已确认成立的部分：

- mailbox 模型正确（sender outbox + recipient inbox）
- daemon 语义正确（只看 self inbox）
- repo 侧 `mt -> leo` 真实投递成立
- session binding 与 Feishu 路由问题已经基本摸清
- `bind_session_key` 已成为更硬、更简单的 session 路由能力
- AGM skill 与更硬系统消息已开始一体化交付

仍可能继续迭代的部分：

- Feishu DM 通知最终链路是否完全稳定
- `bind_session_key` 的产品化细节是否继续收口
- AGM skill 是否从最小 workflow 扩成更完整的 mailbox discipline
- OpenClaw runtime 层是否还需要更强的 action hint / tool routing 机制

但大的判断已经很清楚：

> AGM 现在真正需要补的，不只是 mail 协议本身，而是“收到 mail 之后 agent 会不会稳定走正确入口”的接入层硬度。
