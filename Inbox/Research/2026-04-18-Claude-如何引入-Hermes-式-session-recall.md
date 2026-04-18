---
title: Claude 如何引入 Hermes 式 session recall
date: 2026-04-18
status: draft
tags:
  - Claude
  - Hermes
  - session-recall
  - MCP
  - memory-system
---

# Claude 如何引入 Hermes 式 session recall

## 这轮记录什么

这篇记录想回答一个很具体的问题：

> 如果要把 Hermes 的 `session recall` 思路引入 Claude，第一步是不是应该通过 MCP 注册一个 `session_search` tool？

我的判断是：**是，但只对了一半。**

## 先给结论

### 结论一：作为第一步，MCP 暴露 `session_search` 是最合适的入口

如果目标是先把 Claude 的“按需翻旧账”能力接起来，那么通过 MCP 注册一个 `session_search` tool，基本就是最自然、最小侵入、收益最快的方案。

原因很简单：Hermes 自己的 session recall，在真正执行“检索过去会话、返回摘要”这一步时，本来就是工具化的。

也就是说，把它映射成 Claude 可调用的 MCP tool，并不是勉强套进去，而是和 Hermes 原本的实现形态高度一致。

### 结论二：如果目标是复刻 Hermes 的完整 session recall，只做一个 MCP tool 还不够

Hermes 真正强的地方，不是“有一个 search tool”，而是它把这件事做成了两层结构：

1. **持续归档 / 建索引层**：主循环持续把会话材料沉淀下来
2. **按需召回层**：模型在当前轮判断需要时，再调用 `session_search`

所以如果在 Claude 侧只做 MCP `session_search`，那你拿到的只是 **recall front door**，还不是 Hermes 那种完整的“经验池 + 入口”机制。

## 为什么 MCP tool 是对的第一步

### 1. Hermes 的 recall 动作本来就是 tool 化的

从源码结构看，Hermes 的 `session_search` 不是硬编码在主循环里的固定步骤，而是一个显式工具：

- `tools/session_search_tool.py` 里定义了 `session_search(...)`
- 同文件里把它注册成 `name="session_search"`

这意味着：

- 过去会话并不会自动每轮都被拉出来
- 模型是在当前问题需要时，主动调用工具完成检索和总结

换句话说，Hermes 的 session recall 本质上就是：

> **模型驱动的按需召回，而不是系统强制的每轮回放。**

这一点和 Claude + MCP 的契合度非常高。

### 2. Claude 很适合“自己决定何时回忆”

Claude 本来就很擅长处理这种场景：

- 用户说“我们之前是不是聊过这个？”
- 用户说“上次怎么处理的？”
- 用户提到“remember / last time / before / we did this before”

在这些语境下，只要工具描述写得足够明确，Claude 很容易学会：

- 当前上下文不够
- 这更像跨 session 问题
- 应该调用 `session_search`

因此，MCP tool 是一个非常适合 Claude 的 recall 接口层。

### 3. 这是最小侵入的接法

如果直接在 Claude 一侧强行做“自动 recall 注入”，往往会遇到几个问题：

- 容易把无关历史灌进当前轮
- prompt 越来越重
- recall 触发条件不清晰
- 很难解释“为什么这次想起了那次没想起”

而 MCP tool 方式把边界切得很清楚：

- Claude 负责判断“该不该翻旧账”
- MCP server 负责“怎么搜、搜什么、返回什么”

这会让系统更可控，也更容易迭代。

## 但为什么说“只做 MCP tool 还不够”

Hermes 的 session recall 能成立，不只是因为有个 `session_search`，而是因为它背后一直有一层持续运转的 transcript 基础设施。

### Hermes 的完整机制其实分两层

#### 第一层：持续沉淀过去会话

在 Hermes 里，主循环会持续把当前会话刷进 `SessionDB`。

更关键的是，这件事不是“有需要时再存”，而是默认持续发生。

这意味着 Hermes 一边在回答当前问题，一边也在默默给未来的 recall 准备原料。

如果没有这一层，`session_search` 再漂亮也只是空壳，因为根本没有足够稳定的过去材料可搜。

#### 第二层：按需检索过去会话

等模型在某一轮判断：

- 当前问题和过去经历有关
- 靠当前上下文不够
- 有必要翻旧账

这时才会去调 `session_search`。

所以 Hermes 的真正结构不是：

> 有个 recall tool

而是：

> **先长期积累，再按需取回。**

这也是为什么单做 MCP `session_search` 还不够，因为那只覆盖了“取回”这半边。

## 放到 Claude 里，应该怎样理解这件事

### 最小可用版：先把 recall 入口接起来

如果想快速落地，最合理的第一版是：

1. 持久化 Claude 历史会话
2. 提供一个 MCP `session_search`
3. 在 tool description 里明确触发语境，例如：
   - last time
   - before
   - remember
   - we did this before
   - 之前怎么弄的
   - 上次聊到哪了

这样 Claude 就具备了“先问自己要不要翻旧账，再决定是否调工具”的能力。

这是一个非常好的 v1。

### 更像 Hermes 的完整版：把底座也补上

如果目标是做成真正 Hermes 风格的 session recall，至少还要加上下面这些能力：

#### 1. transcript store

把 Claude 的对话、任务、上下文持续落盘。

技术上可以是：

- SQLite + FTS
- Postgres + 全文检索
- 文件归档 + 索引层

重点不是存在哪，而是必须形成一套**长期可检索的过去经验池**。

#### 2. summary / ranking / dedup

Hermes 的 `session_search` 不是单纯 grep 文本，它会做：

- 搜索结果排序
- session 去重
- child / parent session 归并
- 最后返回更像“可读摘要”而不是原始命中片段

如果 Claude 端只是返回一堆原始日志，那只是检索，不是 recall。

#### 3. current session 边界处理

Hermes 会避免把“当前 session 自己”又作为 recall 结果回捞出来，因为当前轮其实已经有这部分上下文。

这类边界如果不处理，Claude 很容易出现：

- 明明就在当前对话里
- 却又去查一遍
- 返回重复内容

#### 4. future extensions

如果再往前走一步，可以逐渐加入：

- `recent_sessions`
- `session_open`
- `session_summarize`
- recall 候选建议
- 主题级经验视图

这样 Claude 端的 recall 才会从“能搜”逐渐升级成“会回忆”。

## 它和 memory 的差别

这是一个很容易混的点。

### memory 更像“预取注入”

Hermes 里的 memory manager 会在当前轮开始前做 `prefetch_all(...)`，然后把结果注入当前轮上下文。

也就是说，memory 更像：

> **系统先猜你这轮大概率需要哪些稳定记忆，再提前塞进来。**

### session recall 更像“按需检索”

session recall 不一样。

它不是每轮自动注入，而是模型发现：

- 当前问题和“过去某次具体会话”强相关
- 仅靠稳定记忆不够
- 需要翻旧账

这时才去调工具。

所以从机制上说：

- **memory** 偏预注入
- **session recall** 偏按需召回

这也是为什么如果把 Hermes 的 session recall 思路移植到 Claude，优先落成 MCP tool 会更合理。

## 一个更准确的表述

如果只用一句话描述给自己以后看，我会这么说：

> **对于 Claude，MCP `session_search` 很适合作为 Hermes 式 session recall 的 front door；但它必须建立在独立 transcript store / index layer 之上，否则你只是给 Claude 一个搜索接口，而不是给它一个可持续积累的过去经验系统。**

## 推荐落地顺序

### Phase 1：先做对

- 持久化 Claude 会话
- 做 MCP `session_search`
- 优化 tool description，让 Claude 知道什么时候该搜

### Phase 2：再做像 Hermes

- 做 session 去重和父子 session 归并
- 做摘要层
- 做 recent sessions
- 做 recall 排序和命中质量优化

### Phase 3：再做得更像“会回忆”

- 做主题级 recall
- 做自动 recall 候选
- 和 memory / skill 层联动

## 最后一句总结

Hermes 给 Claude 的启发，不是“给它加一个搜索工具”这么简单，而是：

> **把过去经验做成一个长期积累的池子，再给模型一个按需调取这池子内容的入口。**

其中，MCP `session_search` 非常适合作为第一锤；但真正让它像 Hermes 的，不是这把锤子本身，而是锤子后面那整套长期归档与索引底座。
