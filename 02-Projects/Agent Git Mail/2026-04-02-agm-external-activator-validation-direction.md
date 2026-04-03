---
title: AGM 外部激活器验证方向（2026-04-02）
date: 2026-04-02
tags:
  - agent-git-mail
  - openclaw
  - activation
  - design
status: in-progress
summary: 在不修改 OpenClaw 本体的前提下，暂时停止继续押 plugin 自动唤醒 idle session，转向先验证“外部激活器”这条最小可行路径。
---

# AGM 外部激活器验证方向（2026-04-02）

## 1. 当前阶段判断

到这一步，AGM / mailbox 本体已经基本成立：

- mailbox 模型已纠偏
  - `send` 写 sender `outbox` + recipient `inbox`
  - `daemon` 只看自己的 `inbox`
- `mt -> leo` 的 repo 级投递已经多次验证通过
- `bind_session_key`、更硬系统消息、AGM skill、skill 跟 plugin 打包安装，这些接入层强化也已经落地并发布

但当前仍未真正闭环的关键问题不是 mail transport，而是：

> **OpenClaw 的 idle session 不会因为 queued system event 自动醒来处理。**

也就是说：

- `enqueueSystemEvent(...)` 成功
- `requestHeartbeatNow(...)` 也调用了
- 但 Leo 这种空闲 direct session 不会因此被真正拉起来
- 结果仍然需要人工再 ping 一句，agent 才会处理堆积的 AGM 通知

这说明问题已经从 AGM plugin 本身，转移到 **宿主 activation 机制** 上。

---

## 2. 当前明确不做的事

### 2.1 不修改 OpenClaw 本体

用户已经明确拍板：

- **不改 OpenClaw core**
- 不做 AGM 专供的宿主能力
- 也不在此时为 AGM 加一个 OpenClaw 特供唤醒接口

所以后续不再继续沿下面这些方向推进：

- 为 AGM 单独加 `requestSessionDeliveryNow()` 之类的专口
- 为 plugin 定制 OpenClaw 的 idle wake 机制
- 把问题继续定义成“需要宿主新能力”后立刻开改 core

### 2.2 暂不继续押 plugin 自动唤醒这条路

虽然已经对 plugin 路做了多轮修补：

- Feishu direct 优先绑定
- `SessionBindingStore` 持久化恢复
- `bind_session_key`
- 更硬系统消息
- AGM skill 跟随安装

但这些修补本质上解决的是：

- 路由到哪条 session
- 收到通知后更容易走对 AGM 入口

并没有解决：

> **空闲 session 自动开始执行一轮 agent run**

在不改 OpenClaw 的前提下，这个点很难靠 plugin 单独补平。

---

## 3. 新方向：先做“外部激活器”的最小验证

用户当前的真实目标不是继续证明 plugin 哪一层还能补，而是：

> **先用最小范围验证：外部激活器能不能把这套系统跑起来。**

这条路的基本判断是：

- AGM 继续负责 mail transport
- 不再把 plugin 自动唤醒当作当前阶段必须打通的路径
- 改由一个**外部激活器**在检测到新 mail 后，主动通过目标 channel 发一条轻量消息，把 agent 唤醒

这里的核心思路是：

1. `leo-mail` inbox 出现新信
2. 外部激活器检测到这封新信
3. 外部激活器通过 Leo 的飞书 DM 发一条轻量消息
4. Leo 被真正激活
5. Leo 再通过 AGM skill / `agm read` 进入 mailbox workflow

---

## 4. 为什么这条路值得先验

这不是拍脑袋，而是基于当前已经被反复验证过的事实：

### 已经成立的部分
- mail 投递通
- Leo 收到用户消息后会被激活
- Leo 被激活后具备走 AGM 的能力（至少已经会回 AGM mail）

### 真正缺的部分
- 从“新 mail 到达”到“空闲 Leo 被拉起来”之间，没有宿主内建自动桥接

因此，如果外部激活器能做到：

> **把“新 mail 到达”转成“发一条最轻量的飞书消息”**

那么理论上：

- Leo 会被正常激活
- AGM skill / 更硬系统消息就能开始真正发挥作用

换句话说，外部激活器不是替代 AGM，而是替代当前做不到的 idle wake。

---

## 5. 当前对外部激活器的判断

### 5.1 这是一个现实可行的工程折中

它不优雅，但有很大概率能跑通：

- 不改 OpenClaw
- 不依赖 system event 真正唤醒 idle agent
- 直接利用“用户消息能激活 agent”这件已经被观察到的事实

### 5.2 这条路当前还未被端到端验证

虽然机理上成立，但目前仍然只能说：

- **高度可信**
- **值得试**
- 但**还没完成闭环验证**

所以现阶段正确说法是：

> 先做最小外部激活器验证，不先宣称它已经跑通。

---

## 6. 最小验证目标

用户当前要求的是：

> **最小范围验证“外部激活器能否实现”。**

因此后续的最小验证不应一上来就做成完整产品，而应先收成一个最小闭环：

1. Leo inbox 出现新 mail
2. 一个外部激活器检测到它
3. 外部激活器通过 Leo 的飞书 DM 发一条轻量消息
4. Leo 被唤醒
5. Leo 走 AGM 入口处理 mail

如果这条链能成立，再决定：

- 这个外部激活器是临时脚本
- 还是 AGM 的正式 companion process
- 还是后续收成独立组件

---

## 7. 当前策略结论

到当前时点，AGM × OpenClaw 的策略已经发生转向：

### 之前的主尝试
- 通过 plugin + system event + heartbeat/requestHeartbeatNow
- 尝试让 Leo 在 idle 状态下自动醒来处理 AGM 通知

### 现在的收口
- plugin 路已经尽力补过一轮，能补的接入层都补了
- 但在“不改 OpenClaw 本体”的约束下，继续押 plugin 自动唤醒价值不高
- 当前更值得做的是：

> **先验证一个最小外部激活器是否可行。**

这并不是否定 plugin 的价值，而是承认：

- plugin 当前更适合作为 mailbox notification + routing + skill 引导层
- 真正的 idle wake，如果不能改宿主，就要靠外部激活手段补上

---

## 8. 一句话总结

> **当前阶段先不继续深挖 plugin 自动唤醒；在不改 OpenClaw 的前提下，优先验证“外部激活器”能否把 AGM 的异步协作真正跑起来。**
