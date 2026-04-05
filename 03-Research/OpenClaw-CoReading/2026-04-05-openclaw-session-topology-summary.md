---
title: OpenClaw 共读：session topology 总结篇
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - session
  - topology
status: active
---

# OpenClaw 共读：session topology 总结篇

## 一句话结论

OpenClaw 的 session 不是“一个聊天窗口对应一条历史”这么简单，而是一张 **会话拓扑图**：`main session` 是主干，`subagent session` 是挂在主干下的子节点，`heartbeat session` 是主干的低频守望分支，`cron isolated session` 是按计划临时起的新执行节点。真正让它像长期个人助理 runtime 的，不是某一条会话很强，而是 **这些不同类型的 session 被放进同一套命名、持久化、路由、运行、交付体系里。**

## 这部分在系统里负责什么

这一篇不是在看某一个模块，而是在收束前面几篇已经出现过的 session 形态。

它要回答的是：

- OpenClaw 到底有哪几类 session
- 它们分别解决什么问题
- 它们在命名、父子关系、持久化和 delivery 上怎么区分
- 为什么系统既能有长期主会话，又能有 subagent、heartbeat、cron 这些衍生运行形态

如果前面几篇主要是在看“局部机制”，这一篇就是把这些局部机制放回：

> **OpenClaw 的整体会话拓扑。**

## 主链路

我这轮抓的主链路是这几段：

1. **主 session key 的 canonical 规则**  
   `src/config/sessions/session-key.ts` + `src/config/sessions/main-session.ts`
2. **session entry 作为统一 runtime 容器**  
   `src/config/sessions/types.ts`
3. **subagent session 通过 `parentSessionKey` / `spawnDepth` / `spawnedBy` 挂到主拓扑上**  
   `src/config/sessions/types.ts` + `src/agents/subagent-registry.types.ts`
4. **heartbeat 默认绑定主 session，可选派生 `:heartbeat` session**  
   `src/infra/heartbeat-runner.ts`
5. **cron isolated session 通过独立 session key / sessionId / reset policy 形成计划性执行节点**  
   `src/cron/isolated-agent/session.ts` + `src/cron/isolated-agent/session-key.ts`

一句话串起来：

> **OpenClaw 用统一的 session key / session entry 体系承载多种运行形态，再通过父子字段、后缀约定和 sessionTarget 策略把它们组织成一张可治理的会话图。**

## 关键文件与代码片段

### 1. `session-key.ts`：拓扑最上游先决定“主干会话”长什么样

这段还是最关键的入口：

```ts
export function resolveSessionKey(scope: SessionScope, ctx: MsgContext, mainKey?: string) {
  ...
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

这意味着：

- **直聊默认收敛到主 session**
- **群/channel 则保留独立 bucket**

也就是说，OpenClaw 的会话拓扑不是天然分散的，它一开始就有主干收敛规则。

我的判断：

> **main session 不是“后来演化出来的默认会话”，而是系统从入口处就主动塑形的主干节点。**

### 2. `main-session.ts`：main session 既是逻辑锚点，也是拓扑锚点

`resolveMainSessionKey()` 和 `canonicalizeMainSessionAlias()` 这两组函数的价值，在拓扑视角下更清楚：

- `main` 不是随便写个字符串
- 配置了 `mainKey` 后，要 canonicalize 到正式主键
- 历史 alias / legacy key 也要尽量折回同一根主干

一句话判断：

> **main session 是整张拓扑图的 canonical root。**

没有这个 root，后面 heartbeat、cron、subagent 很多“默认落点”都会漂。

### 3. `SessionEntry`：所有 session 类型都落在同一个统一容器里

`SessionEntry` 这一点我越来越认同。它不是只服务主会话，而是让所有 session 类型都能共存于一个统一容器：

- 通用字段：`sessionId`、`updatedAt`、`sessionFile`
- 主会话关心的：delivery / queue / usage / context
- subagent 关心的：`spawnedBy`、`parentSessionKey`、`spawnDepth`、`subagentRole`
- heartbeat 关心的：`lastHeartbeatText`、`heartbeatTaskState`
- ACP / model / auth 这些跨类型字段也都能落在 entry 里

这说明 OpenClaw 不是为每类会话单独造数据库表或完全独立结构，而是：

> **先统一 session 容器，再在容器上挂类型差异。**

### 4. subagent session：它们是树状子节点，不是平级分身

`SessionEntry` 和 `SubagentRunRecord` 结合起来看，subagent 的拓扑位置很明确：

- `parentSessionKey`
- `spawnedBy`
- `spawnDepth`
- `childSessionKey`
- `requesterSessionKey`
- `controllerSessionKey`

这说明 subagent 会话不是“又一个 main session”，也不是另一个独立人格空间，而是：

> **被正式挂到父会话下面的树状节点。**

这点很关键。因为 OpenClaw 的多 agent 不是“多个平权人格”，而是 **session tree**。

### 5. heartbeat session：它默认依附主干，但可以派生出专用守望分支

heartbeat 的默认行为是基于主 session 跑，因为它要继承：

- 现有 delivery context
- session 绑定
- heartbeat task state
- 最近路由/会话连续性

但当 `isolatedSession === true` 时，它会这样做：

```ts
const isolatedKey = `${sessionKey}:heartbeat`;
const cronSession = resolveCronSession({ ..., sessionKey: isolatedKey, forceNew: true });
```

这意味着 heartbeat 在拓扑里的位置很特别：

- 默认是主干上的低频守望动作
- 必要时可以生出一个专用 `:heartbeat` 分支

我的判断：

> **heartbeat session 不是另一类平级主会话，而是主干会话的“守望型旁支”。**

### 6. cron isolated session：它更像“计划性临时节点”，不是会话内续跑

cron 这块最关键的是 `resolveCronSession()` 和 `resolveCronAgentSessionKey()`。

cron isolated session 的特点是：

- 按 `sessionTarget` 决定挂 main / current / isolated / custom
- isolated 时可新建 `sessionId`
- 新 session 要清空旧 delivery routing 污染
- 可以复用 existing fresh session，也可以强制新建

这说明 cron isolated session 在拓扑里的位置更像：

> **从主系统里按计划生成的独立执行节点。**

它不一定有 subagent 的父子树关系，也不一定要像 followup 那样沿原会话继续。

它的重点是：

- 计划性
- 独立执行
- 可显式交付

### 7. 一个简化拓扑图

如果把这几类 session 摊平，我会这样画：

```text
agent:main:<mainKey>                ← 主干 root / main session
├── agent:main:<group/channel...>   ← 群组/频道隔离会话（同 agent 下的并列主会话面）
├── agent:main:<mainKey>:heartbeat  ← 主会话派生出的守望分支（可选）
├── agent:main:<cron-key...>        ← cron current/main/custom target（取决于 sessionTarget）
└── child sessions
    ├── agent:<child-agent>:...     ← subagent session
    └── descendants...              ← 更深层子会话
```

注意：

- **main session 是 root**
- **group/channel session 是同 agent 下的并列会话面**
- **heartbeat 是主干派生分支**
- **cron isolated 是计划执行节点**
- **subagent 是树状子节点**

这几个不能混成一类。

## 设计判断

### 1. OpenClaw 的 session topology 不是扁平列表，而是“主干 + 并列会话面 + 树状子节点 + 派生分支”

这是我这轮最核心的判断。

如果把所有 session 都看成平级列表，会误解很多设计：

- 为什么 main key 要 canonicalize
- 为什么 subagent 要有 parentSessionKey / spawnDepth
- 为什么 heartbeat 要用 `:heartbeat`
- 为什么 cron 要有 sessionTarget

它们其实是在组织不同类型的节点，而不是仅仅保存更多会话。

### 2. main session 是根节点，不只是默认值

这个判断很重要。

很多系统的“main session”只是个方便的名字。OpenClaw 不是。

在它这里，main session 更像：

- 默认直聊收敛点
- heartbeat 默认落点
- 许多 session alias 的 canonical root
- delivery continuity 的稳定锚点

也就是说：

> **main session 是拓扑根，不只是默认 bucket。**

### 3. subagent / heartbeat / cron 三者虽然都不是 main，但拓扑角色完全不同

- **subagent**：树状子节点
- **heartbeat**：主干派生守望分支
- **cron isolated**：计划性执行节点

这三者名字看起来都像“额外 session”，但语义完全不同。

这也是为什么 OpenClaw 能同时支持多种长期运行形态，而不必把一切后台行为都塞进一个大黑盒里。

### 4. 统一 SessionEntry 是对的，但也意味着类型语义必须靠 discipline 保住

统一容器的好处是：

- 兼容
- 复用 store / transcript / delivery / usage
- 减少平行模型

代价也很明显：

- 不同 session 类型共用一个结构后，语义边界只能靠字段 discipline 维持
- 读代码时不容易一眼知道某字段主要服务哪类 session

也就是说，OpenClaw 用统一性换来了一部分认知成本。

### 5. 从长期个人助理角度，session topology 是比“单次推理能力”更根的资产

一个长期助理系统最终拼的，不只是模型有多强，而是：

- 会话怎么存活
- 不同主动/被动运行形态怎么挂接
- 结果怎么回正确位置
- 多 agent / 多 channel 如何不串台

这些问题本质上都落在 session topology 上。

所以从主线角度看，这篇总结是值得的。它把前面很多零散阅读真正收成了一张图。

## 关键术语解释

- **session topology**：系统中不同类型 session 之间的组织关系，包括主干、并列会话面、树状子节点和派生分支。
- **main session**：OpenClaw 的主干会话根节点，许多默认行为和 canonical 规则都围绕它展开。
- **canonical main key**：主 session 的标准化命名形式，用来消除 alias 和 legacy key 漂移。
- **subagent session**：由父会话创建并挂到其下方的子会话节点。
- **heartbeat session**：heartbeat 运行时可选使用的派生会话分支，常以 `:heartbeat` 后缀存在。
- **cron isolated session**：cron 按计划执行时创建或复用的独立会话节点。
- **sessionTarget**：cron 任务指定应落到哪类 session 节点上的策略。
- **session continuity**：同一条会话线在多轮运行中保持上下文、路由和交付连续性的能力。

## 本轮遗留问题

1. 这篇已经把拓扑图收起来了，下一篇很适合转去 `tool execution model`，看这些不同 session 节点到底如何驱动 exec / process / sessions_spawn / cron 等真实动作。
2. 如果后面想从 OpenClaw 抽一个更轻的长期助理 runtime，这张拓扑图很适合作为“哪些节点必须保留”的起点。
3. 还可以专门做一篇 session 命名规则总结，把 `main`、`group/channel`、`subagent`、`heartbeat`、`cron` 的 key 语法全部摊平。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/IcJ4decEiooYBZxR8MpcbcoSnJe`)
- Vault git sync: in progress
