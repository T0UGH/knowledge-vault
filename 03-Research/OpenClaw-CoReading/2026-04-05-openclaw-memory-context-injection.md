---
title: OpenClaw 共读：memory / context injection
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - memory
  - context
status: active
---

# OpenClaw 共读：memory / context injection

## 一句话结论

OpenClaw 的 memory / context injection 不是单点功能，而是一条分层链路：**会话运行前先组 system prompt 和 bootstrap context，运行中通过 `memory_search` / `memory_get` 按需召回，长对话压力大时再走 memory flush / post-compaction context 做上下文重整。** 换句话说，它不是“先把记忆一股脑全塞进 prompt”，而是 **静态注入 + 工具召回 + 压缩后再补上下文** 的组合设计。

## 这部分在系统里负责什么

这一层要解决的不是一个问题，而是三类不同问题：

- **开局给模型什么背景**：workspace、docs、skills、project context、identity 这些静态上下文怎么进 system prompt
- **运行中怎么找记忆**：MEMORY.md、memory/*.md、以及可选 session memory 怎么被 semantic search
- **上下文快撑爆时怎么办**：长会话 compaction 之后，怎样把必要的后置上下文重新补回来，而不是让模型在压缩后失忆

所以 OpenClaw 的 memory/context 不是“有个 memory tool 就算完事”，而是：

> **把上下文拆成不同生命周期：开局注入的、按需召回的、压缩后补回的。**

## 主链路

我这轮抓的主链路是这几段：

1. **reply run 在真正起跑前准备 system prompt / skills / context files**  
   `src/auto-reply/reply/get-reply-run.ts`
2. **embedded run attempt 构建完整 system prompt，并生成 systemPromptReport**  
   `src/agents/pi-embedded-runner/run/attempt.ts`
3. **system prompt report 把 prompt 的构成拆开做可观察性记录**  
   `src/agents/system-prompt-report.ts`
4. **memory plugin state 决定是否注入 memory prompt section，以及 memory runtime 是否可用**  
   `src/plugins/memory-state.ts`
5. **memory runtime 再去拿 active memory search manager**  
   `src/plugins/memory-runtime.ts`
6. **memory backend config / store path / sources 在 agent scope 上解析**  
   `src/agents/memory-search.ts`
7. **长对话时，agent-runner-memory 负责 memory flush gating 和 post-compaction refresh**  
   `src/auto-reply/reply/agent-runner-memory.ts`
8. **/context 命令能把当前 system prompt 的组成反向展示出来**  
   `src/auto-reply/reply/commands-context-report.ts`

一句话串起来：

> **OpenClaw 先把“开局上下文”编成 system prompt，再把“可搜索记忆”挂成工具运行时能力，最后在 compaction 后补一层刷新上下文，确保长会话不会因为压缩彻底失忆。**

## 关键文件与代码片段

### 1. `get-reply-run.ts`：进入 agent run 之前，先做上下文装配

这文件最关键的不是某个 memory 函数，而是它把“上下文准备”放在真正执行前面。

能看到它在 run 前做了这些事：

- drain system events
- 追加 untrusted context
- 生成 thread history / starter context
- 确保 skills snapshot
- 拼 `extraSystemPrompt`
- 最后才交给 `runReplyAgent`

这里最关键的信号是：

```ts
const skillResult = await ensureSkillSnapshot(...)
...
const prefixedBody = [threadContextNote, prefixedBodyBase].filter(Boolean).join("\n\n")
...
const followupRun = {
  ...
  run: {
    ...
    skillsSnapshot,
    ...
    extraSystemPrompt: extraSystemPromptParts.join("\n\n") || undefined,
  },
}
```

我的理解：

OpenClaw 并不是把 memory/context 当作 reply 时临场附加的一点料，而是把它们视为：

> **run request 的正式组成部分。**

也就是说，上下文注入点比模型调用更靠前。

### 2. `run/attempt.ts`：system prompt 是被明确构建出来的，不是神秘黑盒

这段是这轮最重要的源码之一：

```ts
const appendPrompt = buildEmbeddedSystemPrompt({
  workspaceDir: effectiveWorkspace,
  ...
  skillsPrompt: effectiveSkillsPrompt,
  docsPath: docsPath ?? undefined,
  workspaceNotes,
  ...
  contextFiles,
  memoryCitationsMode: params.config?.memory?.citations,
})
```

然后紧跟着：

```ts
const systemPromptReport = buildSystemPromptReport({
  ...
  systemPrompt: appendPrompt,
  bootstrapFiles: hookAdjustedBootstrapFiles,
  injectedFiles: contextFiles,
  skillsPrompt,
  tools: effectiveTools,
})
```

这说明两件事：

#### a) 开局上下文主要是 **prompt composition**
也就是：
- workspace bootstrap files
- injected context files
- skills prompt
- tool list / tool schema
- docs 路径
- heartbeat 提示
- extra system prompt

#### b) OpenClaw 对 prompt 组成是 **可观测的**
它不是拼完就算了，而是还专门做 `systemPromptReport`。

这点很关键。因为真正复杂的 agent 系统，最容易失控的就是：

- prompt 到底有多大
- 哪部分最占 token
- skills 有多重
- project context 到底注了多少

OpenClaw 在这点上明显有工程自觉。

### 3. `system-prompt-report.ts`：它把“上下文是什么”变成结构化报告

`buildSystemPromptReport()` 会拆这些东西：

- `systemPrompt.chars`
- `projectContextChars`
- `nonProjectContextChars`
- `injectedWorkspaceFiles`
- `skills.promptChars`
- `tools.listChars`
- `tools.schemaChars`

一句话判断：

> **OpenClaw 不只是注入 context，它还在审计 context。**

这非常重要。

因为很多 agent 系统在 context 上的失败，不是“没注进去”，而是“注了太多，但没人知道谁在吃 token”。

### 4. `plugins/memory-state.ts`：memory 是插件态，不是硬编码死在主循环里

这个文件把 memory plugin state 明确拆成三块：

- `promptBuilder`
- `flushPlanResolver`
- `runtime`

对应能力分别是：

- memory prompt section 怎么进 prompt
- memory flush 策略怎么定
- memory runtime（搜索 manager）怎么取

这说明 OpenClaw 对 memory 的态度是：

> **memory 是平台能力，但通过插件注册接入，不是内核里写死的一坨逻辑。**

这点我很认同。这样后面不管换 backend、换 embedding provider、还是换 flush 策略，都不至于把主循环一起搅乱。

### 5. `plugins/memory-runtime.ts`：真正的 semantic recall 走 runtime manager，不是 prompt 文件扫描

这里的关键是：

```ts
return await runtime.getMemorySearchManager(params);
```

也就是说，`memory_search` 背后不是简单 grep，而是一个真正的 memory search manager。

并且 runtime 还会先：

- `applyPluginAutoEnable`
- `resolveRuntimePluginRegistry`
- 再 `getMemoryRuntime()`

这说明 semantic memory 本质上是一个 **runtime service**，而不是 prompt 里的静态内容。

### 6. `agents/memory-search.ts`：memory search 配置本身就是一套子系统

这个文件本身的长度已经说明问题了。它在解析：

- `sources`: `memory` / `sessions`
- `provider`
- `fallback`
- `model`
- `store.path`
- `vector enabled`
- `fts tokenizer`
- `chunking.tokens / overlap`
- `sync.onSessionStart / onSearch / watch`
- `query.maxResults / minScore / hybrid / mmr / temporalDecay`

一句话判断：

> **OpenClaw 的 memory search 不是小工具，而是一套可配置的 retrieval subsystem。**

这也解释了为什么 `memory_search` 适合做按需召回，而不是默认把所有记忆都塞进 prompt。

### 7. `agent-runner-memory.ts`：长会话下真正关键的是 memory flush，不是普通 recall

如果只看功能表，容易盯着 `memory_search`。

但从长期运行角度看，这文件同样关键。它处理的是：

- prompt tokens 估算
- transcript usage snapshot
- 是否触发 memory flush
- post-compaction context 重新注入

尤其这段很说明问题：

```ts
const refreshPrompt = await readPostCompactionContext(...)
...
params.followupRun.run.extraSystemPrompt = [existingPrompt, refreshPrompt]
  .filter(Boolean)
  .join("\n\n")
```

这说明 compaction 之后，OpenClaw 会主动补一层刷新 prompt。

这不是普通聊天机器人里很常见的设计。它说明 OpenClaw 承认：

> **长会话压缩之后，单靠 transcript 残片不够，需要有意识地把关键上下文重新挂回系统 prompt。**

### 8. `commands-context-report.ts`：/context 让 prompt composition 变成可调试对象

这文件是一个很好的观察窗。它最终会回显：

- system prompt 多大
- injected files 有哪些
- tools schema 多大
- skills prompt 多大
- sandbox 模式

我的判断：

> **OpenClaw 把“当前上下文到底由什么组成”做成了用户可见能力，而不只是内部调试能力。**

这很成熟，因为 context 失控本来就是 agent 平台里最难排的故障之一。

## 设计判断

### 1. OpenClaw 的 memory/context 设计是三层，不是一层

这轮我最核心的判断是：

#### 第一层：开局静态注入
- system prompt
- project context
- workspace files
- skills / tools / docs
- extra system prompt

#### 第二层：运行中按需召回
- `memory_search`
- `memory_get`
- 可选 session memory
- semantic retrieval backend

#### 第三层：长会话压缩后再补上下文
- memory flush
- post-compaction refresh
- transcript usage gating

这三层职责不一样，混在一起理解就会乱。

### 2. `memory_search` 不是“记忆本身”，而是记忆访问面

很多人会把 memory tool 和 memory 体系混为一谈。

OpenClaw 这里更清楚：

- memory runtime / backend / store 是底层系统
- `memory_search` / `memory_get` 是 agent 能触达它的工具界面

所以工具只是 access layer，不是 memory 本体。

### 3. system prompt report 很值钱，它把 context 变成了可治理对象

这一点我特别认可。

真正复杂的 agent 平台，最后都要面对一个现实：

- context 是有限预算
- 但想注的东西会越来越多
- 如果没有结构化报告，最后没人知道 prompt 到底被什么吃掉了

OpenClaw 至少在这个方向上是清醒的。

### 4. memory 被做成插件态，是对的

这让 OpenClaw 的主循环不用绑死某一种 memory backend，也让：

- recall 策略
- flush 策略
- backend 能力
- embedding provider

可以分开演进。

这比把 memory 逻辑硬塞进主 runner 成熟很多。

### 5. 真正长期可用的不是“更多记忆”，而是“上下文生命周期管理”

这轮看到最后，我觉得 OpenClaw 真正成熟的地方不只是有 memory_search，而是它已经开始管：

- 哪些上下文开局就该进 prompt
- 哪些要按需搜
- 哪些在 compaction 后必须补回

这比单纯“加个 memory tool”高一个层级。

## 关键术语解释

- **context injection**：把系统运行所需的背景信息注入 prompt 或运行时环境的过程。
- **bootstrap context files**：开局就注入 system prompt 的 workspace / project 上下文文件。
- **systemPromptReport**：对 system prompt 构成做结构化统计和审计的报告。
- **memory runtime**：memory backend 的运行时接入层，负责提供 search manager 等能力。
- **memory search manager**：真正执行 semantic search / sync / status 的后台管理对象。
- **memory_search / memory_get**：agent 侧访问记忆系统的工具界面，不等于底层 memory 本体。
- **memory flush**：在上下文接近极限时，把关键会话信息转写/整理到记忆层，减轻 prompt 压力。
- **post-compaction context**：会话压缩之后重新补回的一层关键上下文，防止模型在压缩后失忆。
- **skills snapshot**：本轮 run 看到的技能快照，避免每轮运行时技能上下文漂移不受控。

## 本轮遗留问题

1. `buildEmbeddedSystemPrompt()` 具体怎样组织项目上下文、skills、tools 和 docs，还值得单独做一次 prompt composition 共读。
2. memory-core / memory-lancedb 扩展到底怎样实现 runtime backend，这一轮只摸到了接入层，还没深挖插件实现。
3. session memory 与长期 MEMORY.md / memory/*.md 的协同策略，后面值得单独读一篇。
4. memory flush 产物最终写到哪里、如何去重、何时再次被召回，还可以继续顺着 `memory-flush.ts` 往里抠。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/KeZHdyx6RoGfEMx40Hmcf6XLnMg`)
- Vault git sync: done (`f745984 add OpenClaw co-reading note: memory-context-injection`)
