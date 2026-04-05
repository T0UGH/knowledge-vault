---
title: OpenClaw 共读：gateway routing
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - gateway
  - routing
status: active
---

# OpenClaw 共读：gateway routing

## 一句话结论

OpenClaw 的 gateway routing 不是“收到消息就找个 agent 回一下”这么简单。它本质上是在维护一条完整的 **路由连续性链条**：**inbound route → session key → delivery context → bound conversation → outbound delivery**。只有这条链不断，系统才能保证“谁收到的消息，最终就由谁收到结果”。

## 这部分在系统里负责什么

这一层解决的是平台里最容易出事故的一类问题：

- 一条消息进来，应该路由给哪个 agent
- 这轮会话应该写到哪个 session key
- 后续回复到底该发回哪个 channel / account / thread
- thread / topic / bound conversation 存在时，结果应该回当前会话还是父会话
- subagent / task completion / heartbeat 这种非直接对话回复，应该找谁做最终投递

如果这层做薄了，系统表面上“跑起来”没问题，但很快会出现：

- agent 回到了错误的群
- thread 回复丢到主频道
- 子任务结果找不到正确会话
- session 还在，delivery target 却漂了

所以 gateway routing 的职责不是“找路”，而是：

> **维护运行时的消息归属连续性。**

## 主链路

我这轮抓的主链路是这几段：

1. **inbound 先按 channel/account/peer 解析 agent route**  
   `src/routing/resolve-route.ts`
2. **route 决定 sessionKey / mainSessionKey / lastRoutePolicy**  
   `src/routing/resolve-route.ts`
3. **session 里保存 delivery context，作为后续回复基线**  
   `src/utils/delivery-context.ts`
4. **带 thread 的 sessionKey 可以反推出 delivery info**  
   `src/config/sessions/delivery-info.ts`
5. **bound conversation 独立维护“会话绑定”**  
   `src/infra/outbound/session-binding-service.ts`
6. **bound router 在 task/subagent completion 时优先找绑定目标**  
   `src/infra/outbound/bound-delivery-router.ts`
7. **最终 outbound delivery 统一走发送队列和镜像回写**  
   `src/infra/outbound/deliver.ts`
8. **subagent announce completion 会先 resolve completion origin，再决定最终投递目的地**  
   `src/agents/subagent-announce-delivery.ts`

一句话串起来：

> **OpenClaw 先决定“这条消息属于谁”，再把“以后该回给谁”存进 session 和 binding，最后所有异步结果都沿这条路由连续性回到正确会话。**

## 关键文件与代码片段

### 1. `resolve-route.ts`：inbound route 不是拍脑袋选 agent

`resolveAgentRoute()` 一上来就把输入正规化：

- `channel`
- `accountId`
- `peer`
- `guildId`
- `teamId`
- `memberRoleIds`
- `dmScope`
- `parentPeer`

然后再用分层匹配策略找 route。最关键的是这一段 tier 顺序：

- `binding.peer`
- `binding.peer.parent`
- `binding.peer.wildcard`
- `binding.guild+roles`
- `binding.guild`
- `binding.team`
- `binding.account`
- `binding.channel`
- 最后才 `default`

我的判断：

这不是普通 if-else，而是在明确表达一个优先级原则：

> **越具体的上下文绑定，越优先；越通用的默认配置，越靠后。**

这点很重要。平台只要把 route 选错一次，后面整条 session / delivery 链都会偏。

### 2. `resolve-route.ts`：route 产物里已经带了 runtime 意图

`choose()` 最后产出的 route，不只是 `agentId`，还包括：

- `sessionKey`
- `mainSessionKey`
- `lastRoutePolicy`
- `matchedBy`

尤其是：

```ts
const sessionKey = buildAgentSessionKey(...)
const mainSessionKey = buildAgentMainSessionKey(...)
const route = {
  agentId,
  sessionKey,
  mainSessionKey,
  lastRoutePolicy: deriveLastRoutePolicy({ sessionKey, mainSessionKey }),
  matchedBy,
}
```

这说明 route 不是“选完 agent 就结束”。

在 OpenClaw 里，route 的真正产物是：

> **这个 inbound 事件要接到哪条 session 线上，以及后续 last-route 应该按 main 还是按 session 继承。**

所以 routing 直接影响 session continuity。

### 3. `delivery-context.ts`：session 里的 delivery context 才是后续回复的基线

这里最值得看的不是字段，而是合并逻辑：

```ts
const merged = mergeDeliveryContext(
  normalizeDeliveryContext({
    channel: source.lastChannel ?? source.channel,
    to: source.lastTo,
    accountId: source.lastAccountId,
    threadId: source.lastThreadId,
  }),
  normalizeDeliveryContext(source.deliveryContext),
)
```

以及：

```ts
export function deliveryContextFromSession(entry?) {
  ...
  return normalizeSessionDeliveryFields(source).deliveryContext;
}
```

这说明 OpenClaw 不把 delivery target 看成一次性参数，而是把它当成 session 的一部分。

更关键的是 `mergeDeliveryContext()` 这段保护：

```ts
const channelsConflict =
  normalizedPrimary?.channel &&
  normalizedFallback?.channel &&
  normalizedPrimary.channel !== normalizedFallback.channel;
```

然后如果 channel 冲突，就不交叉混用 `to/accountId/threadId`。

这个细节非常对。因为最危险的 bug 不是“没有目标”，而是：

- 用 Slack 的 channel
- 拼上 Discord 的 thread
- 再带着另一个 accountId 发出去

那就彻底乱了。

OpenClaw 在这里明显是在做 **route field pairing**，保证 delivery context 的字段不会跨 channel 乱串。

### 4. `delivery-info.ts`：thread-aware session 不是只看 sessionKey 字符串

这文件做的事不花哨，但很关键：

```ts
export function extractDeliveryInfo(sessionKey) {
  const { baseSessionKey, threadId } = parseSessionThreadInfo(sessionKey);
  ...
  let entry = store[sessionKey];
  if (!entry?.deliveryContext && baseSessionKey !== sessionKey) {
    entry = store[baseSessionKey];
  }
  ...
}
```

这里透露出一个很重要的运行时态度：

> **thread/session 语法只是入口线索，真正的 delivery 真相还是 session store 里的 deliveryContext。**

也就是说，OpenClaw 不会单靠字符串后缀来赌路由，而是尽量回到 store 里找正规记录。

### 5. `session-binding-service.ts`：bound conversation 是单独维护的，不硬塞进 session 本体

这个 service 值得你特别注意，因为它和 session 是并行层，不是附属字段。

它维护的是：

- `bindingId`
- `targetSessionKey`
- `targetKind` (`subagent` / `session`)
- `conversation`
- `status`
- `boundAt`
- `expiresAt`
- `metadata`

一句话判断：

> **session 描述“这条会话线是谁”，binding 描述“当前哪个真实会话面板绑定到这条线”。**

这是个很成熟的拆分。

因为 thread/topic/current conversation 这种东西，本来就比 session 更短命、更易变。如果全塞进 session entry，本体会很快被交互层污染。

### 6. `bound-delivery-router.ts`：异步结果优先走绑定，不走猜测

这文件的逻辑很干净。

它的输入是：

- `targetSessionKey`
- `requester`
- `failClosed`

然后判断：

- 有没有 active binding
- requester 能不能精确匹配 binding
- 单绑定时能不能 fallback

如果命中：
- `mode = "bound"`

否则：
- `mode = "fallback"`

这里最关键的不是实现细节，而是设计立场：

> **异步 completion 的目标解析优先依赖 binding 事实，而不是临时猜当前 conversation。**

这就是为什么 task completion / subagent completion 可以更稳地回到正确 thread。

### 7. `deliver.ts`：最终发送不是直接调插件，而是先入 delivery queue

`deliverOutboundPayloads()` 一上来先做的是：

```ts
const queueId = params.skipQueue ? null : await enqueueDelivery(...)
```

成功后再 `ackDelivery()`，失败则 `failDelivery()`。

这层很像 write-ahead delivery queue。也就是说：

- 先把要发什么记下来
- 再真正去发
- 成功就确认
- 失败就记录错误

这说明 OpenClaw 的 outbound delivery 也不是“函数调用就结束”，而是有自己的可靠性层。

这和 routing 连起来看就更清楚了：

> **routing 决定该发给谁，delivery queue 决定这次发送不能悄悄丢。**

### 8. `deliver.ts`：mirror sessionKey 让 delivery 结果重新回挂 session

`deliverOutboundPayloadsCore()` 里有一段很关键：

```ts
const sessionKeyForInternalHooks = params.mirror?.sessionKey ?? params.session?.key;
```

再往后，完成 delivery 后会走 transcript mirror 回写。

这说明 outbound 不只是发出去，而是仍然要把“这次发出去的结果”回挂到对应 session 线上。

换句话说，路由链的最后一步不是外发，而是：

> **外发完成后，系统内部也要知道这次结果属于哪条 session。**

### 9. `subagent-announce-delivery.ts`：completion origin 的解析把 routing、binding、session 合到一起了

这文件特别能代表 OpenClaw 的 routing 思维。

看 `resolveSubagentCompletionOrigin()`：

1. 先拿 `requesterOrigin`
2. 再构造 requester conversation
3. 再交给 `createBoundDeliveryRouter().resolveDestination(...)`
4. 如果命中 bound binding，就生成 bound target
5. 如果没命中，再给 hook runner 一个机会
6. 再不行，回退到 requesterOrigin

也就是说，子任务完成不是简单地“发回 parent”。它真正做的是：

> **沿着 request origin → bound conversation → hook override → fallback 这条链，找当前最可信的投递目标。**

这就比“直接回 parent session”成熟很多。

## 设计判断

### 1. OpenClaw 的 routing 真正要保护的是“消息归属连续性”

这轮最核心的判断就是这个。

如果把 routing 理解成“入站找 agent，出站找 channel”，会低估它。

实际上它在保护的是：

- 谁接了这条消息
- 这条消息后来变成了哪条 session
- 这条 session 未来该回给谁
- 异步结果出来后，是否还能找到原来的 conversation

这是一条连续性问题，不是单次解析问题。

### 2. session 与 binding 分层是对的

这也是这轮我最认同的设计之一。

- **session**：长期运行会话线
- **binding**：当前 UI / thread / conversation 与该会话线的临时绑定关系

这能避免 session 被界面层细节污染，也让 thread / topic 生命周期变化更好处理。

### 3. delivery context 是 session runtime 的一部分，不是发送时临时补参

很多系统的做法是：发送时再临时推断 channel/to/thread。

OpenClaw 不是。它把 delivery context 放进 session 里，作为后续 outbound 和 completion 回流的稳定基线。

这个决定很关键，因为它直接减少了“后面猜路”的空间。

### 4. bound delivery router 是异步任务结果回流的关键稳定器

如果没有 bound router，task / subagent / heartbeat 这种异步结果很容易掉进默认 channel 或旧 session。

有了 bound router，平台可以更有把握地回答：

> **这个完成结果应该回当前 thread，还是回父会话，还是按 fallback 退回原始 requester。**

### 5. OpenClaw 的 routing 设计很像“状态连续性优先”的系统，而不是“即时发送优先”的系统

它宁愿：

- 多存一层 deliveryContext
- 多做一层 binding service
- 多走一层 bound router
- 多维护一层 delivery queue

也不愿意把路由问题留给发送时临场猜测。

这是一个很成熟的平台选择。

## 关键术语解释

- **gateway routing**：OpenClaw 中把 inbound 事件解析到正确 agent/session，并把 outbound 结果送回正确会话面的整体机制。
- **resolved route**：根据 channel、account、peer、guild、team 等条件解析出的正式路由结果。
- **lastRoutePolicy**：决定后续 last-route 更新应该绑定到 main session 还是具体 session 的策略。
- **delivery context**：回复目标的结构化上下文，包含 channel、to、accountId、threadId。
- **binding**：某个当前 conversation/thread 与某条 session 的绑定关系。
- **bound delivery**：优先按绑定关系把异步结果送回当前会话面的投递模式。
- **fallback routing**：绑定不明确或失效时，退回到 requester origin 或默认路由的降级路径。
- **delivery queue**：发送前先落盘、发送后再确认的可靠性队列。
- **transcript mirror**：外发完成后再把结果镜像写回 session transcript，保持内部记录与真实交付一致。

## 本轮遗留问题

1. `resolve-route.ts` 里 `buildAgentSessionKey()` 的具体语法，以及 identityLinks / dmScope 对 session continuity 的影响，还值得单独深挖。
2. thread binding 在 Discord / Telegram 等具体 channel adapter 里是怎么落地的，这轮还没展开。
3. outbound target resolution 和 plugin hook 之间的边界，还可以专门做一篇“发送控制面”共读。
4. ACP persistent bindings 和普通 session binding 的关系，后面很值得做一次对照。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/IkwtdL5HWobvcpxePuEc9gNznCg`)
- Vault git sync: done (`2be0e56 add OpenClaw co-reading note: gateway-routing`)
