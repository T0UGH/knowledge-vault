---
title: OpenClaw 共读：heartbeat 机制如何支撑长期个人助理
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - heartbeat
  - long-running-agent
status: active
---

# OpenClaw 共读：heartbeat 机制如何支撑长期个人助理

## 一句话结论

heartbeat 是 OpenClaw 从“被动响应型 agent”走向“长期存在的个人助理”的关键桥梁。它本质上不是一个定时发消息的小功能，而是一条 **预检 → 选目标 session / delivery → 生成 heartbeat prompt → 走正常 reply/agent run → 过滤 HEARTBEAT_OK 噪音 → 必要时外发/必要时剪 transcript** 的受控运行链。换句话说，**heartbeat 不是外挂提醒器，而是把助理从“有人敲一下才动”升级成“会自己醒、会自己看、会自己决定要不要打扰你”的机制。**

## 这部分在系统里负责什么

heartbeat 这一层解决的是长期个人助理才会遇到的问题：

- 当没有新用户消息时，系统怎么还能自己运行
- 哪些时间应该主动醒来，哪些时间应该保持安静
- 醒来后，是继续挂在原 session 上，还是开独立 heartbeat session
- 检查完如果没事，怎么尽量不污染 transcript、不打扰用户
- 如果有 cron 事件、exec completion、wake reason，怎么把这些事件收口进同一条长期助理主线

所以 heartbeat 真正在做的，不是“定时 ping 模型”，而是：

> **给 OpenClaw 一条没有新用户输入时仍然能安全运行的主循环。**

## 主链路

我这轮抓的主链路是这几段：

1. **heartbeat prompt 和 HEARTBEAT.md 约定定义在 `auto-reply/heartbeat.ts`**  
   `src/auto-reply/heartbeat.ts`
2. **wake 层负责 coalescing / retry / target-aware scheduling**  
   `src/infra/heartbeat-wake.ts`
3. **`runHeartbeatOnce()` 是真正的 heartbeat 主循环**  
   `src/infra/heartbeat-runner.ts`
4. **preflight 先做启用判断、quiet hours、requests-in-flight、HEARTBEAT.md gating、事件分类**
5. **delivery target 再决定这次 heartbeat 回哪、能不能回、是否允许 direct**  
   `src/infra/outbound/targets.ts`
6. **heartbeat 实际执行仍然复用正常 reply pipeline，只是带上 `isHeartbeat` 等运行标记**
7. **`HEARTBEAT_OK` / 空结果 / 重复结果会走特殊过滤和 transcript prune**  
   `src/auto-reply/heartbeat.ts` + `src/infra/heartbeat-events-filter.ts` + `src/infra/heartbeat-runner.ts`
8. **cron / exec completion / wake reason 会通过 heartbeat prompt 分流成不同的主动处理语义**

一句话串起来：

> **OpenClaw 先决定这次 heartbeat 应不应该醒、醒给谁、带什么上下文，再复用正常 agent run 去思考，最后只把真正值得打扰你的结果送出来。**

## 关键文件与代码片段

### 1. `auto-reply/heartbeat.ts`：heartbeat 的起点不是 scheduler，而是协议

这里最核心的是默认 prompt：

```ts
export const HEARTBEAT_PROMPT =
  "Read HEARTBEAT.md if it exists (workspace context). Follow it strictly. Do not infer or repeat old tasks from prior chats. If nothing needs attention, reply HEARTBEAT_OK.";
```

这段话其实已经把 heartbeat 的设计立场说得很清楚：

- 默认先看 `HEARTBEAT.md`
- 只做文件里明确要求的事
- 不要自己脑补旧任务
- 没事就回 `HEARTBEAT_OK`

我的判断：

> **heartbeat 首先是一个“主动性约束协议”，不是一个鼓励 agent 到处找活干的开关。**

这个点特别重要。否则长期助理很容易变成高频打扰器。

### 2. `runHeartbeatOnce()`：heartbeat 真正复用的是正常 reply/agent pipeline

这段是整篇最重要的代码之一：

```ts
const replyOpts = heartbeatModelOverride
  ? {
      isHeartbeat: true,
      heartbeatModelOverride,
      suppressToolErrorWarnings,
      bootstrapContextMode,
    }
  : { isHeartbeat: true, suppressToolErrorWarnings, bootstrapContextMode };
...
const replyResult = await getReplyFromConfig(ctx, replyOpts, cfg);
```

这里很关键。

heartbeat 并没有单独造一套“心跳专用 agent 引擎”，而是：

- 构造一份特殊的 heartbeat ctx
- 打上 `isHeartbeat: true`
- 再走正常的 `getReplyFromConfig -> reply pipeline`

这说明：

> **heartbeat 不是旁路系统，而是复用主执行链的一种特殊运行模式。**

这和前面读 commands vs embedded run mode 的结论是闭环的：OpenClaw 更倾向“同一主循环的不同运行模式”，而不是“每种功能各起一套执行系统”。

### 3. `runHeartbeatOnce()`：长期助理的关键不是会醒，而是知道什么时候别醒

heartbeat 主循环一开始先做这些拦截：

- `areHeartbeatsEnabled()`
- `isHeartbeatEnabledForAgent()`
- `resolveHeartbeatIntervalMs()`
- `isWithinActiveHours()`
- `getQueueSize(CommandLane.Main)`

也就是：

- 总开关开没开
- 这个 agent 有没有 heartbeat 配置
- 轮询间隔是否合法
- 当前是不是 quiet hours
- 主 lane 里是不是已经有请求在飞

这一套特别像长期助理系统该有的守门逻辑。

我的判断：

> **长期助理的成熟度，不在于“能主动”，而在于“知道什么时候不该主动”。**

### 4. `resolveHeartbeatPreflight()`：heartbeat 真正先做的是“是否值得跑这一轮”

从主循环能看到 preflight 会干这些事：

- 检查 `HEARTBEAT.md` 是否存在、是否有效为空
- 解析 tasks block
- 判断这轮有没有 due tasks
- 分类 cron events / exec completion / wake reason
- 确定 session / store / turn source 等预备信息

尤其这段很关键：

```ts
if (isHeartbeatContentEffectivelyEmpty(heartbeatFileContent) && tasks.length === 0) {
  return { skipReason: "empty-heartbeat-file", ... }
}
```

以及：

```ts
// No tasks due - skip this heartbeat to avoid wasteful API calls
return { prompt: null, ... }
```

这说明 heartbeat 不是“到点一定调模型”，而是：

> **先判断有没有值得做的事，没事就连 API 都不打。**

这非常像一个真正重视长期运行成本的个人助理系统。

### 5. `heartbeat-wake.ts`：wake 层不是简单 timer，而是带 coalescing / retry / target 的唤醒协调器

这文件很值钱。

它维护的是：

- `pendingWakes`
- `scheduled`
- `running`
- `timerDueAt`
- `timerKind`

并且 wake reason 还有优先级：

- `RETRY`
- `INTERVAL`
- `ACTION`

还有 target-aware key：

```ts
const wakeTargetKey = `${agentId ?? ""}::${sessionKey ?? ""}`;
```

我的判断：

> **heartbeat wake 层本质上是一个轻量调度器，不只是 setTimeout 包装。**

它的价值在于：

- 多个 wake 不会乱炸
- requests-in-flight 时会 retry
- 不同 agent / session 的唤醒能并存
- 生命周期重启时会清掉旧 timer 元状态

这正是长期助理系统需要的“低频但稳定”的后台唤醒层。

### 6. `resolveHeartbeatDeliveryTarget()`：heartbeat 能不能变成“个人助理”，关键看它知道该回哪

这段非常关键。heartbeat delivery target 不是死写的，而是会综合：

- `heartbeat.target`
- `heartbeat.to`
- `heartbeat.accountId`
- session 的 `deliveryContext`
- turn source context
- directPolicy

而且 heartbeat mode 会特别处理 thread 继承：

```ts
// heartbeat mode intentionally drops inherited thread IDs to avoid replying
// in stale threads
```

这说明 OpenClaw 非常清楚 heartbeat 这种主动消息和普通回复不一样：

> **主动消息最怕回到一个已经过时的 thread。**

所以 heartbeat 在 delivery 上会更保守，这个取舍非常对。

### 7. `HEARTBEAT_OK` + transcript prune：这是长期助理体验里特别值钱的一个细节

heartbeat 最成熟的一点，不是会主动，而是会尽量“无痕”。

从主循环看，以下几种情况都会 special-case：

- 空结果
- `HEARTBEAT_OK`
- 只剩很短 ack 文本
- 24h 内重复 heartbeat payload

对应逻辑包括：

- `normalizeHeartbeatReply()`
- `stripHeartbeatToken()`
- `pruneHeartbeatTranscript()`
- duplicate suppression

一句话判断：

> **OpenClaw 的 heartbeat 不是为了“留下每一次心跳记录”，而是为了“必要时介入，不必要时尽量像没来过”。**

这非常像真正的个人助理，而不是日志型 bot。

### 8. heartbeat 会把 cron / exec completion / wake events 都折叠进同一条主动处理面

`heartbeat-events-filter.ts` 和 `runHeartbeatOnce()` 这一组很说明问题。

如果有：

- cron event
- exec completion
- heartbeat wake action

它不会要求系统各自造一套独立“通知逻辑”，而是会统一转成 heartbeat prompt 语义，比如：

- `A scheduled reminder has been triggered...`
- `An async command you ran earlier has completed...`
- `Handle this internally ... HEARTBEAT_OK`

这说明 heartbeat 不只是 timer 功能，而是：

> **长期助理的统一主动处理入口。**

## 设计判断

### 1. heartbeat 是 OpenClaw 从“响应式 agent”变成“持续型助理”的关键桥梁

这轮最核心的判断就是这个。

没有 heartbeat，OpenClaw 再强也主要是：

- 你发消息
- 它回复
- 也许能起 subagent
- 也许能跑 cron

但 heartbeat 一加进来，系统就多了一个维度：

> **没有新用户输入时，系统也能定期醒来、检查、判断、必要时通知。**

这才真正像个人助理，而不是会话执行器。

### 2. heartbeat 的成熟点不在“主动”，而在“主动性的节制”

这一篇里最让我认同的地方，是它几乎每一步都在克制：

- quiet hours
- requests-in-flight 跳过
- `HEARTBEAT.md` 空文件跳过
- no-tasks-due 跳过
- `HEARTBEAT_OK` 不污染 transcript
- duplicate payload suppression
- directPolicy / stale thread 防护

这说明 OpenClaw 对长期助理有一个成熟认识：

> **好助理不是一直在说话，而是该说的时候说，不该说的时候尽量安静。**

### 3. heartbeat 不是独立执行器，而是复用主 reply pipeline 的特殊运行模式

这点也很重要。

OpenClaw 没有为 heartbeat 单独造一套 agent engine，而是：

- 用特殊 ctx
- 加 `isHeartbeat`
- 可选 `lightContext`
- 可选 heartbeat model override
- 继续走正常 reply pipeline

这让 heartbeat 和主会话能力天然共享：

- session
- memory
- skills
- routing
- delivery
- prompt assembly

这比单独分叉一套“心跳机器人逻辑”更稳。

### 4. wake 层 + preflight 层 + delivery 层，三者一起才构成长期助理能力

单看其中一层都不够：

- 只有 wake，没有 preflight → 会频繁无意义调用模型
- 只有 preflight，没有 delivery → 有事也不知道回哪
- 只有 delivery，没有 wake → 永远不会主动跑

所以 heartbeat 不是一个点，而是一个三层闭环：

- **wake**：何时醒
- **preflight**：值不值得跑
- **delivery**：结果回哪

### 5. 从主线角度，heartbeat 比很多“深层 runtime 细节”都更重要

如果目标是理解 OpenClaw 为什么能做长期个人助理，那 heartbeat 比继续深挖某个 loader / env override / plugin backend 都更主线。

因为 heartbeat 直接回答了：

> **这个系统如何在没有新消息时依然活着。**

这是长期助理和普通 agent 最大的分水岭之一。

## 关键术语解释

- **heartbeat**：OpenClaw 中用于定期或被动唤醒 agent、执行检查任务、必要时向用户回报结果的长期运行机制。
- **HEARTBEAT.md**：工作区内定义周期性检查任务和提示的文件，是 heartbeat 的主要显式任务来源。
- **HEARTBEAT_OK**：heartbeat 在无用户可见后续动作时返回的标准确认 token。
- **preflight**：heartbeat 真正执行前的预检阶段，用于判定是否应当跳过本轮运行。
- **isolatedSession**：为 heartbeat 单独创建的专用 session 模式，避免长历史拖重上下文。
- **quiet hours / active hours**：heartbeat 允许运行或应当静默的时间窗口策略。
- **wake layer**：负责 heartbeat 唤醒、合并请求、重试和目标化调度的协调层。
- **transcript prune**：在 heartbeat 只产生 `HEARTBEAT_OK` 或重复结果时，回滚这轮 transcript 污染的机制。
- **delivery target**：heartbeat 主动消息最终应该送达的 channel / to / accountId / threadId 目标。

## 本轮遗留问题

1. 后面很值得专门补一篇：heartbeat、cron isolated-agent、followup-runner 三者到底是什么关系，边界怎么划。
2. `resolveHeartbeatPreflight()` 还可以更细读一轮，尤其是 task 调度和 event-driven heartbeat 的合流点。
3. isolated heartbeat session 和主 session 之间的上下文取舍，值得专门做一次“token economics”视角的对照。
4. 如果以后真把 OpenClaw 往“长期个人助理产品”方向推，heartbeat visibility / duplicate suppression / quiet hours 这些策略会是非常关键的产品面。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/VPSUdOEZXovpJtx6sp7ceT0gnyf`)
- Vault git sync: done (`f1d4228 add OpenClaw co-reading note: heartbeat-mechanism-for-long-running-assistant`)
