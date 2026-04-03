---
title: AGM × HappyClaw 集成设计（Phase 1）
date: 2026-04-01
tags:
  - agent-git-mail
  - happyclaw
  - integration
  - design
status: draft
summary: 设计 AGM 与 HappyClaw 的 Phase 1 集成边界：AGM 保持独立 mail transport，HappyClaw 仅提供一个薄的 internal inject host capability，把 AGM 新信事件注入主会话。
---

# AGM × HappyClaw 集成设计（Phase 1）

## 1. 背景

`Agent Git Mail`（AGM）已经完成基于 remote repo 的 mail transport 闭环：

- send / reply / archive
- remote fetch / per-contact waterline
- daemon 新信检测
- OpenClaw plugin 注入主会话

现在要解决的问题是：

> **如何让 AGM 在 HappyClaw 中也能稳定地把新信事件送进主会话。**

本设计只关注：

- AGM → HappyClaw 的事件注入边界
- 主会话兼容方式
- 宿主职责划分

不讨论：

- AGM mail transport 本体设计
- HappyClaw 全量 plugin/runtime 抽象升级
- mailbox UI 产品化

---

## 2. 设计目标

本方案必须满足以下目标：

1. **注入主会话，而不是新开独立会话**
2. **AGM 保持独立，不绑定 HappyClaw 业务实现**
3. **HappyClaw 只提供薄宿主能力，不承接 mailbox 业务逻辑**
4. **事件 source 可审计，不伪装成人类消息**
5. **同一封信不会重复注入主会话**
6. **实现改动尽量小，便于 HappyClaw 本体维护**

---

## 3. 非目标

Phase 1 明确不做：

- AGM 专用独立会话
- thread / subagent / cron 会话注入
- 通用任意 source 注入平台
- 富 UI 卡片展示
- 将完整邮件正文自动塞入主会话
- 单独新开 HappyClaw adapter repo

---

## 4. 总体架构

系统分三层：

### 4.1 AGM transport 层
由 AGM 负责：

- remote repo 作为 mailbox 真相源
- send / reply 写 sender repo
- daemon 轮询 contacts remote
- per-contact waterline 检测新信
- 形成“需要通知宿主”的事件

### 4.2 HappyClaw host capability 层
由 HappyClaw 负责：

- 提供一个本地 internal inject endpoint
- 接收 AGM 事件
- 校验目标主会话是否合法
- 复用现有主会话消息链
- 广播前端并唤醒 agent

### 4.3 Agent 决策层
由 HappyClaw 主会话内 agent 负责：

- 是否读取该邮件
- 是否回复
- 是否归档
- 如何根据正文继续执行

一句话：

> **AGM 负责发现信，HappyClaw 负责把这个信号送进主会话。**

---

## 5. 边界原则

### 5.1 AGM 不直接调用 HappyClaw 内部业务函数
AGM 不应直接耦合这些内部实现细节：

- `storeMessageDirect(...)`
- `broadcastNewMessage(...)`
- `queue.sendMessage(...)`
- `enqueueMessageCheck(...)`
- `processGroupMessages(...)`

原因：
- 耦合过深
- HappyClaw 内部变更会反向破坏 AGM
- 边界不清

AGM 只应依赖一个**稳定、薄、受控**的 host capability 入口。

### 5.2 HappyClaw 不负责 mailbox 业务
HappyClaw 不做以下事情：

- mailbox remote 轮询
- obligation 判定
- reply 策略
- archive 规则
- sender / recipient transport 协议

这些都属于 AGM 的领域逻辑。

### 5.3 主会话是唯一注入目标
AGM 事件的消费位置必须是 HappyClaw 主会话。

允许：
- main / direct session

不允许：
- thread
- subagent
- ACP 临时会话
- cron 临时会话

理由：
- mailbox 语义属于 agent 主上下文
- 避免噪音污染短生命周期上下文
- 降低路由复杂度

---

## 6. 方案选择

## 方案：HappyClaw 提供专用 internal inject endpoint

推荐新增一个专用入口：

```http
POST /internal/agm/inject
```

而不是做成通用注入平台。

原因：

1. 现在只有 AGM 这个明确 source
2. 需求是“host capability patch”，不是独立产品
3. 做专用入口最薄、最稳、最容易维护
4. 后续若未来确实出现多 source，再抽象也不迟

---

## 7. 协议设计

### 7.1 Endpoint
```http
POST /internal/agm/inject
```

### 7.2 鉴权
请求必须满足以下至少一项，推荐同时启用：

- 仅监听 `127.0.0.1`
- Bearer internal secret

示例：

```http
Authorization: Bearer <HAPPYCLAW_INTERNAL_SECRET>
```

### 7.3 Request Body

```json
{
  "chatJid": "web:main",
  "source": "agm",
  "sourceMeta": {
    "mailboxId": "boron",
    "messageId": "2026-04-01T12-00-00Z-atlas-to-boron-1234.md",
    "from": "atlas",
    "subject": "Test",
    "reason": "new_mail"
  },
  "content": "[AGM] New mail from atlas: Test"
}
```

### 7.4 字段约束

#### `chatJid`
- 必填
- 目标主会话 id
- 只允许 main/direct 会话

#### `source`
- 必填
- 固定为 `"agm"`

#### `sourceMeta`
- 必填
- 用于审计和幂等
- 建议包含：
  - `mailboxId`
  - `messageId`
  - `from`
  - `subject`
  - `reason`

#### `content`
- 必填
- 注入主会话的可见文本
- 第一阶段保持纯文本

### 7.5 Response

成功：
```json
{
  "ok": true,
  "accepted": true,
  "chatJid": "web:main",
  "messageId": "2026-04-01T12-00-00Z-atlas-to-boron-1234.md"
}
```

失败：
```json
{
  "ok": false,
  "error": "invalid_chat_target"
}
```

---

## 8. HappyClaw 处理流程

HappyClaw 在收到 `/internal/agm/inject` 请求后，按以下流程处理：

1. 校验 auth
2. 校验 `source === "agm"`
3. 校验 `chatJid` 是否为允许的 main/direct 会话
4. 执行幂等检查
5. 构造一条 `source=agm` 的 injected message
6. 接入现有主会话消息链
7. 广播前端
8. 唤醒 agent 继续处理
9. 返回 accepted 结果

关键点：

> **HappyClaw 只负责“注入”这一步，不负责解释 mailbox 业务。**

---

## 9. 幂等设计

### 9.1 幂等 key
推荐使用：

```text
agm:<chatJid>:<messageId>
```

### 9.2 幂等目标
避免以下情况重复注入：

- AGM daemon 重启
- HappyClaw 重启后 AGM 重试
- 同一封信被重复检测
- 网络重试导致重复 POST

### 9.3 Phase 1 实现建议
可以先做轻量实现：

- sqlite 小表
- 或宿主持久层里一张 `injected_events` 表

至少要保证**跨进程重启后仍可防重**。
如果只放内存，会太脆。

---

## 10. AGM 侧行为设计

### 10.1 调用时机
AGM daemon 在发现新的 inbound mail 时：

1. 根据 local checkpoint 判断这封信是否需要通知
2. 构造 inject payload
3. POST 到 HappyClaw `/internal/agm/inject`
4. 成功后更新 AGM 自己的通知 checkpoint

### 10.2 AGM 侧 checkpoint
AGM 也建议保留自己的 checkpoint，防止自己无限重试。

但要注意：

> **最终防重不能只靠 AGM，HappyClaw 侧也必须幂等。**

也就是双层防重：

- AGM checkpoint：减少重复请求
- HappyClaw idempotency：防止重复注入

---

## 11. 消息语义

### 11.1 source 必须显式保留
注入后的消息应带有明确 source：

- `source = agm`

不能伪装成：
- human
- 普通 system
- web client user

### 11.2 文本内容建议
第一阶段只做薄提醒，例如：

```text
[AGM] New mail from atlas: Test
```

而不是把整封邮件正文直接塞进主会话。
原因：

- 主会话噪音更小
- 保持 mailbox “先提醒，再决定是否读”
- 避免未来迁移时文本协议过重

### 11.3 agent 后续动作
agent 收到 AGM 通知后，再决定是否：

- `agm list`
- `agm read`
- `agm reply`
- `agm archive`

---

## 12. 同机 / 异机一致性

该方案天然兼容：

- HappyClaw 和 AGM 在同一机器
- HappyClaw 和 AGM 在不同机器但通过安全 tunnel / local agent 调用

但 Phase 1 推荐默认模型仍是：

- AGM 与 HappyClaw 同机部署
- 通过 localhost internal route 调用

这样最简单、最稳。

---

## 13. 仓库边界

当前不建议单独开 HappyClaw adapter repo。

推荐边界：

- `agent-git-mail`：AGM transport / daemon / CLI
- `happyclaw`：`/internal/agm/inject` 宿主补口

原因：

- 复杂度还不值得抽仓
- 当前只是 host capability patch
- 多一层 repo 只会增加管理摩擦

---

## 14. Phase 1 交付内容

### AGM 侧
- 检测新信后调用 HappyClaw inject API
- 维护最小通知 checkpoint
- 日志记录请求成功/失败

### HappyClaw 侧
- `/internal/agm/inject`
- auth + local-only
- 主会话目标校验
- 幂等防重
- 接入现有消息链
- 广播 + 唤醒

### 验证标准
至少验证以下链路：

1. AGM 检测到新信
2. 成功 POST `/internal/agm/inject`
3. HappyClaw 返回 accepted
4. 主会话 transcript 中可见 AGM 注入消息
5. agent 在原上下文中继续处理
6. 重复发送同一 `messageId` 不会重复注入

---

## 15. 风险与取舍

### 风险 1：internal route 的长期稳定性
这是 internal route，不是最终公共平台接口。
但当前作为 HappyClaw host patch 是可接受的。

### 风险 2：如果未来不止 AGM 一个 source
那时可能需要把 `/internal/agm/inject` 抽成更通用的宿主事件注入口。
但现在没必要提前泛化。

### 风险 3：主会话噪音
如果通知过频，主会话会被打断。
这不应由 HappyClaw 解决，而应由 AGM 的通知策略控制。

---

## 16. 最终结论

Phase 1 的正式设计应为：

> **AGM 保持独立 mail transport；HappyClaw 在本体内提供一个专用、薄、受控的 `/internal/agm/inject` 宿主注入口；AGM daemon 发现新信后按协议调用该入口，把事件注入主会话，由 agent 在原上下文继续处理。**

一句话总结：

> **不把 AGM 做成 HappyClaw 专用，不把 HappyClaw 做成 mailbox 系统；只在两者之间补一个薄注入口。**
