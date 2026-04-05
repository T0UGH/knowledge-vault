---
title: OpenClaw 共读：tool execution model
date: 2026-04-05
tags:
  - openclaw
  - co-reading
  - architecture
  - tools
  - execution
status: active
---

# OpenClaw 共读：tool execution model

## 一句话结论

OpenClaw 的 tool execution model 不是“模型调几个函数”这么简单，而是一套 **按能力类型分层派发** 的执行体系：本机动作走 `exec/process` 这一条运行时通道，会话编排走 `sessions_spawn/subagents` 这一条会话树通道，计划性动作走 `cron` 这一条主动执行通道，而所有这些工具最终都被统一放进 `createOpenClawTools()` + `tool policy` 这套框架里。换句话说，**OpenClaw 的 tool 不是一组函数接口，而是 runtime 的动作总线。**

## 这部分在系统里负责什么

前面几篇已经把会话、路由、heartbeat、cron、session topology 这些骨架读出来了。

但一个长期助理 runtime 不能只会“维持结构”，还得真的把动作执行出去。tool execution model 这一层回答的就是：

- agent 调工具时，哪些动作落到本机 shell / process 层
- 哪些动作会变成新 session / 新 subagent
- 哪些动作会被登记成未来执行的 cron job
- 为什么这些工具看起来都在同一个 tool list 里，但底层运行语义完全不同
- tool policy 在哪里真正限制这些动作

所以这一层是在回答：

> **OpenClaw 为什么不只是会话系统，而是一个能真正执行动作的 runtime。**

## 主链路

我这轮抓的主链路是这几段：

1. **`createOpenClawTools()` 统一把所有工具组装进 agent 可见工具集**  
   `src/agents/openclaw-tools.ts`
2. **`tool-catalog.ts` 定义工具分组和 profile，说明工具不是乱堆在一起**
3. **`exec` 负责启动真实命令执行**  
   `src/agents/bash-tools.exec.ts`
4. **`process` 负责托管 exec 产生的后台进程与交互通道**  
   `src/agents/bash-tools.process.ts`
5. **`sessions_spawn` / `subagents` 负责把动作升级成子会话/子运行单元**  
   `src/agents/tools/sessions-spawn-tool.ts`
6. **`cron` 负责把动作升级成计划性执行契约**  
   `src/agents/tools/cron-tool.ts`
7. **`pi-tools.policy.ts` 决定不同 session / subagent / channel / provider 能不能用哪些工具**

一句话串起来：

> **OpenClaw 先把工具统一暴露给 agent，再按动作类型把它们分流到本机执行、会话编排、计划执行三条不同 runtime 通道，并用 tool policy 在外围做约束。**

## 关键文件与代码片段

### 1. `openclaw-tools.ts`：工具执行模型先从“统一装配”开始

这文件最重要的不是某个工具，而是它说明 OpenClaw 先有一个统一工具装配面。

`createOpenClawTools()` 里把这些能力放到同一个 agent tool set 里：

- `cron`
- `message`
- `gateway`
- `sessions_list`
- `sessions_history`
- `sessions_send`
- `sessions_spawn`
- `sessions_yield`
- `subagents`
- `session_status`
- web tools
- image/pdf tools

我的判断：

> **OpenClaw 的 tool execution model 首先是“统一可见，底层异构执行”。**

也就是说，模型侧看到的是统一 tool surface，但 runtime 侧并不是统一的执行语义。

### 2. `tool-catalog.ts`：工具目录本身已经在暗示执行分层

`CORE_TOOL_SECTION_ORDER` 里把工具分成：

- Files
- Runtime
- Web
- Memory
- Sessions
- Messaging
- Automation
- Agents
- Media

其中对这篇最关键的是三块：

- **Runtime**：`exec` / `process`
- **Sessions**：`sessions_*` / `subagents`
- **Automation**：`cron`

这说明 OpenClaw 不是把工具仅仅当成“函数列表”，而是在目录层面就已经有：

> **运行时动作、会话动作、自动化动作**

这三类区别。

### 3. `bash-tools.exec.ts`：`exec` 是本机动作入口，不是随手 `spawn()` 一下就完

`exec` 这层非常厚，说明它根本不是薄包装。它在处理：

- host / gateway / node 等 target
- security / ask / elevated policy
- PATH / shell env
- sandbox env
- command analysis / approval
- background vs foreground
- PTY / non-PTY
- cwd / env / path prepend
- approval request binding

一句话判断：

> **OpenClaw 的 exec 不是“shell 工具”，而是受 policy、approval、host-target、sandbox 共同约束的本机动作入口。**

这也是为什么它能放进长期助理 runtime，而不只是给模型一个危险 shell。

### 4. `bash-tools.process.ts`：`process` 不是执行命令，而是托管“已执行命令”的生命周期

这个文件特别能说明 OpenClaw 的运行时分层。

它支持的 action 包括：

- `list`
- `poll`
- `log`
- `write`
- `send-keys`
- `submit`
- `paste`
- `kill`

也就是说：

- `exec` 负责起进程
- `process` 负责后续交互和托管

我的判断：

> **OpenClaw 把“启动动作”和“管理进程生命周期”明确拆成两层。**

这很像成熟 runtime，而不是把所有 shell 交互都糊进一个工具。

### 5. `sessions-spawn-tool.ts`：有些动作不该落到 shell，而该升级成“新 session / 新 runtime”

这层非常关键。

`sessions_spawn` 的参数一看就知道它不是普通 helper：

- `runtime: subagent | acp`
- `mode: run | session`
- `thread`
- `cleanup`
- `sandbox`
- `streamTo`
- `attachments`

而且它内部会根据 runtime：

- 走 `spawnSubagentDirect()`
- 或走 `spawnAcpDirect()`
- 必要时还会 `registerSubagentRun()`

这说明：

> **OpenClaw 里有些“动作”根本不应该变成一个 shell command，而应该变成一个正式子会话。**

这就是为什么 tool execution model 里，session tools 是单独一条执行通道。

### 6. `cron-tool.ts`：有些动作不该立刻执行，而该变成“未来执行契约”

`cron` 这层又是第三种语义。

它不是：
- 直接执行
- 也不是立刻起子会话

而是把动作规范化成 job：

- `schedule`
- `sessionTarget`
- `wakeMode`
- `payload`
- `delivery`
- `failureAlert`

我的判断：

> **cron tool 的本质不是“运行任务”，而是“登记一份未来的执行契约”。**

所以从执行模型看：

- `exec` = 现在在本机干
- `sessions_spawn` = 现在起一个新运行单元去干
- `cron` = 未来按契约再干

这三者非常清楚。

### 7. `pi-tools.policy.ts`：真正让 tool execution model 可控的，是外围 policy，不是每个工具自己单兵防守

这文件很值钱。它不只是一个 allow/deny 工具名的地方，而是在做：

- subagent tool deny/allow
- leaf vs orchestrator subagent 区分
- provider-specific policy
- channel/group context policy
- sandbox tool policy

比如它明确写了：

- subagent 永远禁止 `gateway` / `cron` / `sessions_send`
- leaf subagent 额外禁止 `subagents` / `sessions_spawn` / `sessions_list` / `sessions_history`

这说明 OpenClaw 的 tool execution model 成熟点在于：

> **不是每个工具自己猜“该不该让这个 agent 调我”，而是有一层统一 policy 在外部收边界。**

### 8. 一个最简执行分层图

如果把这篇收成一张最简图，我会这样画：

```text
Agent sees unified tools
├── Runtime execution
│   ├── exec      -> 启动本机/节点/网关命令
│   └── process   -> 托管已启动进程的生命周期
├── Session execution
│   ├── sessions_spawn -> 起新 subagent / ACP session
│   ├── subagents      -> 管理子会话
│   └── sessions_send  -> 跨会话投递
├── Scheduled execution
│   └── cron      -> 创建未来执行契约
└── Policy layer
    └── tool policy / subagent policy / channel-policy / provider-policy
```

这张图基本就是我对 OpenClaw tool execution model 的压缩理解。

## 设计判断

### 1. OpenClaw 的 tool 不是函数接口，而是动作总线

这是我这轮最核心的判断。

表面上看工具都长得像函数：
- name
- schema
- execute

但系统层面看，它们承载的是不同动作类型：

- 本机命令执行
- 进程生命周期托管
- 子会话生成
- 计划性调度
- 跨会话消息投递

所以更准确地说，tool system 是：

> **agent 发起动作的统一总线。**

### 2. `exec/process`、`sessions_spawn/subagents`、`cron` 三条线是核心执行三分法

这篇最大的收获就是可以把执行模型收成这三分法：

#### 立即本机执行
- `exec`
- `process`

#### 立即会话化执行
- `sessions_spawn`
- `subagents`

#### 延迟计划执行
- `cron`

这和 session topology / long-running assistant 主线是能接上的。

### 3. OpenClaw 的成熟度在于“统一可见 + 异构执行 + 外围治理”

这三点缺一不可：

- **统一可见**：模型看到的是一个统一工具世界
- **异构执行**：底层按动作类型走不同 runtime
- **外围治理**：tool policy 在外部统一收边界

如果只有前两点没有 policy，很危险；只有 policy 没有统一 surface，模型又不好用。

### 4. 这也是它比普通 chat agent 更像 runtime 的地方

普通 chat agent 往往是：
- prompt
- tool call
- function execution
- answer

OpenClaw 明显不是这么扁平。

它更像：
- session runtime
- routing
- prompt governance
- execution bus
- long-running modes
- policy layer

所以 tool execution model 正好是它“runtime 感”最强的一个证明点。

### 5. trade-off：工具统一了，理解成本也会变高

这套设计的代价也很明显：

- 工具名在模型眼里是统一的，但底层语义差异很大
- 使用者要理解什么时候该 exec，什么时候该 spawn，什么时候该 cron
- policy 层一旦收不好，系统会要么太危险，要么太束手束脚

也就是说，OpenClaw 用更强执行力，换来了更高的工具语义复杂度。

## 关键术语解释

- **tool execution model**：agent 调用工具时，系统如何把不同类型的动作派发到对应 runtime 通道的整体机制。
- **runtime execution**：立即在本机、节点或网关环境里执行命令的动作类型。
- **session execution**：把动作提升为新 session / 子会话运行单元的执行类型。
- **scheduled execution**：把动作登记成未来执行契约的执行类型。
- **tool policy**：围绕工具使用做 allow/deny/profile 约束的治理层。
- **subagent policy**：针对子 agent 会话额外收紧的工具策略。
- **approval**：在执行敏感命令前要求系统或人类确认的机制。
- **delivery contract**：cron 等工具约定任务结果如何交付给用户或外部系统的契约。

## 本轮遗留问题

1. 按我们之前的顺序，下一篇最自然就是 `plugin / channel architecture`，因为执行完动作之后，下一层就是“结果如何活在不同表面上”。
2. 如果后面要收一个“OpenClaw 最小闭环 runtime”，这篇很适合拿来判断哪些执行通道必须保留。
3. 还可以专门补一篇：`tool policy` 体系如何作为 OpenClaw 的安全边界工作。

## Pipeline Status
- Obsidian draft: done
- Term explanation: done
- Humanizer: done (light pass)
- Feishu publish: done (`https://feishu.cn/docx/LpHKdoGYeoP2JgxkFpNczkEOnaf`)
- Vault git sync: done (`86ea68b add OpenClaw co-reading note: tool-execution-model`)
