---
title: OpenClaw 共读：cron isolated-agent / heartbeat / followup-runner 三者关系
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - cron
  - heartbeat
  - followup
status: active
---

# OpenClaw 共读：cron isolated-agent / heartbeat / followup-runner 三者关系

## 一句话结论

cron isolated-agent、heartbeat、followup-runner 不是三套互相平行的“后台能力”，而是 OpenClaw 长期助理 runtime 的三种不同续跑形态：**cron isolated-agent 负责“按计划主动起一个 agent turn”，heartbeat 负责“按节律/事件唤醒并决定是否打扰用户”，followup-runner 负责“沿当前会话把后续 turn 接着跑下去”。** 三者共享同一套 session / skills / model / delivery / agent execution 基础设施，但它们的 **触发源、session 策略、delivery 契约、静默默认值** 都不同。

## 这部分在系统里负责什么

如果只看表面，你会觉得这三样都像“后台运行”。

但从主线角度看，它们解决的是三种不同问题：

- **followup-runner**：当前用户会话还没真正结束，需要沿这条 session 继续跑后续 turn
- **heartbeat**：没有新消息时，系统需要定期醒来检查、处理事件，必要时再通知用户
- **cron isolated-agent**：系统需要在指定时间主动创建一次 agent turn，完成独立任务或提醒

也就是说，它们分别对应：

> **会话内续跑、低频守望、计划性主动执行。**

如果没有把这三者分清，就很容易把 OpenClaw 理解成“凡是后台事情都靠一个统一 scheduler 顶着”。实际上不是，它的长期运行能力是分层设计的。

## 主链路

我这轮抓的主链路是这几段：

1. **followup-runner 从当前 reply queue 中取 `FollowupRun`，继续沿原 session 执行**  
   `src/auto-reply/reply/followup-runner.ts`
2. **heartbeat 用 `runHeartbeatOnce()` 做预检、选 session、选 delivery，再复用正常 reply pipeline**  
   `src/infra/heartbeat-runner.ts`
3. **heartbeat 的 isolatedSession 直接复用 cron isolated session 的建会话模式**  
   `src/infra/heartbeat-runner.ts` + `src/cron/isolated-agent/session.ts`
4. **cron isolated-agent 用 `runCronAgentTurn()` 在独立 session 上执行 agent turn**  
   `src/cron/isolated-agent/run.ts`
5. **cron 的 `sessionTarget` 和 `deliveryPlan` 决定这次主动运行挂在哪条会话线、是否自动 announce**  
   `src/cron/session-target.ts` + `src/cron/delivery-plan.ts`
6. **三者最终都共享 `runEmbeddedPiAgent()` / model selection / skills snapshot / routing / delivery 这些底层 runtime 设施**

一句话串起来：

> **followup-runner 是“沿原会话继续跑”，heartbeat 是“低频唤醒后再决定要不要跑/要不要发”，cron isolated-agent 是“按计划新起一轮 agent turn”；三者用的是同一台引擎，但开的不是同一条车道。**

## 关键文件与代码片段

### 1. `followup-runner.ts`：followup-runner 的核心是“沿原会话续跑”，不是新建独立任务

这文件一上来就暴露了核心对象：`FollowupRun`。

它继承的是当前 reply turn 已经准备好的运行上下文：

- `queued.run.sessionId`
- `queued.run.sessionKey`
- `queued.run.sessionFile`
- `queued.run.skillsSnapshot`
- `queued.run.extraSystemPrompt`
- `queued.prompt`
- `queued.originatingChannel / To / ThreadId`

然后最后还是会收口到：

```ts
const result = await runEmbeddedPiAgent({
  sessionId: queued.run.sessionId,
  sessionKey: queued.run.sessionKey,
  ...
  skillsSnapshot: queued.run.skillsSnapshot,
  prompt: queued.prompt,
  extraSystemPrompt: queued.run.extraSystemPrompt,
  ...
})
```

我的判断：

> **followup-runner 的本质不是后台任务调度器，而是当前会话主循环的异步续跑器。**

它最像的是：这条 session 还有后续动作没跑完，那就继续沿它原来的 run context 往下跑，而不是重新起一条新会话线。

### 2. `followup-runner.ts`：它对“回原处”极其敏感

这文件里还有一层很关键：`sendFollowupPayloads()` 优先尝试回原 originating channel / to / thread。

也就是说 followup-runner 的 delivery 假设是：

> **这次后续输出，仍然属于原来那条对话面。**

这和 heartbeat / cron 有本质区别。后两者更偏“主动触达”，而 followup-runner 更偏“把未完成的这一轮补完”。

### 3. `heartbeat-runner.ts`：heartbeat 是“先判断是否值得跑”，不是直接起 agent turn

heartbeat 主循环最值钱的地方，在于它不是到点就跑，而是先做：

- heartbeatsEnabled
- per-agent heartbeat enable
- interval / active hours
- requests-in-flight
- preflight（HEARTBEAT.md、cron events、exec completion、tasks due）
- delivery target resolution

然后只有确定“值得跑”后，才会：

```ts
const replyResult = await getReplyFromConfig(ctx, replyOpts, cfg);
```

我的判断：

> **heartbeat 更像“守望层 + 机会性运行层”，不是稳定产生用户可见输出的主动任务层。**

这也是为什么它会有：

- `HEARTBEAT_OK`
- transcript prune
- duplicate suppression
- quiet hours
- no-tasks-due skip

这些东西都在说明：heartbeat 的默认目标不是“生产结果”，而是“维持助理存在感但尽量不打扰”。

### 4. `heartbeat-runner.ts`：heartbeat 的 isolatedSession 其实直接借了 cron 的 session 模式

这段特别关键：

```ts
const useIsolatedSession = heartbeat?.isolatedSession === true;
...
const isolatedKey = `${sessionKey}:heartbeat`;
const cronSession = resolveCronSession({
  cfg,
  sessionKey: isolatedKey,
  agentId,
  nowMs: startedAt,
  forceNew: true,
});
```

注释直接说明：

- heartbeat isolated session 使用和 cron `sessionTarget: "isolated"` 同样的模式
- 给 heartbeat 一个全新的 sessionId / 空 transcript
- 避免把完整历史都塞给模型

这点特别说明问题：

> **heartbeat 不是独立发明了一套 session 策略，而是复用了 cron isolated-agent 那套“新起独立 session 执行”的思路。**

这也是这三者关系里最重要的连接点之一。

### 5. `cron/isolated-agent/session.ts`：cron isolated session 的核心价值是“按策略复用或新建会话，但新 session 要清空 delivery routing 污染”

`resolveCronSession()` 做的事不花哨，但很关键：

- 读 store
- 判断现有 entry 是否 fresh
- 需要时生成新 `sessionId`
- fresh session 可以复用旧 `sessionId`
- 新 session 时清 `lastChannel / lastTo / lastThreadId / deliveryContext`

尤其这个清理动作非常对：

> **独立主动运行的新 session 不应该继承旧 session 的 thread 路由脏状态。**

这正是 cron isolated-agent 和 followup-runner 的区别：

- followup-runner 要尽量继承当前会话 delivery continuity
- isolated cron / heartbeat 新 session 则要谨慎切断旧路由污染

### 6. `cron/delivery-plan.ts`：cron 的 delivery 是显式契约，不是默认“回原处”

这文件很能说明 cron 的定位。

它会先看 job 的 `delivery`，然后得出：

- `mode: announce | webhook | none`
- `channel`
- `to`
- `threadId`
- `accountId`
- `requested`

而且默认规则也很有意思：

- `sessionTarget = main` + `payload.kind = systemEvent`
- `sessionTarget = isolated/current/session:*` + `payload.kind = agentTurn`

一句话判断：

> **cron 更像“计划性主动执行 + 显式交付契约”，而不是会话内续跑。**

这和 followup-runner 完全不是一回事。

### 7. `cron/isolated-agent/run.ts`：isolated-agent 是真正的“计划性 agent turn 运行器”

这文件的体量已经说明它不是小配件。它在准备：

- agentId / defaults merge
- delivery plan
- tool policy
- workspaceDir / agentDir
- cron session
- live model selection
- skills snapshot
- timeout / think level
- run execution
- delivery dispatch
- session persistence

我的判断：

> **cron isolated-agent 是 OpenClaw 里最接近“计划性独立 agent 任务执行器”的那一层。**

如果 followup-runner 代表“会话续跑”，heartbeat 代表“低频守望”，那 cron isolated-agent 代表的就是：

> **按时间表主动发起一轮正式 agent turn。**

### 8. 三者共享的是同一套底层 runtime，不共享的是运行契约

这是这轮最重要的收束点。

它们共享：

- session store / transcript
- `runEmbeddedPiAgent()`
- model selection / auth profile
- skills snapshot
- delivery routing / outbound
- prompt assembly

但不共享：

- **触发源**
  - followup：当前会话内部后续动作
  - heartbeat：interval / wake / exec completion / cron event / HEARTBEAT.md
  - cron：计划任务触发

- **session 策略**
  - followup：继承当前 session
  - heartbeat：默认基于主 session，可选 isolated `:heartbeat`
  - cron：main / current / isolated / custom session target

- **delivery 默认值**
  - followup：尽量回原 originating conversation
  - heartbeat：尽量克制，必要时主动触达
  - cron：按 delivery plan 明确 announce / webhook / none

- **静默默认值**
  - followup：默认不是为静默设计
  - heartbeat：默认强静默倾向（HEARTBEAT_OK / prune / dedupe）
  - cron：取决于 job delivery contract

## 设计判断

### 1. 这三者分别占了“持续运行”问题的三个不同维度

这是我这轮最核心的判断：

#### followup-runner
解决的是：
> **这一轮还没跑完，怎么沿当前会话继续。**

#### heartbeat
解决的是：
> **没人发消息时，系统怎么低频醒来检查，又尽量别烦人。**

#### cron isolated-agent
解决的是：
> **系统怎么在指定时间主动起一轮正式 agent 任务。**

这三个问题不一样，所以不应该硬塞进一个“后台任务框架”里理解。

### 2. heartbeat 和 cron isolated-agent 的最大关系，是“heartbeat 借用了 cron 的 isolated session 思路”

这点非常关键。

heartbeat 不是一套完全独立的主动运行系统。它在 isolated session 这个点上，明显就是复用了 cron 的机制。

这说明 OpenClaw 的架构演进很像：

> **先形成一套独立主动 run 的 session 模式，再让 heartbeat 借这套能力实现更轻、更安静的周期守望。**

### 3. followup-runner 最不像 scheduler，它最像 session continuity machinery

很多人会把 followup-runner 也和 cron/heartbeat 一起归到“后台任务”。

我觉得这不对。

followup-runner 的本质是：

- 接住当前会话里后续应该继续跑的动作
- 保持 session continuity
- 保持原 originating conversation continuity

它是 **会话延续机制**，不是 **计划执行机制**。

### 4. OpenClaw 的长期助理能力，恰恰来自这三条线没有被混成一坨

如果把这三者混在一起，系统会很快变得难懂又难治理。

现在这套分工虽然复杂，但其实是清楚的：

- 会话内续跑 → followup-runner
- 周期守望 / 被动唤醒 → heartbeat
- 计划性主动执行 → cron isolated-agent

这正是一个长期助理 runtime 应该有的层次感。

### 5. trade-off：长期运行能力更完整，但运行形态也因此更难一眼看懂

这套设计的代价也很明显：

- 新人很难一眼分清“这次后台动作到底是谁触发的”
- heartbeat / cron / followup 的日志和 delivery 行为不一样
- session 继承与 session 新建的边界需要严格理解

也就是说，OpenClaw 用更清晰的长期运行分层，换来了更高的运行时认知成本。

但我觉得这是值得的，因为这正是“长期个人助理”与“简单聊天 agent”之间的真实复杂度差异。

## 关键术语解释

- **followup-runner**：沿当前会话继续执行后续 agent turn 的异步续跑器。
- **heartbeat**：在无新用户输入时定期或事件驱动唤醒 agent 的守望机制，强调低打扰和主动性节制。
- **cron isolated-agent**：按计划启动的独立 agent turn 运行器，通常运行在独立或显式指定的 session 上。
- **sessionTarget**：cron job 指定本次执行应当挂在哪条会话线上的策略，如 `main`、`isolated`、`current`、`session:*`。
- **delivery plan**：cron 对本次任务结果如何交付的显式契约，如 `announce`、`webhook`、`none`。
- **isolated session**：为主动任务单独创建的新 session，避免继承旧会话上下文和路由污染。
- **session continuity**：同一条会话线在多轮运行中保持上下文、路由和交互连续性的能力。
- **主动运行形态**：系统在没有直接用户消息时仍继续工作的运行模式，包括 heartbeat 和 cron 等。

## 本轮遗留问题

1. 下一篇很适合补 `session topology` 总结篇，把 main / subagent / heartbeat / isolated cron session 全部画成一张图。
2. `cron service` 本体还没细读，后面可以专门看 timer、jobs、store、delivery-failure 策略如何把 isolated-agent 托管起来。
3. 如果以后想把 OpenClaw 裁剪成更轻的个人助理 runtime，followup-runner、heartbeat、cron 三者里哪些是必须保留的，值得专门做一次“最小闭环”判断。
4. 还可以继续补一篇：`heartbeat` 和 `cron main/systemEvent` 的关系，为什么有些主动事件走 systemEvent，有些走 agentTurn。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/OoZUdQep8oLgrAxVr3Zcl1Eunph`)
- Vault git sync: done (`b5d9884 add OpenClaw co-reading note: cron-isolated-agent-heartbeat-followup-runner`)
