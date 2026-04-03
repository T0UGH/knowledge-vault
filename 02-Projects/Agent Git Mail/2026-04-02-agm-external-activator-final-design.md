---
title: AGM 外部激活器最终设计
date: 2026-04-02
tags:
  - agent-git-mail
  - openclaw
  - activator
  - design
status: draft
summary: 放弃 OpenClaw AGM plugin，统一收成 AGM 单包（内置 daemon）+ 可扩展外部激活器架构。外部激活器先落 Feishu，通过 openclaw agent 命令发送可见消息稳定唤醒 idle agent，同时保留未来扩展到其他激活器的接口边界。
---

# AGM 外部激活器最终设计

## 1. 设计结论

本轮 AGM × OpenClaw 集成的最终收口如下：

1. **不再继续维护 `openclaw-agent-git-mail` plugin**
2. **只维护一个包：`@t0u9h/agent-git-mail`**
3. **daemon 直接内置在 AGM 里**
4. **AGM 检测到新邮件后，不再依赖 OpenClaw plugin / system event / heartbeat 唤醒 idle agent**
5. **改由 AGM 通过“外部激活器”发送一条真实可见的激活消息，稳定触发目标 agent**

一句话：

> **AGM 负责邮件、daemon 和激活；OpenClaw 只作为被外部消息激活的宿主。**

---

## 2. 为什么放弃 plugin 路

过去这轮集成里，plugin 已经做过多轮补丁：

- Feishu direct session 优先绑定
- `SessionBindingStore` 持久化恢复
- `bind_session_key`
- 更硬系统消息
- AGM skill 跟随安装

这些改动能提升“消息到了之后更容易走对 AGM 入口”的概率，但始终绕不过一个宿主级约束：

> **idle session 不会因为 queued system event 自动开始一轮 agent run。**

这导致 AGM plugin 路最终停在一个尴尬状态：

- 邮件投递已经成功
- 通知 enqueue 也成功
- 但 agent 还是要等人工 ping 才真正处理

用户已经明确拍板：

- **不改 OpenClaw 本体**
- 所以不再继续为 plugin 路追加复杂补丁
- 直接转向一个更直接、更可控的外部激活方案

因此，plugin 继续存在的价值已经不高，反而会制造三层结构复杂度：

- AGM
- plugin
- 外部激活补丁

最终决定是把这层复杂度砍掉。

---

## 3. 最终架构

最终架构只保留两层：

### 3.1 AGM（唯一维护包）
AGM 负责：

- mailbox 协议
- send / reply / archive
- sender outbox + recipient inbox
- daemon 检测本地 inbox
- 外部激活触发
- 激活去重 / checkpoint

### 3.2 OpenClaw（宿主）
OpenClaw 只负责：

- 收到外部可见消息
- 激活 agent
- agent 在现有聊天通道上下文中运行

也就是说：

- OpenClaw 不再承担 AGM mail notification plugin 的职责
- 不再由 plugin 注入系统事件再试图唤醒 idle agent
- AGM 直接把“新邮件到达”转成“发一条能唤醒 agent 的外部消息”

---

## 4. 核心设计原则

### 4.1 一个系统只维护一个真正的入口
后续只维护：

- `@t0u9h/agent-git-mail`

不要继续维护一套 AGM plugin 再和 AGM CLI/core 互相追版本。

### 4.2 daemon 是 AGM 的一部分
mailbox 检测语义属于 AGM，而不是宿主插件。

所以：
- daemon 跟 mailbox 协议走
- 不跟 OpenClaw runtime/plugin 生命周期绑死

### 4.3 激活不是 mail transport 的一部分，但由 AGM 编排
mail transport 负责把信送到 inbox。  
agent wake 负责让对方真正开始处理。

它们不是同一件事，但对产品体验来说是一个连续链路，所以由 AGM 统一编排最自然。

### 4.4 当前接受“用户可见唤醒消息”
这次设计明确接受：

- 激活消息用户可见
- 它不是静默信号
- 但必须稳定、及时

用户已经明确表示：

> 相比“完全无痕”，更重要的是“一定能及时触发”。

---

## 5. 外部激活器是什么

外部激活器不是另一个产品，也不是一个新的大系统。  
它只是 AGM daemon 发现新信之后，用来把目标 agent 叫醒的一层薄触发能力。

### 当前第一版
第一版外部激活器直接落在 AGM 内部，以“激活器接口 + Feishu 实现”的方式组织。

也就是说：

- AGM daemon 检测到新信
- AGM 选择一个 activator
- activator 执行一次激活动作

而不是：

- 新开一个完全独立的 plugin
- 或再造一个旁路服务

---

## 6. 最小闭环流程

以 `mt -> leo` 为例：

1. `mt` 发送一封信
   - `mt` 写自己 `outbox/`
   - `leo` 写自己 `inbox/`

2. `leo` 机器上的 AGM daemon 轮询本地 inbox

3. AGM daemon 发现新文件：
   - 例如 `2026-04-02T05-28-19Z-mt-to-leo-b899.md`

4. AGM daemon 调用已配置的 activator

5. activator 发送一条可见消息给 Leo 的飞书 DM

6. Leo 被 OpenClaw 正常激活

7. Leo 在已有 channel 中继续处理 AGM 邮件
   - 先 `agm read <filename>`
   - 再决定回复 / 归档 / 继续行动

一句话：

> **mail 进 inbox，activator 发消息，OpenClaw 被动响应。**

---

## 7. 为什么这条路更靠谱

因为它只依赖已经被验证过的事实：

1. AGM mailbox 投递链路已经通
2. Leo / rk 被真实飞书消息触发后会被激活
3. `openclaw agent --channel feishu -t <openId> -m <message> --deliver` 已经被验证是可执行入口

相反，plugin 路依赖的是一个当前并不存在的宿主能力：

- queued system event 能否真正把 idle session 拉起来

在“不改 OpenClaw 本体”的约束下，这个能力补不出来。

所以最稳妥的工程选择是：

> **不要继续赌宿主内部唤醒；直接利用“外部真实消息会激活 agent”这个现成事实。**

---

## 8. 激活器接口设计

虽然第一版只做 Feishu，但设计必须便于后续扩展到其他激活器。

### 接口

```ts
interface AgmActivator {
  name: string;
  activate(input: {
    selfId: string;
    filename: string;
    from: string;
    subject?: string | null;
    message: string;
  }): Promise<ActivationResult>;
}
```

### 返回值

```ts
interface ActivationResult {
  ok: boolean;
  activator: string;
  externalId?: string | null;
  error?: string | null;
}
```

### 设计意图

- AGM daemon 只依赖抽象接口
- 当前实现一个 `feishu-openclaw-agent` activator
- 后续如果有：
  - Telegram activator
  - Discord activator
  - SMS activator
  - Webhook activator
  也可以直接挂进去

也就是说：

> **先做一个实现，但接口先抽好。**

---

## 9. 第一版激活器：Feishu via `openclaw agent`

### 激活动作

当前第一版不直接调用 Feishu API，而是复用 OpenClaw 已有的 agent CLI 入口：

```bash
openclaw agent --channel feishu -t "<openId>" -m "<message>" --deliver
```

### 为什么这么选

相比直接调 Feishu API，这条路有三个好处：

1. 复用 OpenClaw 已有 channel 发送能力
2. 避免自己再处理 Feishu 鉴权和消息格式细节
3. 与 agent 真实入口保持一致

所以 Feishu activator 的职责可以简单理解为：

> **把新邮件翻译成一条 `openclaw agent --channel feishu ...` 命令。**

---

## 10. 第一版输入配置

建议 AGM config 增加一块 activator 配置，例如：

```yaml
activation:
  enabled: true
  activator: feishu-openclaw-agent
  poll_interval_seconds: 5
  dedupe_mode: filename
  feishu:
    open_id: ou_xxx
    message_template: |
      [AGM ACTION REQUIRED]
      你有新的 Agent Git Mail。
      请先执行：agm read {{filename}}
```

### 字段说明
- `enabled`：是否启用外部激活
- `activator`：当前使用哪个 activator
- `poll_interval_seconds`：外部激活轮询间隔
- `dedupe_mode`：当前先按 filename 防重
- `feishu.open_id`：目标 agent 的飞书 openId
- `feishu.message_template`：激活消息模板

---

## 11. daemon 如何与 activator 配合

### 检测逻辑
AGM daemon 每轮：

1. 看本地 inbox
2. 找出新文件
3. 判断该文件是否已经激活过
4. 未激活过则调用 activator
5. 成功后记录 checkpoint

### 防重
checkpoint 建议放在：

```text
~/.config/agm/activation-state.json
```

最小结构：

```json
{
  "processed": {
    "2026-04-02T05-28-19Z-mt-to-leo-b899.md": {
      "activatedAt": "2026-04-02T14:30:05+08:00"
    }
  }
}
```

### 为什么先按 filename 去重
因为当前 AGM 文件名本来就是主标识，最小实现阶段这已经够了。

---

## 12. 激活消息模板

第一版消息必须满足两个目标：

1. 足够强，能把 agent 推到 AGM 入口
2. 足够短，不变成新的复杂对话污染源

推荐模板：

```text
[AGM ACTION REQUIRED]
你有新的 Agent Git Mail。
请先执行：agm read {{filename}}
```

### 不推荐
- 太长的上下文解释
- 同时带多条动作要求
- 让 agent 直接自由发挥

这条消息的唯一目标就是：

> **把 agent 拉起来，并把第一步动作钉死到 `agm read`。**

---

## 13. 最小验证方法

用户当前明确要求的是：

> **先验证外部激活器能不能实现。**

因此，第一轮验证只做最小闭环：

### 校验 1：检测新信
- Leo inbox 出现一封新 mail
- daemon 日志显示发现该文件

### 校验 2：激活消息送达
- Leo 飞书 DM 收到一条固定的激活消息

### 校验 3：不重复发
- 在没有新 mail 时，外部激活器不应重复对同一 filename 再发一次

### 校验 4：agent 被真正拉起
- Leo 因这条飞书消息而被激活
- 后续开始处理 AGM mail

这轮验证成立，就可以认为“外部激活器方案可行”。

---

## 14. 当前不做

为了保持最小范围，当前明确不做：

- 不继续维护 AGM OpenClaw plugin
- 不修改 OpenClaw 本体
- 不做多平台 activator
- 不做自动执行 `agm read`
- 不做复杂 action planner
- 不做 webhook / queue / job system
- 不做完全静默激活

当前只追求：

> **最小、稳定、可复用地把新 mail 转成一条能立即激活 agent 的外部消息。**

---

## 15. 后续扩展方向

第一版只做 Feishu activator，但接口已经预留扩展空间。后续如果要扩展，可以加：

- `telegram-openclaw-agent`
- `discord-openclaw-agent`
- `sms-activator`
- `webhook-activator`

但这些都属于后续能力，不影响当前最小闭环。

关键原则不变：

> **AGM 统一编排 mail + daemon + activation；OpenClaw 只是被消息激活的宿主。**

---

## 16. 最终结论

本轮最终设计应收成：

> **停止继续维护 `openclaw-agent-git-mail` plugin；统一只维护 `@t0u9h/agent-git-mail`。AGM 内置 daemon，检测本地 inbox 的新邮件后，通过可扩展的外部激活器发送一条真实可见的激活消息。第一版激活器使用 `openclaw agent --channel feishu -t <openId> -m <message> --deliver` 触发 Leo / rk 的飞书 DM，从而稳定唤醒 idle agent。**

一句话总结：

> **mail 归 AGM，wake 也归 AGM；OpenClaw 不再承担 AGM plugin 职责，只负责响应被激活的真实消息。**
