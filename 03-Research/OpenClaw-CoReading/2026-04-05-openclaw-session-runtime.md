---
title: OpenClaw 共读：session runtime
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - runtime
  - session
status: active
---

# OpenClaw 共读：session runtime

## 一句话结论

OpenClaw 的 session runtime 不是“聊天记录容器”，而是 **平台级运行单元**：它同时绑定了 `sessionKey`、持久化 `sessionId`、转录文件、运行中状态、系统事件队列，以及最终对外回复的 delivery 上下文。

## 这部分在系统里负责什么

如果只看手感，OpenClaw 像是在“收消息 → 跑 agent → 回消息”。

但源码里真正把系统稳住的，是 session 这一层。它把几件本来很容易散掉的东西绑在了一起：

- **路由身份**：这条消息应该落到哪个 session bucket
- **持久化身份**：这个 bucket 当前对应哪个 `sessionId`
- **转录文件**：这轮运行和后续回复写到哪份 transcript
- **运行时状态**：这个 session 当前是否有 embedded run 正在跑
- **系统事件**：重启、heartbeat、exec 完成等内部事件怎么注入下一轮 prompt
- **对外回复上下文**：最终消息发回哪个 channel / thread / account

所以 OpenClaw 的 session 不是附属物，而是运行时的骨架层。

## 主链路

我这轮先抓的主链路是这几段：

1. **入口先决定 sessionKey**  
   `src/config/sessions/session-key.ts`
2. **主会话 key 再 canonicalize 成平台认可的主 session**  
   `src/config/sessions/main-session.ts`
3. **session store 把 sessionKey 映射到持久化 entry**  
   `src/config/sessions/store.ts`
4. **转录文件基于 entry.sessionId 落到真实 transcript 文件**  
   `src/config/sessions/transcript.ts`
5. **embedded run 的活跃状态单独挂在全局 runtime state 上**  
   `src/agents/pi-embedded-runner/runs.ts`
6. **系统事件按 sessionKey 入队，下一轮再 drain 到 prompt**  
   `src/infra/system-events.ts`
7. **最终 outbound delivery 成功后，再镜像写回 session transcript**  
   `src/infra/outbound/deliver.ts`

一句话串起来就是：

> **sessionKey 先定义“这是哪条会话线”，store 再把它绑定到 sessionId 和元数据，embedded runner 在运行时围绕 sessionId 管理活跃状态，系统事件和对外 delivery 最后再回挂到这条会话线上。**

## 关键文件与代码片段

### 1. `session-key.ts`：先把外部消息归到正确 bucket

最关键的是这段：

```ts
export function resolveSessionKey(scope: SessionScope, ctx: MsgContext, mainKey?: string) {
  const explicit = ctx.SessionKey?.trim();
  if (explicit) {
    return normalizeExplicitSessionKey(explicit, ctx);
  }
  const raw = deriveSessionKey(scope, ctx);
  if (scope === "global") {
    return raw;
  }
  const canonicalMainKey = normalizeMainKey(mainKey);
  const canonical = buildAgentMainSessionKey({
    agentId: DEFAULT_AGENT_ID,
    mainKey: canonicalMainKey,
  });
  const isGroup = raw.includes(":group:") || raw.includes(":channel:");
  if (!isGroup) {
    return canonical;
  }
  return `agent:${DEFAULT_AGENT_ID}:${raw}`;
}
```

我的理解：

- **直聊默认收敛到主 session**，而不是每个 sender 一个新 session
- **群聊 / channel** 则保留隔离性
- session runtime 从第一步就不是“自然长出来”的，而是**被明确 canonicalize 的**

这点很关键。OpenClaw 先决定的是 **会话拓扑**，不是先把消息存下来。

### 2. `main-session.ts`：主 session 是平台规则，不是随手字符串

这里有两层价值：

```ts
export function resolveMainSessionKey(...) {
  if (cfg?.session?.scope === "global") {
    return "global";
  }
  ...
  return buildMainSessionKey(defaultAgentId, cfg?.session?.mainKey);
}
```

以及：

```ts
export function canonicalizeMainSessionAlias(...) {
  ...
  const isMainAlias =
    raw === "main" ||
    raw === mainKey ||
    raw === agentMainSessionKey ||
    raw === agentMainAliasKey ||
    raw === legacyMainKey ||
    raw === legacyMainAliasKey;
  ...
}
```

这说明主 session 有两个要求：

- **能稳定解析出当前 agent 的 canonical main key**
- **还能兼容历史 alias 和遗留 key**

也就是说，OpenClaw 在 session 层是很在意**标识连续性**的。它不接受“看起来像 main 就差不多”这种松散做法。

### 3. `store.ts`：session store 是持久化真相层

这层最值得注意的不是 CRUD，而是它在做三件很 runtime 的事：

#### a) session key 归一化

```ts
export function normalizeStoreSessionKey(sessionKey: string): string {
  return sessionKey.trim().toLowerCase();
}
```

#### b) 兼容 legacy key 并选择最新 entry

```ts
export function resolveSessionStoreEntry(params: { store: Record<string, SessionEntry>; sessionKey: string; })
```

#### c) 会话写入带锁、带维护、带磁盘预算

`store.ts` 里挂着：

- session write lock
- pruning / capping
- file rotation
- disk budget
- ACP metadata preservation

这说明 session store 不是一层薄薄的 JSON 读写封装，而是 **runtime durability layer**。

换句话说，OpenClaw 真正在乎的是：

> 这条会话线如何在长时间运行、多次重启、模型切换、subagent/ACP 参与后，依然保持一致。

### 4. `types.ts`：SessionEntry 本身已经暴露了 runtime 的野心

`SessionEntry` 非常长，但长得有价值。它里面不是只有 transcript 信息，还包括：

- `sessionId`
- `spawnedBy` / `parentSessionKey`
- `spawnDepth`
- `status`
- `startedAt` / `endedAt` / `runtimeMs`
- `deliveryContext`
- `modelOverride` / `authProfileOverride`
- `inputTokens` / `outputTokens` / `estimatedCostUsd`
- `skillsSnapshot`
- `acp`

这说明 OpenClaw 的 session entry 承载的是：

> **一条会话线的完整 runtime 画像**

不是“谁说了什么”的轻量聊天 metadata。

### 5. `transcript.ts`：session transcript 不是聊天日志，而是持久化运行轨迹

这里最关键的逻辑是：

```ts
const entry = (store[normalizedKey] ?? store[sessionKey]) as SessionEntry | undefined;
if (!entry?.sessionId) {
  return { ok: false, reason: `unknown sessionKey: ${sessionKey}` };
}
```

以及：

```ts
const resolvedSessionFile = await resolveAndPersistSessionFile({
  sessionId: entry.sessionId,
  sessionKey,
  ...
});
```

这说明 transcript 的真实锚点不是 `sessionKey`，而是：

- `sessionKey` 负责找 bucket
- `sessionId` 负责找到实际 transcript 文件

这是一个很成熟的分层：

- **sessionKey = 路由身份 / 逻辑会话名**
- **sessionId = 持久化实例身份**

这样做的好处是，逻辑 key 可以 canonicalize / alias / 兼容历史，而底层 transcript 仍然能稳定落盘。

### 6. `runs.ts`：embedded run 活跃状态是 runtime 单例，不直接塞进 store

`src/agents/pi-embedded-runner/runs.ts` 很关键。它把当前活跃 run 放在一个全局 singleton 里：

```ts
const embeddedRunState = resolveGlobalSingleton(..., () => ({
  activeRuns: new Map<string, EmbeddedPiQueueHandle>(),
  snapshots: new Map<string, ActiveEmbeddedRunSnapshot>(),
  waiters: new Map<string, Set<EmbeddedRunWaiter>>(),
  modelSwitchRequests: new Map<string, EmbeddedRunModelSwitchRequest>(),
}));
```

这里的判断很重要：

- **持久化 store** 负责 session 的长期真相
- **embedded run singleton** 负责当前进程内的活跃运行状态

这两个层是分开的。

这也是为什么 OpenClaw 的 session runtime 手感像平台，而不是普通聊天脚本：

> 它明确区分了“持久化状态”和“进程内活跃状态”。

### 7. `system-events.ts`：系统事件按 sessionKey 入队，等待下一轮消费

这段非常能体现“session 是 prompt 注入边界”：

```ts
export function enqueueSystemEvent(text: string, options: SystemEventOptions) {
  const key = requireSessionKey(options?.sessionKey);
  const entry = getOrCreateSessionQueue(key);
  ...
  entry.queue.push({ text: cleaned, ... })
}
```

以及：

```ts
export function drainSystemEventEntries(sessionKey: string): SystemEvent[] {
  ...
  entry.queue.length = 0;
  entry.lastText = null;
  entry.lastContextKey = null;
  queues.delete(key);
}
```

也就是说，系统事件不是全局广播，也不是直接写死进 transcript，而是：

- **按 sessionKey 排队**
- **在下一轮 prompt 前 drain**
- **消费后清空**

这是典型的 **ephemeral runtime side-channel**。

### 8. `deliver.ts`：对外发完，才镜像回 transcript

这里最有意思的是最后这段：

```ts
if (params.mirror && results.length > 0) {
  const { appendAssistantMessageToSessionTranscript } = await loadTranscriptRuntime();
  await appendAssistantMessageToSessionTranscript({
    agentId: params.mirror.agentId,
    sessionKey: params.mirror.sessionKey,
    text: mirrorText,
    idempotencyKey: params.mirror.idempotencyKey,
  });
}
```

这说明 OpenClaw 的 transcript 并不总是“模型一吐字就直接写进去”。

在 outbound 这条链路上，更像是：

1. 先完成外部 delivery
2. 再以 mirror 的方式写回 session transcript

这是一种很平台化的思路：

> transcript 不只是生成记录，也是“最终交付结果的可追溯镜像”。

## 设计判断

### 1. OpenClaw 的 session 是平台对象，不是消息容器

这轮最核心的判断就是这个。

如果把 OpenClaw 的 session 理解成“聊天历史”，很多设计会看起来过重；但如果把它理解成：

> **平台托管的一条持续运行会话线**

那很多东西就合理了：

- 为什么有 canonical sessionKey
- 为什么要 session store lock
- 为什么 transcript 用 `sessionId` 落盘
- 为什么系统事件按 sessionKey 排队
- 为什么 active run 放 singleton 而不全写进 store

### 2. `sessionKey` 和 `sessionId` 的分层是对的

这是这轮源码里我最认同的设计之一。

- `sessionKey` 解决逻辑路由和会话命名
- `sessionId` 解决持久化实例身份

这比把两者混成一个字段成熟很多，也更适合长期兼容和迁移。

### 3. store + runtime singleton 的双层结构很像真正的 runtime

OpenClaw 没有偷懒把所有东西都塞进一个 JSON 文件里。

它实际上在做：

- **store**：长期真相
- **singleton runtime maps**：当前进程的活跃状态
- **transcript**：持久化轨迹
- **system events**：下一轮 prompt 的短期注入

这四层分得相当清楚。

### 4. session 是 OpenClaw 后续一切能力的挂点

后面不管是：

- subagent
- memory
- ACP
- heartbeat
- outbound routing
- dashboard / status / cost

基本都要回挂到 session 这条线。

所以先读 session runtime 是对的。后面读 subagent、gateway、memory，都会轻很多。

## 关键术语解释

- **sessionKey**：逻辑会话标识，用来路由消息、系统事件和 session store entry。
- **sessionId**：持久化会话实例标识，主要用于定位真实 transcript 文件。
- **canonicalize**：把多个别名、历史 key 或模糊输入收敛成平台认可的标准形式。
- **embedded run**：OpenClaw 进程内执行的一次 agent 运行，不是外部独立 worker。
- **singleton runtime state**：当前进程内共享的一份全局状态，用来跟踪活跃 run、等待器、快照等短期运行态信息。
- **delivery context**：消息最终发回哪个 channel / account / thread 的上下文信息。
- **transcript mirror**：外部 delivery 成功后，再把最终输出镜像写回 transcript，保证持久化记录与真实交付一致。
- **ephemeral system event**：只服务下一轮 prompt 的短期系统事件，消费后会被清空，不作为长期持久化真相。

## 本轮遗留问题

1. `runEmbeddedPiAgent` 的真正主入口和 prompt 组装链路，还需要单独下钻。
2. session store entry 是在哪些关键时机被创建、切换、reset、fork 的，这一轮还没完全展开。
3. subagent session 和主 session 在 store / transcript / announce 上的耦合点，要放到下一篇看。
4. memory/context 是在 session runtime 之上组装，还是更深地嵌在 embedded runner 内，这一轮还没回答。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/ENHJddxdLogdNKxQkKkc4p34nWb`)
- Vault git sync: done (`bcef799 add OpenClaw co-reading note: session-runtime`)
