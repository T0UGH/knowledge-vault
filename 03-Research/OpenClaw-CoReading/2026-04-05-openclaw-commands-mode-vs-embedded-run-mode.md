---
title: OpenClaw 共读：commands mode vs embedded run mode
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - commands
  - embedded-run
status: active
---

# OpenClaw 共读：commands mode vs embedded run mode

## 一句话结论

OpenClaw 的运行主线不是单一路径，而是一个 **先判定命令控制面、再决定是否进入 embedded agent run** 的双层结构：`getReplyRun` 先把消息整理成 session-aware followup run，同时给 commands path 一个优先拦截机会；只有命令链判定“shouldContinue”后，才真正进入 `runReplyAgent -> runEmbeddedPiAgent` 这条 agent 主执行路径。换句话说，**commands mode 是控制面，embedded run mode 是执行面。**

## 这部分在系统里负责什么

这一层回答的是 OpenClaw 主循环里最关键的一个分叉问题：

- 一条消息进来，什么时候应该被当成 `/context`、`/reset`、`/status` 这类命令处理
- 什么时候应该进入真正的 agent run
- commands path 和 embedded path 到底共享哪些底层设施
- 为什么 OpenClaw 既能像“命令型助理”，又能像“长期 agent runtime”

前面几篇已经把 session、routing、memory、skills、prompt 这些部件拆开看过了。

这一篇真正补的是：

> **这些部件在一轮真实运行里，是怎样沿着 commands path 和 embedded path 分工协作的。**

## 主链路

我这轮抓的主链路是这几段：

1. **`buildCommandContext()` 先把 inbound body 归一化成 command 视图**  
   `src/auto-reply/reply/commands-context.ts`
2. **`getReplyRun()` 负责统一准备 session、followupRun、skillsSnapshot、system events、extraSystemPrompt**  
   `src/auto-reply/reply/get-reply-run.ts`
3. **命令相关内容先经 `handleCommands()` 拦截**  
   `src/auto-reply/reply/commands-core.ts`
4. **如果命令链返回 `shouldContinue: false`，本轮停在 commands mode**
5. **如果命令链返回 `shouldContinue: true`，则进入 `runReplyAgent()`**  
   `src/auto-reply/reply/agent-runner-execution.ts`
6. **真正的 agent execution 再统一收口到 `runEmbeddedPiAgent()` 或 CLI provider path**  
   `src/agents/command/attempt-execution.ts`
7. **embedded run 继承 session / skillsSnapshot / prompt / routing / auth / media 等运行期参数**

一句话串起来：

> **commands path 先决定“这是不是该由控制面处理的消息”，embedded path 再负责“把这轮真正送进 agent runtime 执行”。**

## 关键文件与代码片段

### 1. `commands-context.ts`：commands mode 的入口不是裸字符串，而是规范化的 command context

这段很关键：

```ts
const commandBodyNormalized = normalizeCommandBody(
  isGroup ? stripMentions(rawBodyNormalized, ctx, cfg, agentId) : rawBodyNormalized,
  { botUsername: ctx.BotUsername },
);
```

然后生成：

```ts
return {
  surface,
  channel,
  ownerList,
  senderIsOwner,
  isAuthorizedSender,
  abortKey,
  rawBodyNormalized,
  commandBodyNormalized,
  from,
  to,
}
```

我的理解：

commands mode 从一开始就不是“看到 `/` 就算命令”，而是先做：

- mention stripping
- command body normalization
- sender authorization
- surface/channel 识别
- abort key 生成

这说明 commands path 不是 UI 语法糖，而是正式控制面入口。

### 2. `get-reply-run.ts`：主循环先统一准备 run context，再决定后续是否真跑 agent

这是整篇最关键的分层点。

在真正跑 agent 前，`getReplyRun()` 已经做了很多事：

- session first-turn / group intro 判定
- system events drain
- untrusted context 注入
- thread history / starter context
- media note 处理
- `ensureSkillSnapshot()`
- `followupRun` 构造
- `extraSystemPrompt` 拼接
- auth profile / queue mode / lane / queueKey

比如这里：

```ts
const skillResult = await ensureSkillSnapshot(...)
...
const followupRun = {
  prompt: queuedBody,
  ...
  run: {
    agentId,
    sessionId: sessionIdFinal,
    sessionKey,
    sessionFile,
    workspaceDir,
    config: cfg,
    skillsSnapshot,
    provider,
    model,
    extraSystemPrompt: extraSystemPromptParts.join("\n\n") || undefined,
    ...
  },
}
```

这个设计特别说明问题：

> **commands mode 和 embedded mode 不是各自从零起盘，它们共享的是一层已经准备好的 run context。**

也就是说，主循环先把平台级上下文准备好，再决定这轮到底走控制面还是执行面。

### 3. `commands-core.ts`：commands mode 是一个 handler chain，不是单一 if-else

`handleCommands()` 的结构很清楚：

```ts
for (const handler of HANDLERS) {
  const result = await handler(params, allowTextCommands);
  if (result) {
    return result;
  }
}
...
return { shouldContinue: true };
```

这说明 command path 的职责不是“完成所有工作”，而是：

- 看这条消息是否应该被控制面拦截
- 如果拦截，就直接产出 reply 或执行副作用
- 如果不拦截，明确放行到 embedded run

这和普通聊天系统里“命令只是一个特殊 prompt 模式”不一样。

OpenClaw 这里更像：

> **在 agent runtime 前面挂了一层控制面路由器。**

### 4. `commands-core.ts`：reset/new 这种命令不是回复文本，而是直接碰运行时状态

这段能看出 command path 为什么是控制面：

- emit reset hooks
- ACP bound conversation reset
- before_reset plugin hook
- transcript 读取
- binding target reset

也就是说，像 `/reset`、`/new` 这种命令根本不是“让模型回复一句 reset 成功”，而是：

> **直接操作 session / transcript / hook / binding 这些 runtime 结构。**

这进一步证明 commands mode 是平台控制面，而不是另一种聊天风格。

### 5. `handleCommands()` 的 `shouldContinue` 是主循环分叉闸门

整个主循环最有价值的一个小设计，其实就是这个返回值：

- `shouldContinue: false` → 本轮停在 commands path
- `shouldContinue: true` → 放行给 agent run

这让 commands mode 和 embedded mode 的边界非常清楚：

- command handlers 只决定“是否截流”
- 真正 agent 推理不在 commands core 里做

这个分层我很认同。

### 6. `agent-runner-execution.ts`：embedded run path 才是长会话 agent 执行主线

虽然这个文件很长，但最关键的点很简单：

- 它负责 provider/model fallback
- 负责 reply delivery pipeline
- 负责 block replies / compaction / retries
- 最终调用 `runEmbeddedPiAgent()` 或 CLI provider path

这里说明：

> **embedded run mode 才是 OpenClaw 真正的 agent execution core。**

commands mode 没有替代它，只是在它前面负责控制面分流。

### 7. `attempt-execution.ts`：commands path 的某些命令最终也会落到 `runEmbeddedPiAgent()`

这点很有意思，也最能说明“双层结构”而不是“两套完全独立系统”。

在 `runAgentAttempt()` 里，无论是普通 followup run，还是 command-triggered execution，最后都会收口成：

```ts
return runEmbeddedPiAgent({
  sessionId,
  sessionKey,
  agentId,
  sessionFile,
  workspaceDir,
  config,
  skillsSnapshot,
  prompt: effectivePrompt,
  provider,
  model,
  extraSystemPrompt,
  ...
})
```

也就是说，command path 里如果有需要真正调用 agent 的场景，并不是另起一套 execution engine，而是：

> **命令层做判断，执行层仍然复用同一个 embedded runner。**

这就是为什么 OpenClaw 的 commands mode 和 embedded mode 看起来分叉，但底层很多基础设施其实是共用的。

### 8. 两条路径共享的底层设施，比看起来多得多

从这轮代码看，commands mode 和 embedded run mode 共享至少这些东西：

- sessionKey / sessionEntry
- session store / transcript
- skillsSnapshot
- extraSystemPrompt / system prompt bundle
- auth profile 解析
- routing / message channel / thread context
- media note / body normalization
- send policy
- reset hooks / internal hooks

一句话判断：

> **它们不是两套系统，而是同一条主循环上的两个分叉层级。**

## 设计判断

### 1. Commands mode 本质上是控制面，不是轻量 agent mode

这轮我最核心的判断就是这个。

很多系统会把 slash commands 理解成：

- prompt 模板切换
- 特殊 system prompt
- 另一种聊天模式

OpenClaw 更不是这样。

它的 commands mode 本质上是：

- reset session
- route to status/info/help/context handlers
- 触发 hooks
- 操作 binding / ACP thread target
- 决定这轮是否进入真正 agent run

所以它是控制面，不是另一个低配 agent。

### 2. Embedded run mode 才是长期个人助理的真正执行引擎

如果你问“OpenClaw 长期助理的核心执行力在哪”，答案不在 commands path，而在：

- `runReplyAgent`
- `agent-runner-execution`
- `runEmbeddedPiAgent`

这里才有：

- 长 session
- compaction
- fallback
- transcript
- tool calls
- delivery pipeline
- runtime continuity

### 3. 两条路径之所以能共存，关键在于前面有统一的 run preparation layer

`getReplyRun()` 非常关键。它把很多平台级上下文都先准备好了：

- session
- skillsSnapshot
- system events
- thread context
- extra system prompt
- media context
- queue mode
- auth profile

这意味着 commands / embedded 不是各自重复准备上下文，而是：

> **共享一层 run preparation，再在后面分叉。**

这会让系统边界清楚很多。

### 4. 这也是 OpenClaw 能同时像“命令型工具”和“长期助理”的原因

如果没有这套分层，OpenClaw 很容易走向两个极端之一：

- 要么所有命令都变成 prompt 内语义，不可控
- 要么命令和 agent execution 彻底分裂成两套系统，难维护

现在这套结构比较健康：

- command path 负责控制和拦截
- embedded path 负责推理和执行
- 两者共享 session/runtime 基础设施

### 5. trade-off：双层路径会提高主循环理解成本

这套设计是对的，但也有代价。

代价就是：

- 新人更难一眼看懂“消息到底走哪”
- 某些问题要先判断是 command bug 还是 embedded bug
- reset / ACP / send-policy 这类控制行为会让入口逻辑变厚

也就是说，OpenClaw 用更清晰的平台边界，换来了更高的主循环认知成本。

但我觉得这个 trade-off 是值得的。

## 关键术语解释

- **commands mode**：OpenClaw 在真正 agent execution 前的控制面路径，负责处理 slash commands、reset/status/help/context 等指令，以及相关 runtime 副作用。
- **embedded run mode**：OpenClaw 真正执行长期 agent 推理、工具调用、compaction、fallback 和 delivery 的主执行路径。
- **control plane**：系统里负责配置、控制、重置、路由和状态操作的一层，不直接承担主要业务执行。
- **execution plane**：真正执行核心业务逻辑的一层，这里主要指 `runEmbeddedPiAgent()` 所代表的 agent run 执行面。
- **followupRun**：在 reply 主循环中构造出的统一运行描述对象，后续 commands path 和 embedded path 都会围绕它做判断或执行。
- **shouldContinue**：commands path 返回给主循环的放行信号，决定是否继续进入 embedded run。
- **commandBodyNormalized**：经过 mention stripping 和命令规范化后的命令文本，用于稳定匹配命令处理器。
- **run preparation layer**：在真正分叉前统一准备 session、skills、prompt、events、queue 等运行上下文的那一层。

## 本轮遗留问题

1. 如果继续往下读，很值得单独做一篇：`getReplyRun()` 作为主循环装配层到底有多重、哪些逻辑应该继续上浮/下沉。
2. command handlers runtime 里各个 handler 的职责边界，还可以专门读一篇“commands control plane anatomy”。
3. CLI provider path 和 embedded provider path 的差异，目前只触到了入口，还没展开对比。
4. 如果之后要从 OpenClaw 抽一个更轻的个人助理 runtime，commands mode 和 embedded mode 哪些层应该保留、哪些可以砍掉，是个很值得继续思考的问题。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/I359dbOgCo4cVvxPHM3cu9PBnFc`)
- Vault git sync: in progress
