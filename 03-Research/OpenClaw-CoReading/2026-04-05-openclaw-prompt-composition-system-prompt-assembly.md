---
title: OpenClaw 共读：prompt composition / system prompt assembly
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - prompt
  - system-prompt
status: active
---

# OpenClaw 共读：prompt composition / system prompt assembly

## 一句话结论

OpenClaw 的 prompt composition 不是“拼一段 system prompt 字符串”这么简单，而是一个 **受控装配过程**：先收集 runtime、tools、skills、bootstrap files、docs、memory prompt、workspace notes、extra system prompt，再按固定章节顺序组装成 system prompt，并通过 `systemPromptReport` 把这次装配结果做成可观察对象。换句话说，OpenClaw 真正在管理的不是 prompt 文本，而是 **prompt 的来源结构**。

## 这部分在系统里负责什么

这一层要回答的是：

- 一轮 agent run 开始前，system prompt 到底由哪些来源拼出来
- 哪些内容是稳定底板，哪些是动态段落
- 为什么 subagent / command / main agent 看到的 prompt 不一样
- 为什么 context 不只是“文件注入”，还包括 tools、skills、docs、runtime、memory、reply tags、heartbeats、silent replies 等平台规则

如果没有这一层，前面我们读过的：

- session runtime
- gateway routing
- memory/context injection
- subagent lifecycle

都还是散的。

而 prompt composition 这一层做的事，就是把它们收口到一轮 run 的真实入口里。

一句话说：

> **前面那些平台能力最后都得在 prompt assembly 这一层变成模型能看到的工作面。**

## 主链路

我这轮抓的主链路是这几段：

1. **reply command 侧为命令模式组装 system prompt bundle**  
   `src/auto-reply/reply/commands-system-prompt.ts`
2. **run attempt 侧为 embedded run 组装真正的运行时 prompt**  
   `src/agents/pi-embedded-runner/run/attempt.ts`
3. **embedded prompt builder 只是薄包装，真正拼接逻辑在 agent-level system prompt builder**  
   `src/agents/pi-embedded-runner/system-prompt.ts`
4. **prompt 参数先被标准化成 runtime / timezone / repoRoot 等结构**  
   `src/agents/system-prompt-params.ts`
5. **bootstrap files 先解析，再转成 injected context files**  
   `src/agents/bootstrap-files.ts`
6. **最终统一由 `buildAgentSystemPrompt()` 按章节组装**  
   `src/agents/system-prompt.ts`
7. **组装完成后，再生成 `systemPromptReport` 做结构化审计**  
   `src/agents/system-prompt-report.ts`

一句话串起来：

> **OpenClaw 先把 prompt 的原料都收上来，再做参数规范化，再做文件注入裁剪，最后按稳定章节模板装配成 system prompt，并给这次装配留一份结构报告。**

## 关键文件与代码片段

### 1. `run/attempt.ts`：真正的 run 在模型启动前已经做完 prompt assembly

这里最关键的是这段：

```ts
const appendPrompt = buildEmbeddedSystemPrompt({
  workspaceDir: effectiveWorkspace,
  ...
  skillsPrompt: effectiveSkillsPrompt,
  docsPath: docsPath ?? undefined,
  workspaceNotes,
  reactionGuidance,
  promptMode: effectivePromptMode,
  runtimeInfo,
  messageToolHints,
  sandboxInfo,
  tools: effectiveTools,
  modelAliasLines: buildModelAliasLines(params.config),
  userTimezone,
  userTime,
  userTimeFormat,
  contextFiles,
  memoryCitationsMode: params.config?.memory?.citations,
})
```

这个调用本身已经说明问题了：

system prompt 的原料不只是“几段文案”，而是平台运行期里很多正式对象：

- tools
- skillsPrompt
- docsPath
- workspaceNotes
- reactionGuidance
- promptMode
- runtimeInfo
- sandboxInfo
- modelAliasLines
- contextFiles
- memoryCitationsMode

我的判断：

> **OpenClaw 的 system prompt 是平台配置和运行时状态的投影，而不是静态模板。**

### 2. `pi-embedded-runner/system-prompt.ts`：embedded builder 是一层薄包装

这一层其实很克制：

```ts
return buildAgentSystemPrompt({
  workspaceDir: params.workspaceDir,
  ...
  toolNames: params.tools.map((tool) => tool.name),
  toolSummaries: buildToolSummaryMap(params.tools),
  ...
  contextFiles: params.contextFiles,
  memoryCitationsMode: params.memoryCitationsMode,
})
```

这说明 OpenClaw 没把 embedded runner prompt 单独做成一套平行体系，而是：

- embedded 层负责把运行期对象整理好
- 真正的 prompt composition 收口到统一的 agent-level builder

这个设计很对。

因为如果 main run、embedded run、commands run 各自有一套 prompt builder，后面维护会很快分叉。

### 3. `system-prompt-params.ts`：先规范 runtime 参数，再进 prompt builder

`buildSystemPromptParams()` 做的事很基础，但非常必要：

- resolve `repoRoot`
- resolve `userTimezone`
- resolve `userTimeFormat`
- format `userTime`
- 统一生成 `runtimeInfo`

这说明 OpenClaw 在 prompt assembly 上有一个重要原则：

> **先把输入参数结构化和标准化，再去拼 prompt。**

不是边算边拼字符串。

这会让很多东西更稳：

- 时间信息
- repo 根路径
- runtime line
- channel / model / shell / host 信息

### 4. `bootstrap-files.ts`：文件注入不是直接把所有 workspace 文件塞进去

这里的链路很关键：

1. `resolveBootstrapFilesForRun()`
2. `filterBootstrapFilesForSession()`
3. `applyBootstrapHookOverrides()`
4. `buildBootstrapContextFiles()`

这意味着 OpenClaw 对“哪些文件能进 prompt”是有过滤和预算控制的。

尤其：

```ts
const contextFiles = buildBootstrapContextFiles(bootstrapFiles, {
  maxChars: resolveBootstrapMaxChars(params.config),
  totalMaxChars: resolveBootstrapTotalMaxChars(params.config),
  warn: params.warn,
})
```

这说明 injected files 根本不是无限制拼接，而是受：

- 单文件上限
- 总字符预算
- hook override
- session filter

共同约束。

一句话判断：

> **bootstrap context 是预算受控注入，不是把 workspace 当喂料池。**

### 5. `commands-system-prompt.ts`：命令模式也走同一套装配观

这文件很值钱，因为它说明 `/context`、命令执行这些场景，并不是走完全不同的脑回路。

它也会做：

- resolve bootstrap context
- build workspace skill snapshot
- create tools
- buildSystemPromptParams
- buildAgentSystemPrompt

也就是说，command mode 只是：

> **共享同一个 prompt composition worldview，只是输入原料和运行场景更轻。**

这很重要，因为它避免了“主 agent 一套 prompt，命令模式另一套 prompt，调试信息完全对不上”的情况。

### 6. `system-prompt.ts`：真正的 prompt 不是一段大模板，而是一组 section builders

这是这轮最值得看的文件。

它不是一大段硬编码文案，而是很多 section builder 拼出来的，比如：

- `buildSkillsSection()`
- `buildMemorySection()`
- `buildUserIdentitySection()`
- `buildTimeSection()`
- `buildReplyTagsSection()`
- `buildMessagingSection()`
- `buildVoiceSection()`
- `buildDocsSection()`

然后主函数里按顺序装配：

- Tooling
- Tool Call Style
- Safety
- CLI Quick Reference
- Skills
- Memory
- Model Aliases
- Workspace
- Documentation
- Sandbox
- Authorized Senders
- Current Date & Time
- Workspace Files (injected)
- Reply Tags
- Messaging
- Voice
- Project Context
- Silent Replies
- cache boundary
- extra/group/subagent context
- Heartbeats
- Runtime

我的判断：

> **OpenClaw 把 system prompt 当成“结构化 prompt 文档”，而不是一段提示词。**

这会带来两个直接好处：

1. 各 section 能单独裁剪、复用、最小化
2. 不同 run mode 可以共享大部分结构，只改少量 section

### 7. `system-prompt.ts`：prompt mode 说明 OpenClaw 不追求“一份 prompt 走天下”

这里的 `PromptMode` 很关键：

- `full`
- `minimal`
- `none`

而且文件里明确写了：

- `minimal` 用于 subagents
- `none` 只保留最基本 identity line

这说明 OpenClaw 的设计不是“把 main agent prompt 给所有人复用”，而是：

> **不同会话角色看到的是同一家族 prompt，但密度和职责不同。**

这和前面读 subagent lifecycle 时的判断正好闭环：

- subagent 是托管执行单元
- 所以 prompt 也应该更聚焦、更轻、更少平台噪音

### 8. `system-prompt.ts`：工具说明、平台规则、消息路由，全部被纳入 prompt assembly

我这轮最大的感受之一是：OpenClaw 的 system prompt 里真正重的，不是 persona，而是 **平台操作规范**。

比如：

- 工具清单和工具摘要
- cron / exec / process 的边界
- ACP harness 路由规则
- approval 规则
- reply tags 规则
- messaging 路由规则
- silent reply 规则
- heartbeat 规则
- sandbox 规则
- runtime line

这说明 OpenClaw 对 prompt assembly 的看法是：

> **system prompt 不是用来塑造“说话风格”的，它首先是平台运行协议的承载体。**

这点非常像成熟 agent runtime，而不是聊天机器人产品。

### 9. `system-prompt.ts`：Project Context 在 prompt 里是显式 section，不是隐式拼接

当 `contextFiles` 有内容时，它会明确输出：

- `# Project Context`
- 每个文件一个 `## path`
- 里面放完整内容

而且还有特殊规则：

- 如果存在 `SOUL.md`，就明确提示要 embody persona/tone

这个设计我觉得很好，因为它让 project context 不是黑盒：

> **文件为什么进了 prompt、以什么标题进、模型应该怎样对待这些文件，都是显式可读的。**

### 10. `SYSTEM_PROMPT_CACHE_BOUNDARY`：Prompt 里还内建了缓存边界意识

这个细节很值钱。

源码里写得很清楚：

> 把大的稳定 prompt context 放在 cache boundary 上面，动态 group/session additions 放下面，减少 cache invalidation。

这说明 OpenClaw 不只是会拼 prompt，它已经在考虑：

- 哪些部分稳定
- 哪些部分经常变
- 怎么安排位置以优化缓存复用

这已经是很 runtime-native 的 prompt engineering 了，不是一般项目会想到的层级。

## 设计判断

### 1. OpenClaw 真正在管理的是“prompt 来源结构”，不是 prompt 文案

这是这轮最核心的判断。

如果只看最终 prompt 文本，会觉得它只是很长。

但源码层面看，真正被管理的是：

- tools
- skills
- docs
- workspace notes
- bootstrap files
- runtime info
- heartbeat
- memory prompt section
- extra system prompt
- project context
- role-specific mode

也就是说，OpenClaw 管的是 prompt 的 **构成图谱**。

### 2. section builder + prompt mode 是正确的长期维护方式

这套比“大模板字符串拼接”成熟很多。

原因很简单：

- main agent / subagent / command mode 需求不同
- 平台规则会持续增长
- 某些 section 要条件注入
- 某些 section 以后还要做预算治理

如果没有结构化 section builder，后面 prompt 会不可维护。

### 3. bootstrap 预算、context files、prompt report 三件事是一个闭环

我觉得这三件事应该一起看：

- `bootstrap-files.ts` 控预算
- `system-prompt.ts` 负责装配
- `system-prompt-report.ts` 负责审计

这才是完整闭环。

只有注入没有预算，prompt 会爆。  
只有预算没有报告，没人知道哪里爆。  
只有报告没有结构化 builder，也很难精细治理。

### 4. OpenClaw 的 prompt 更像“runtime contract”而不是“assistant persona”

这点很重要。

很多系统把 system prompt 的重心放在：

- 语气
- 人设
- 写作风格

OpenClaw 当然也有这些，但它更核心的是：

- 工具协议
- 平台纪律
- 路由规则
- 会话规则
- 安全和审批规则
- 运行时环境说明

这就是为什么它读起来更像平台说明书，而不是聊天设定卡。

### 5. Prompt composition 其实是 OpenClaw 架构的汇合点

前面几篇看起来像不同模块：

- session
- subagent
- routing
- memory

但到了 prompt assembly 这里，会发现它们都得落到：

> **这一轮 run，到底让模型看见什么。**

所以这篇实际上是前四篇的汇合点。

## 关键术语解释

- **prompt composition**：把不同来源的上下文、规则、工具说明和运行信息装配成最终 prompt 的过程。
- **system prompt assembly**：system prompt 的结构化组装过程，强调有顺序、有 section、有条件注入。
- **prompt mode**：不同会话角色使用的 prompt 密度模式，如 `full`、`minimal`、`none`。
- **bootstrap files**：运行前从 workspace 读取并候选注入的上下文文件。
- **context files**：经过过滤、裁剪、预算控制后真正注入 prompt 的文件内容。
- **workspace notes**：附加到 prompt 的工作区说明或局部运行备注。
- **tool summaries**：工具描述的压缩摘要，用来构成 Tooling section。
- **systemPromptReport**：对本轮 prompt 组成和体积的结构化报告。
- **cache boundary**：为了提高 prompt 缓存复用率而人为划定的稳定/动态内容分界点。

## 本轮遗留问题

1. `buildWorkspaceSkillSnapshot()` 如何生成 skills prompt，这一层还可以单独做一篇“skills prompt 形成机制”。
2. `buildBootstrapContextFiles()` 的裁剪策略和 warning 策略值得单独下钻，尤其是对大仓库共读时的影响。
3. `SYSTEM_PROMPT_CACHE_BOUNDARY` 在不同 provider/runtime 下的实际收益和边界，还值得做一次性能向共读。
4. 后面可以专门补一篇“commands mode vs embedded run mode”的 prompt 差异对照。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/Ea1Bd1u1zovoPyxs1m1cfluSnM2`)
- Vault git sync: in progress
