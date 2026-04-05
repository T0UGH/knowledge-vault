---
title: OpenClaw 共读：subagent lifecycle
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - subagent
  - runtime
status: active
---

# OpenClaw 共读：subagent lifecycle

## 一句话结论

OpenClaw 的 subagent 不是普通工具调用，也不是“后台起个任务”这么简单。它本质上是 **平台托管的子会话**：有独立 `childSessionKey`、独立运行记录、独立完成态、独立 announce 流程，但它的生命周期始终挂在父会话下面，被父会话统一编排和收口。

## 这部分在系统里负责什么

这一层解决的核心问题是：

- 父 agent 怎么安全地创建子 agent
- 子 agent 怎么拥有独立上下文和运行轨迹
- 子 agent 完成后，结果怎么回到父 agent，而不是直接散到用户面前
- 多个子 agent 并发时，父 agent 怎么知道什么时候该继续、什么时候该收尾

如果没有这层，`sessions_spawn` 只会变成“帮我开个异步任务”。

但 OpenClaw 真正想做的是：

> **把 subagent 当作 session topology 里的节点，而不是工具返回值。**

## 主链路

我这轮抓的主链路是这几段：

1. **spawn 参数收口与子会话创建**  
   `src/agents/subagent-spawn.ts`
2. **子 run 注册到 registry**  
   `src/agents/subagent-registry.ts`
3. **子 run record 作为运行真相**  
   `src/agents/subagent-registry.types.ts`
4. **完成态和 ended hook 的一次性收口**  
   `src/agents/subagent-registry-completion.ts`
5. **announce 不是轮询，而是 push-based completion delivery**  
   `src/agents/subagent-announce.ts`
6. **运行期辅助能力通过 runtime bridge 暴露**  
   `src/agents/subagent-announce.runtime.ts`
7. **父会话对子会话的控制面是 `subagents` tool，而不是直接碰底层 registry**  
   `src/agents/tools/subagents-tool.ts`

一句话串起来：

> **spawn 负责生孩子，registry 负责记账，announce 负责报平安，父会话通过控制面做编排，整套东西合起来才叫 subagent lifecycle。**

## 关键文件与代码片段

### 1. `subagent-spawn.ts`：spawn 入口从一开始就不是“裸调用”

这文件一上来就把意图写得很明白。

首先，参数里就已经有很强的平台味：

```ts
export type SpawnSubagentParams = {
  task: string;
  label?: string;
  agentId?: string;
  model?: string;
  thinking?: string;
  runTimeoutSeconds?: number;
  thread?: boolean;
  mode?: SpawnSubagentMode;
  cleanup?: "delete" | "keep";
  sandbox?: SpawnSubagentSandboxMode;
  expectsCompletionMessage?: boolean;
  attachments?: ...
}
```

这不是“调用一个 helper 函数”的参数形状，而是一个完整子运行时的创建协议。

更关键的是这两段常量：

```ts
export const SUBAGENT_SPAWN_ACCEPTED_NOTE =
  "Auto-announce is push-based. After spawning children, do NOT call sessions_list, sessions_history, exec sleep, or any polling tool..."
```

```ts
export const SUBAGENT_SPAWN_SESSION_ACCEPTED_NOTE =
  "thread-bound session stays active after this task; continue in-thread for follow-ups.";
```

这里其实已经把 OpenClaw 的设计立场说透了：

- **子 agent 完成通知是 push-based**
- **父 agent 不应该靠 polling 盯状态**
- **session mode 和 run mode 是两种不同生命周期**

这就是为什么它的手感和 Claude Code 很不一样。它不是“你自己去查孩子死活”，而是平台承诺：**孩子完事了会回来报。**

### 2. `SessionEntry`：子会话从数据模型上就是一等公民

`src/config/sessions/types.ts` 里这些字段很关键：

- `spawnedBy`
- `spawnedWorkspaceDir`
- `parentSessionKey`
- `spawnDepth`
- `subagentRole`
- `subagentControlScope`
- `startedAt` / `endedAt` / `runtimeMs`
- `status`

这说明 OpenClaw 不是在 session 外面额外挂一层 subagent 元数据，而是直接把：

> **“这是被谁生出来的、在第几层、什么角色、什么控制范围、活了多久、最后怎么结束”**

写进 session 自身模型里。

也就是说，subagent 不是外挂对象，而是 session 体系内部的正式成员。

### 3. `subagent-registry.types.ts`：真正追踪生命周期的是 run record

`SubagentRunRecord` 是这轮最值得看的结构之一：

```ts
export type SubagentRunRecord = {
  runId: string;
  childSessionKey: string;
  controllerSessionKey?: string;
  requesterSessionKey: string;
  requesterOrigin?: DeliveryContext;
  task: string;
  cleanup: "delete" | "keep";
  model?: string;
  workspaceDir?: string;
  runTimeoutSeconds?: number;
  spawnMode?: SpawnSubagentMode;
  createdAt: number;
  startedAt?: number;
  sessionStartedAt?: number;
  accumulatedRuntimeMs?: number;
  endedAt?: number;
  outcome?: SubagentRunOutcome;
  suppressAnnounceReason?: "steer-restart" | "killed";
  expectsCompletionMessage?: boolean;
  endedReason?: SubagentLifecycleEndedReason;
  wakeOnDescendantSettle?: boolean;
  frozenResultText?: string | null;
  endedHookEmittedAt?: number;
}
```

我的判断：

- **session entry** 记录的是子会话长期身份
- **run record** 记录的是这次子运行的生命周期事实

这两个层次分得很清楚。

尤其是这些字段特别说明问题：

- `controllerSessionKey`：谁有权管这个 child
- `endedReason`：不是只有成功失败，还有 kill / timeout / completion 等正式结束语义
- `frozenResultText`：完成输出要被冻结，避免后续读到漂移结果
- `wakeOnDescendantSettle`：父子孙三层关系不是平的，子 agent 自己也可能要等它的孩子

这已经不是普通任务队列了，这是**会话树运行时**。

### 4. `subagent-registry.ts`：registry 不是列表页，而是生命周期大脑

这文件最值得看的，不是某个函数名，而是它到底揽了哪些职责：

- restore / persist runs
- reconcile orphaned runs
- lifecycle error grace period
- ended hook emission
- announce cleanup
- context engine `onSubagentEnded`

比如这段：

```ts
const LIFECYCLE_ERROR_RETRY_GRACE_MS = 15_000;
```

注释写得很明白：embedded run 在 provider/model retry 期间可能会冒出暂时性的 error 事件，所以不能一看到 error 就把子任务当场判死。

这点很工程化。

它说明 OpenClaw 不是把 subagent 当成理想条件下只会“成功 / 失败”二选一的对象，而是承认：

> **真实运行时会抖，会重试，会出现暂时性错误信号。**

registry 的职责就是把这些噪声吸掉，别让父会话太早收到错误结论。

### 5. `subagent-registry-completion.ts`：ended hook 只发一次

这里的 `emitSubagentEndedHookOnce` 很关键：

```ts
if (params.entry.endedHookEmittedAt) {
  return false;
}
if (params.inFlightRunIds.has(runId)) {
  return false;
}
```

然后成功后：

```ts
params.entry.endedHookEmittedAt = Date.now();
params.persist();
```

这个设计不花哨，但特别重要。

真实系统里最恶心的问题不是“没通知”，而是：

- 通知两次
- 父会话收两次完成消息
- UI 看到双份结果
- 后续 cleanup 和控制流都乱掉

OpenClaw 在这块的态度很明确：

> **completion event 必须是幂等收口的，不允许反复抖动。**

### 6. `subagent-announce.ts`：completion 回流不是轮询，是主动回推

这文件里最核心的不是某个 API，而是它整个 prompt 和控制假设。

先看系统提示：

```ts
"You are a **subagent** spawned by the parent orchestrator..."
```

再看规则：

- Stay focused
- Complete the task
- Trust push-based completion
- Do NOT busy-poll for status

还有这句非常关键：

```ts
"Your sub-agents will announce their results back to you automatically (not to the main agent)."
```

这句话几乎就是 OpenClaw subagent 设计的灵魂。

不是：
- 所有孩子都直接回 main
- 父 agent 自己轮询所有孩子
- 子结果只是函数返回值

而是：

> **结果沿着 session tree 往上回流。谁生的，先回谁。**

这才叫托管子会话。

### 7. `subagent-announce.runtime.ts`：announce 依赖运行期桥接，不直接硬耦合底层

这个 runtime bridge 暴露的东西很少，但都很关键：

```ts
export {
  isEmbeddedPiRunActive,
  queueEmbeddedPiMessage,
  waitForEmbeddedPiRunEnd,
} from "./pi-embedded.js";
```

这说明 announce 并不是完全盲发，它会关心：

- 子 run 还活不活着
- 还能不能往正在运行的 session 里塞消息
- 是否要等 embedded run 彻底结束

这进一步证明：subagent completion 在 OpenClaw 里不是“日志事件”，而是**和活跃运行态耦合的编排事件**。

### 8. `subagents-tool.ts`：父会话看见的是控制面，不是内部实现

父会话操作子任务靠的是：

- `list`
- `kill`
- `steer`

不是让 agent 自己乱读 registry。

这个分层很对。agent 看到的是控制面：

- 当前有哪些 child
- 哪些还活着
- 我能 steer 谁
- 我能 kill 谁

而不是直接暴露运行期内部结构。

这让 subagent 在 agent 视角里更像：

> **受控的会话树节点**

而不是可以随便翻内脏的内部对象。

## 设计判断

### 1. OpenClaw subagent 真正特别的地方，是“子会话化”而不是“并发化”

很多系统也支持并发跑任务，但 OpenClaw 这套更进一步：

- child 有自己的 `sessionKey`
- child 有自己的运行记录
- child 有自己的控制面
- child 的结果按父子关系逐级回流

所以它最值钱的地方不是“能并发”，而是：

> **把 delegation 变成 session topology。**

### 2. push-based completion 比 polling 更像平台，而不是脚本

`SUBAGENT_SPAWN_ACCEPTED_NOTE` 这一整套约束，我非常认同。

让父 agent 轮询孩子状态，短期看好像更直观，长期看会带来：

- 无意义工具调用
- 状态竞争
- 双重完成通知
- prompt 噪音

而 push-based completion 的好处很明显：

- 谁该收到结果，平台已经知道
- 谁还没收尾，registry 已经知道
- 父 agent 更像 orchestrator，不像保姆

### 3. run record 和 session entry 分层是必要的

如果只有 child session，没有 run record，很多事情会说不清：

- 这次 run 到底因为什么结束
- 同一个 child session 的 follow-up run 怎么累计 runtime
- 哪次 announce 已经发过
- 哪个结果被冻结为最终交付结果

所以这里的分层不是“多一层抽象”，而是没这层就会乱。

### 4. OpenClaw 的 subagent 更接近“托管执行单元”，不是“自由人格分身”

虽然它们看起来像独立 agent，但从 lifecycle 设计看，本质上仍然是：

- 被父会话创建
- 被父会话约束
- 被平台 registry 管理
- 被平台 announce 流程回收

这和你现在在想的多 agent 分工很相关。

OpenClaw 的这套更偏：

> **主控 + 托管子执行单元**

而不是多个完全平级的长期人格。

## 关键术语解释

- **subagent lifecycle**：子 agent 从创建、运行、完成、回流、清理到归档的一整套生命周期管理。
- **childSessionKey**：子会话的逻辑标识，用来把它当成正式 session 节点处理。
- **requesterSessionKey**：发起这次子任务的父会话 key。
- **controllerSessionKey**：有权 list / steer / kill 这个子任务的控制会话 key。
- **run record**：一次具体子任务运行的事实记录，不等同于 child session 本身。
- **announce**：子任务完成后，把结果自动回推给父会话的机制。
- **push-based completion**：完成消息由平台主动回送，不靠父会话轮询查询。
- **frozenResultText**：为防止后续输出漂移而冻结的最终结果文本。
- **ended hook**：子任务结束时触发的生命周期 hook，用于清理、通知和插件联动。

## 本轮遗留问题

1. `subagent-spawn.ts` 里真正创建 child session、写 store、调 gateway 的细节，还可以再往里抠一轮。
2. `subagent-registry-run-manager.ts` 和 `subagent-registry-lifecycle.ts` 还没展开读，里面应该有更多“状态机味道”的实现。
3. subagent completion 最终怎么和 parent session transcript / system events 拼起来，值得下一轮补一刀。
4. ACP thread-bound session 和 OpenClaw subagent 在生命周期上哪里相像、哪里不同，后面很值得专门对照。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/T4dIdw5Elo8CPoxIKf7cs89OnV7`)
- Vault git sync: done (`778386a add OpenClaw co-reading note: subagent-lifecycle`)
