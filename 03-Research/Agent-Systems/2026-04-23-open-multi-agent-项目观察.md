---
title: open-multi-agent 项目观察
date: 2026-04-23
tags:
  - agent
  - multi-agent
  - orchestration
  - typescript
  - cli
  - codex
  - claude-code
  - cursor
status: active
aliases:
  - open-multi-agent 调研
  - OMA 项目观察
source_repo: https://github.com/JackChen-me/open-multi-agent
---

# open-multi-agent 项目观察

## 记录时间

- 2026-04-23 13:17:42 +0800

## 这是什么

`open-multi-agent` 是一个 **Node.js / TypeScript 侧的轻量多智能体编排框架**。

它要解决的问题不是“再做一个聊天壳”，而是：

> 当你已经有多个 agent 角色时，怎样在 TypeScript 里把它们围绕一个 goal 自动拆任务、按依赖执行、并行协作、共享结果，并在最后综合输出。

它的核心入口是：

- `runAgent()`：单 agent 一次性运行
- `runTeam()`：给 goal，coordinator 自动拆任务 DAG，再编排执行
- `runTasks()`：调用方自己提供任务图，框架负责执行

## 核心判断

### 1. 它是 orchestration engine，不是 coding-agent 产品

最准确的定位是：

> **TypeScript 版多 agent orchestration library / runtime。**

它更接近：

- task decomposition
- task DAG
- dependency-aware scheduling
- shared memory
- synthesis coordinator

而不是：

- Claude Code
- Codex CLI
- Cursor
- 这种面向人类长期交互的 coding-agent shell / IDE 产品

### 2. 它有 CLI，但不是新的 Claude Code / Codex

项目自带一个很小的 CLI：`oma`。

根据 `docs/cli.md`，它的定位是：

- 给 **shell scripts / CI** 用的 JSON-first CLI
- 主要暴露 `runTeam` / `runTasks` / `provider`
- stdout 输出结构化 JSON
- 不提供 interactive REPL
- 不提供 session persistence
- 不提供人类审批闸门
- 不提供像 Claude Code / Codex 那种长期协作式 coding shell 体验

所以更像：

> **把 orchestration 能力打包成命令行入口**

而不是：

> **一个新的 coding-agent terminal 产品**

## 当前支持什么

### 内建 provider / adapter

仓库当前原生支持的 provider 主要包括：

- `anthropic`
- `openai`
- `azure-openai`
- `gemini`
- `grok`
- `minimax`
- `deepseek`
- `copilot`

以及所有 **OpenAI-compatible** 推理服务，例如：

- Ollama
- vLLM
- LM Studio
- llama.cpp server
- 其他兼容 OpenAI API 的模型服务

这意味着它天然擅长的是：

> **统一编排不同模型 / 不同 provider / 不同工具集**

而不是统一编排不同“现成 coding-agent 产品”。

### 内建工具

默认内建工具主要是：

- `bash`
- `file_read`
- `file_write`
- `file_edit`
- `grep`
- `glob`

团队编排场景下还可以启用：

- `delegate_to_agent`

这说明它的“执行层”是 LLM + tool-use 驱动的 agent runtime，而不是 tmux worker / IDE session / interactive subprocess orchestration。

## 能不能在里面用 Codex / Claude Code / Cursor

## 结论

**默认不能把 Codex CLI / Claude Code / Cursor 当一等公民直接塞进去用。**

### 可以的部分

如果说的是“模型能力”，那么：

- Claude 模型：可以通过 `anthropic` provider
- OpenAI 模型：可以通过 `openai` provider
- Copilot：有专门 `copilot` provider
- 本地模型：可以通过 OpenAI-compatible `baseURL`

也就是说，**模型侧** 是能接的。

### 不可以直接等同的部分

如果说的是这些“产品 / 运行时”本身：

- Codex CLI
- Claude Code
- Cursor

那么当前并没有看到官方的一等公民集成层。也没有看到类似：

- 把 Claude Code 当 worker runtime 启起来
- 把 Codex CLI 当 agent backend 统一调度
- 把 Cursor session 纳入其 task runtime

所以不能把它理解成：

> “一个能直接把 Codex / Claude Code / Cursor 都纳进来的统一多 agent 壳。”

## 那有没有间接接法

### 1. 通过 MCP —— 这是正路

项目已经支持 MCP tools。

仓库里有 `connectMCPTools()` 和 `examples/integrations/mcp-github.ts`，说明它可以：

- 启动一个 MCP server
- 把 MCP 暴露出来的 tools 注册成 agent 可用工具
- 让 agent 在自己的 tool-use 回路里调用这些外部能力

所以如果外部生态（例如 Claude Code / Cursor 周边能力）以 **MCP server** 暴露，`open-multi-agent` 可以比较自然地接进去。

但这里接进去的是：

> **工具能力面**

不是：

> **把整个 Claude Code / Cursor / Codex runtime 当成本框架内部 agent backend**

### 2. 通过 `bash` 调外部 CLI —— 理论可行，但很野

因为有 `bash` 工具，所以技术上可以让 agent 去跑外部命令，例如：

- 某个 codex CLI 命令
- 某个 claude CLI 命令
- 自己封装过的外部脚本

但这条路的问题很明显：

- CLI 输出不稳定
- 交互模式不友好
- 会话状态难继承
- 错误恢复差
- 容易变成 agent 套 agent 的 fragile 套壳

所以它不是项目设计的主路径。

## 更准确的对照

### open-multi-agent 更像

- TypeScript 多 agent orchestration library
- goal → coordinator → task DAG → execution → synthesis 的运行时
- 可嵌入 Node / Next.js / serverless / CI 的编排层

### open-multi-agent 不像

- Claude Code 这种长期交互式 coding shell
- Codex CLI 这种以人机协作编码为中心的终端产品
- Cursor 这种 IDE / agent workspace

## 适用场景

它适合：

- 在 Node/TS 后端里做多 agent workflow
- 多角色协作的 research / coding / review 管线
- 需要 dependency-aware task scheduling 的自动化执行
- 通过 MCP 或自定义工具把外部系统接入 agent 团队

它不适合直接承担：

- 人类长期交互的 coding assistant 主界面
- 编排 Claude Code / Codex / Cursor 这种现成交互式 agent 产品
- 需要 session persistence / approval gate / REPL 的代理终端体验

## 一句话结论

> **`open-multi-agent` 是一个多智能体编排框架，不是一个新的 coding-agent CLI。**
>
> 它能很好地统一编排模型、任务和工具；但当前并不是“把 Codex / Claude Code / Cursor 当原生 runtime 接进来”的产品。

## 相关线索

- `~/workspace/open-multi-agent/README.md`
- `~/workspace/open-multi-agent/docs/cli.md`
- `~/workspace/open-multi-agent/src/orchestrator/orchestrator.ts`
- `~/workspace/open-multi-agent/src/llm/adapter.ts`
- `~/workspace/open-multi-agent/src/tool/built-in/delegate.ts`
- `~/workspace/open-multi-agent/examples/integrations/mcp-github.ts`

## 后续可继续追的问题

1. 如果硬要接入 Codex / Claude Code / Cursor，哪一种适合走 MCP，哪一种只能走 subprocess / wrapper。
2. 它和 Hermes / OpenClaw / Claude Code 的边界差在哪：为什么它更像 orchestration layer，而不是长期 agent OS。
3. 如果想做“统一编排多 runtime”的系统，应该参考 `Maestro` 这类把 Claude Code / Gemini CLI / Codex 放进同一控制面的路线，而不是直接指望 `open-multi-agent` 现成承担这个角色。
