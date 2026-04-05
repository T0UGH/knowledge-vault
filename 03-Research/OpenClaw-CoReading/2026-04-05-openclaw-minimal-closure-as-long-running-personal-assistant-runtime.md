---
title: OpenClaw 共读：作为长期个人助理 runtime 的最小闭环
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - runtime
  - assistant
status: active
---

# OpenClaw 共读：作为长期个人助理 runtime 的最小闭环

## 一句话结论

如果把 OpenClaw 当成“长期个人助理 runtime”来看，它的最小闭环不是模型、也不是某个 skill，而是这 7 个环节一起成立：**session topology、reply/run 主循环、routing/delivery continuity、context/memory governance、tool execution bus、active continuation（heartbeat / followup / cron）、plugin/channel docking**。少一个，它都可能还能“工作”；但要少到还能像一个长期个人助理一样稳定活着、持续干活、不乱串台，这 7 个环节缺一不可。

## 这部分在系统里负责什么

前面 13 篇已经把 OpenClaw 的核心部件分开读了一遍：

- session runtime
- subagent lifecycle
- gateway routing
- memory / context injection
- prompt composition
- skills prompt
- long-running assistant vs Claude Code
- commands vs embedded run
- heartbeat
- cron / followup / heartbeat 关系
- session topology
- tool execution model
- plugin / channel architecture

如果继续逐篇拆，很容易越读越细。

所以这一篇的目标不是再引入新模块，而是回答：

> **如果只保留 OpenClaw 真正让它成为“长期个人助理 runtime”的那部分，最小闭环到底是什么？**

这篇本质上是一篇 **架构收束稿**。

## 主链路

我把 OpenClaw 的最小闭环收成 7 个环节：

1. **session topology**：系统先要有长期存在的会话骨架
2. **reply/run loop**：系统要能把一次输入真正变成一轮运行
3. **routing/delivery continuity**：系统要知道结果该回哪
4. **context/memory governance**：系统要在长时间运行下不失忆、不爆 prompt
5. **tool execution bus**：系统要真的能做事，而不只是会说
6. **active continuation**：系统要在没人发消息时还能继续存在和续跑
7. **plugin/channel docking**：系统要能活在真实通信表面上

一句话串起来：

> **OpenClaw 的最小闭环不是“会话 + 模型”，而是“会话骨架 + 执行主循环 + 路由连续性 + 上下文治理 + 动作执行 + 主动续跑 + 渠道宿主”。**

## 关键文件与代码片段

这篇不是新模块导读，而是前面主线的压缩总图。对应源码锚点大致可以收成：

### 1. session 骨架
- `src/config/sessions/session-key.ts`
- `src/config/sessions/main-session.ts`
- `src/config/sessions/types.ts`
- `src/config/sessions/store.ts`
- `src/config/sessions/transcript.ts`

### 2. reply/run 主循环
- `src/auto-reply/reply/get-reply-run.ts`
- `src/auto-reply/reply/agent-runner-execution.ts`
- `src/auto-reply/reply/followup-runner.ts`
- `src/agents/command/attempt-execution.ts`

### 3. routing / delivery continuity
- `src/routing/resolve-route.ts`
- `src/config/sessions/delivery-info.ts`
- `src/infra/outbound/session-binding-service.ts`
- `src/infra/outbound/bound-delivery-router.ts`
- `src/infra/outbound/deliver.ts`
- `src/auto-reply/reply/route-reply.ts`

### 4. context / memory governance
- `src/agents/system-prompt.ts`
- `src/agents/system-prompt-report.ts`
- `src/agents/memory-search.ts`
- `src/plugins/memory-runtime.ts`
- `src/auto-reply/reply/agent-runner-memory.ts`

### 5. tool execution bus
- `src/agents/openclaw-tools.ts`
- `src/agents/tool-catalog.ts`
- `src/agents/bash-tools.exec.ts`
- `src/agents/bash-tools.process.ts`
- `src/agents/tools/sessions-spawn-tool.ts`
- `src/agents/tools/cron-tool.ts`
- `src/agents/pi-tools.policy.ts`

### 6. active continuation
- `src/infra/heartbeat-runner.ts`
- `src/infra/heartbeat-wake.ts`
- `src/cron/isolated-agent/run.ts`
- `src/cron/isolated-agent/session.ts`
- `src/auto-reply/reply/followup-runner.ts`

### 7. plugin / channel docking
- `src/plugins/runtime.ts`
- `src/channels/plugins/load.ts`
- `src/channels/plugins/session-conversation.ts`
- `src/infra/outbound/deliver.ts`
- `src/auto-reply/reply/route-reply.ts`

## 设计判断

### 1. 第一环：session topology 是根骨架

如果没有 session 作为一等公民，OpenClaw 不可能成为长期个人助理。

原因很简单：长期助理一定需要：

- 跨天续聊
- 多会话并存
- 异步结果回原会话
- 子会话和主会话并存
- heartbeat / cron / subagent 有各自挂点

所以最小闭环第一环不是模型，也不是工具，而是：

> **一套能承载不同运行形态的 session topology。**

这也是为什么 `main session`、`subagent session`、`heartbeat session`、`cron isolated session` 这些东西都很关键。

### 2. 第二环：reply/run 主循环决定系统能不能把“消息”变成“运行”

session 有了，还不够。系统还得有一条稳定主循环，把：

- 用户输入
- system events
- skills snapshot
- extra system prompt
- routing / queue / auth

装配成一次正式运行。

`getReplyRun()`、commands mode、embedded run mode 这一层就是闭环第二环。

如果没有这层，系统只有状态，没有真正的 turn execution 语义。

### 3. 第三环：routing / delivery continuity 决定长期助理会不会串台

这是长期助理和单次 agent 最大的差别之一。

单次 agent 可以只管“算出答案”；长期助理必须管：

- 回哪个 channel
- 回哪个 thread
- heartbeat / completion / followup 发给谁
- 是否回原 conversation 还是 current binding

所以最小闭环一定要有：

> **inbound route -> session -> binding -> deliveryContext -> outbound delivery**

否则它就会退化成“能跑但不稳定的 bot”。

### 4. 第四环：context / memory governance 决定它能不能长期不失控

如果没有 context governance，系统不是失忆，就是 prompt 爆炸。

这一环包含：

- system prompt 结构化装配
- bootstrap / context files 预算控制
- `memory_search` / `memory_get` 作为按需召回面
- memory flush / post-compaction refresh
- prompt report / cache boundary

我的判断：

> **长期助理的关键不只是“有记忆”，而是“能治理上下文生命周期”。**

这就是闭环第四环。

### 5. 第五环：tool execution bus 决定它是不是“会干活的 runtime”

如果只有会话和记忆，它还只是一个很会聊的系统。

真正让 OpenClaw 成为 runtime 的，是：

- `exec/process`
- `sessions_spawn/subagents`
- `cron`
- 统一 tool surface
- 外围 tool policy

这让系统不只是“给建议”，而是能：

- 执行命令
- 起子会话
- 计划未来任务
- 管理后台进程

所以闭环第五环其实回答的是：

> **系统除了会理解，会不会真正行动。**

### 6. 第六环：active continuation 决定它是不是“持续存在的助理”

这个环节把 OpenClaw 和普通响应式 agent 拉开了。

active continuation 包括三种形态：

- **followup-runner**：沿原 session 继续跑
- **heartbeat**：低频守望，必要时唤醒、提醒
- **cron isolated-agent**：按计划主动起一轮 agent turn

如果没有这层，OpenClaw 仍然会很强，但更像：

- 会话型 agent
- 而不是持续型个人助理

所以闭环第六环决定的是：

> **系统在没有新消息时，是否仍然“活着”。**

### 7. 第七环：plugin / channel docking 决定它能不能活在真实世界表面上

最后一环不是可选项。

长期个人助理必须活在：

- Feishu
- Telegram
- Discord
- Slack
- Webchat
- 其他通信表面

但核心 runtime 又不能被这些平台语义反向污染。

所以必须有：

- plugin registry
- channel registry pinning
- outbound adapter seam
- session conversation resolver
- target normalization / approval adapters

这就是闭环第七环。

一句话说：

> **系统不仅要会跑，还得能稳定活在你真正和它接触的表面上。**

## 一个最小闭环总图

如果把这 7 环压成一张简图，我会这样画：

```text
1. Session topology
   ↓
2. Reply / run loop
   ↓
3. Routing / delivery continuity
   ↓
4. Context / memory governance
   ↓
5. Tool execution bus
   ↓
6. Active continuation
   ↓
7. Plugin / channel docking
```

更准确一点，它不是线性流水线，而是一个闭环：

- session 承载所有运行
- run loop 驱动一次 turn
- routing 决定归属
- memory/prompt 决定这一轮看到什么
- tools 决定这一轮能做什么
- heartbeat/followup/cron 决定未来是否继续跑
- plugin/channel 决定结果如何活在真实世界
- 结果再回写 session / transcript，形成下一轮的基础

所以真正的闭环应该理解成：

> **Session-centered runtime loop**

而不是单向流程图。

## 如果必须裁到更小，还能不能再少？

可以，但会明显降级。

### 可以裁，但会降级的

#### 去掉 plugin/channel docking
系统还能在 CLI / 本地环境跑，但不再像个人助理，更像本地 agent runtime。

#### 去掉 cron / heartbeat
系统还能做长期会话，但不再像“持续存在的助理”，更像等待输入的 agent。

#### 去掉 sessions_spawn / subagents
系统仍可用，但会失去多 agent runtime 的很多价值。

### 不能轻易裁的

#### 1. session topology
没有它，长期 continuity 就塌了。

#### 2. routing / delivery continuity
没有它，多 channel / 多 thread 很快串台。

#### 3. context / memory governance
没有它，长期运行必然失控。

#### 4. run loop
没有它，其他层都没有执行意义。

所以如果你要一个“最小但仍像长期个人助理”的 OpenClaw 内核，我的判断是：

> **session + run loop + routing/delivery + context governance + 一种主动续跑机制 + 一种动作执行机制**

这几项基本不能少。

## 最终判断

### OpenClaw 作为长期个人助理 runtime 的最小闭环，不是一个点，而是一个系统组合

把它收成一句话就是：

> **长期个人助理 = 长期会话骨架 + 稳定运行主循环 + 正确回路由 + 可治理上下文 + 可执行动作 + 可持续续跑 + 可宿主到真实通信表面。**

这就是我现在对 OpenClaw 主线的总收束。

## 关键术语解释

- **minimal closure**：在不失去系统核心能力的前提下，维持某种产品形态所需的最小机制闭环。
- **session-centered runtime loop**：以 session 为中心，把上下文、执行、交付、续跑都串起来的运行闭环。
- **delivery continuity**：结果始终能回到正确会话面和正确 thread/channel 的连续性能力。
- **context governance**：对 prompt、memory、压缩、召回和预算的系统治理能力。
- **active continuation**：系统在没有新用户输入时，仍能继续运行或再次唤醒的能力。
- **channel docking**：把统一 runtime 能力挂接到具体通信平台表面的过程。

## 本轮遗留问题

1. 这篇已经是一次总收束了。后面如果继续，我建议不要再平铺模块，而是转成两种方向之一：
   - **方向 A：做一篇《OpenClaw 哪些能力值得移植到更轻的个人助理系统》**
   - **方向 B：做一篇《OpenClaw 哪些复杂度是必须的，哪些是可以裁掉的》**
2. 如果回到工程细节，`tool policy` 和 `plugin registry lifecycle` 是两个最值得深挖的治理点。
3. 如果回到产品视角，也可以专门写一篇：`为什么 OpenClaw 更像助理操作系统，而 Claude Code 更像顶级执行器` 的最终收束版。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/OIemdnLfuoVLJmxJhFCclpy6nVg`)
- Vault git sync: in progress
