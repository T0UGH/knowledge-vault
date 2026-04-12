---
title: Codex 6 卷 index 草案 v1
date: 2026-04-12
status: draft
tags:
  - Codex
  - guidebook
  - index
  - source-reading
---

# Codex 6 卷 index 草案 v1

## 这份草案解决什么问题

这不是正文，也不是写作卡片，而是 **Codex guidebookv2 的卷级 index 草案**。

它只先解决四件事：

1. 每一卷到底回答什么主问题
2. 每一卷和前后卷怎么自然导流
3. 每一卷当前应该先吸收哪些旧材料
4. 后面拆 `README-writing-cards` 时，每卷的范围边界先怎么守住

一句话说：

> **先把卷级入口立住，再去拆篇级卡片。**

---

# Guidebook v2 总览（草案）

> `guidebookv2/` 是这套 Codex 源码导读手册的正式阅读入口。整本书不再按目录树平铺，也不再按 Phase 1 调研顺序展开，而是按读者理解 Codex 这套系统所需要跨过的 6 个认知台阶来组织。

## 先读这一句

> **这 6 卷不是材料分桶，而是一条连续问题链：先看清 Codex 到底是什么系统，再看它为什么能持续存在、怎样把 runtime 暴露成控制面、怎样把动作落成可管理执行、怎样长出平台化能力，最后怎样进一步长成审查、协作与高级 runtime。**

## 当前规划中的 6 卷主线

1. 卷一｜Codex 到底是什么系统
2. 卷二｜线程、状态与恢复是怎么成立的
3. 卷三｜Codex 的控制面怎么把 runtime 暴露出来
4. 卷四｜统一执行子系统怎样把动作变成可管理会话
5. 卷五｜能力系统怎样把 Codex 长成平台
6. 卷六｜审查、协作与高级 runtime 为什么不能当作边角功能

---

# 卷一｜Codex 到底是什么系统

## 这一卷回答什么

回答：**Codex 到底是什么形态的系统，真正主体在哪，哪些只是分发壳、入口壳、前端壳。**

## 为什么这一卷重要

因为 Codex 最容易被误读成：

- 一个 npm CLI
- 一个 TUI 工具
- 一个 app-server
- 一个“所有模块都差不多重要”的大仓库

这一卷的任务就是先把这些误读打掉，建立最基本的主体边界感。

## 前置知识

- 不要求先懂 app-server、thread、turn、unified-exec
- 只默认读者知道它是一个 coding agent/CLI 项目

## 建议阅读顺序（草案）

1. 为什么 `codex-cli/` 不是主体
2. 真正的统一入口在哪
3. TUI、app-server、mcp-server 各是什么
4. 为什么 `core` 才像 runtime 聚合中心
5. 为什么 `ThreadManager` 比 app-server 更接近 runtime owner

## 读完这一卷你会得到什么

- 能分清壳和主体
- 能分清入口层、前端层、控制面、runtime 核心层
- 不会再把 TUI 或 npm 壳误读成系统本体

## 当前优先吸收的旧材料

- `00-guidebook/01-系统总图与分层.md`
- `03-boundary-judgments/2026-04-11-codex模块边界判断-v1.md`
- `00-index/01-代码路径索引-入口与分发.md`
- `01-source-notes/01~05`

## 这一卷不急着展开什么

- 不深入 thread/turn 语义
- 不深入 app-server request semantics
- 不深入 unified-exec 内部链路
- 不提前讲平台化能力

---

# 卷二｜线程、状态与恢复是怎么成立的

## 这一卷回答什么

回答：**Codex 为什么不是“一次命令执行就结束”，而是一个可以持续、恢复、继续运行的线程化系统。**

## 为什么这一卷重要

卷一只回答“这是什么系统”，还没有回答：

- 为什么 thread 能成立
- 为什么状态能留下来
- 为什么恢复不是把数据库读出来这么简单

卷二负责把 Codex 的持续工作条件讲清楚。

## 前置知识

- 默认已经读完卷一
- 至少知道 `core`、`ThreadManager`、app-server 是不同层

## 建议阅读顺序（草案）

1. rollout 为什么才是正文真相源
2. SQLite state 为什么不是恢复核心
3. thread / turn / pending request 是怎么形成的
4. recovery 为什么更像 replay
5. turn-history 为什么不是 event log 镜像

## 读完这一卷你会得到什么

- 能分清 rollout 和 SQLite 的职责
- 能理解 recovery 的 replay 心智
- 能把 thread / turn / history 放回同一条持续工作主线里

## 当前优先吸收的旧材料

- `00-guidebook/02-状态持久化与恢复.md`
- `00-guidebook/04-turn-history语义层.md`
- `01-source-notes/06 / 11 / 17 / 18 / 34 / 43 / 47 / 50 / 51 / 58`

## 这一卷不急着展开什么

- 不展开控制面协议投影细节
- 不展开 unified-exec 会话执行细节
- 不展开 plugin / MCP / platform 层

---

# 卷三｜Codex 的控制面怎么把 runtime 暴露出来

## 这一卷回答什么

回答：**thread runtime 成立之后，Codex 怎样把它稳定投影成 TUI、SDK、远端调用者可观察、可控制、可恢复连接的控制面。**

## 为什么这一卷重要

Codex 很容易被理解成“一个厚 app-server”，但真正值钱的不是 app-server 这个名字，而是：

- listener task 怎样充当事件泵
- `bespoke_event_handling` 怎样做协议投影
- request semantics 怎样被稳定组织
- TUI 为什么越来越像跑在 app-server 之上

这一卷要把“控制面”立成一层正式结构，而不是零散 API 汇总。

## 前置知识

- 默认已经读完卷一、卷二
- 需要先知道 thread / turn / recovery 已成立

## 建议阅读顺序（草案）

1. app-server 为什么不是另一套 runtime
2. listener / projection / thread state 怎么闭环
3. `ServerRequestResolved` 到底覆盖了什么
4. `DynamicToolCall` 为什么不属于同一 resolved 模型
5. 为什么 Codex 的控制面不是薄 API 层

## 读完这一卷你会得到什么

- 能分清 runtime owner 和 control-plane facade
- 能理解 request semantics 的层次
- 能看懂 app-server 主线、listener 主线和 TUI over app-server 的关系

## 当前优先吸收的旧材料

- `00-guidebook/03-app-server与thread-turn主线.md`
- `00-guidebook/14-ServerRequestResolved覆盖面与未迁移疑点.md`
- `03-boundary-judgments/2026-04-12-app-server-request-shape分类与收口.md`
- `03-boundary-judgments/2026-04-12-DynamicToolCall为什么不走ServerRequestResolved.md`
- `02-call-chain-drafts/2026-04-11-codex主链草图-interactive-TUI链.md`
- `01-source-notes/09 / 10 / 15 / 16 / 20 / 23 / 32 / 33 / 36 / 37 / 39 / 40 / 41 / 44 / 45 / 48`

## 这一卷不急着展开什么

- 不深入 unified-exec 内部生命周期
- 不扩展到 skills/plugins 平台层
- 不提前讲 guardian / realtime / collab

---

# 卷四｜统一执行子系统怎样把动作变成可管理会话

## 这一卷回答什么

回答：**Codex 在真正执行动作时，为什么不是一个 shell wrapper，而是一套 sessionful execution subsystem。**

## 为什么这一卷重要

如果卷三讲“runtime 怎样被暴露和控制”，卷四讲的就是：

> **真正动作发生时，Codex 怎样把一次执行装成一个可批准、可流式观察、可追踪、可对账的运行会话。**

这是当前最容易长成“高质量主线卷”的部分。

## 前置知识

- 默认已经读完卷一到卷三
- 至少知道 runtime / control plane / thread 已成立

## 建议阅读顺序（草案）

1. `exec.rs` 和 unified-exec 为什么不是一回事
2. `UnifiedExecHandler` 怎样把请求装成执行会话
3. approval / sandbox / policy 为什么在执行前进入主链
4. output 为什么先进入 transcript
5. process store 为什么不是最终权威源
6. unified-exec 为什么更像 execution control plane

## 读完这一卷你会得到什么

- 能分清 primitive exec 和 unified-exec subsystem
- 能看懂执行请求的装配、运行、流式输出、终态封装、状态对账
- 能理解 transcript / process store / end event 三者的关系

## 当前优先吸收的旧材料

- `00-guidebook/05-unified-exec执行子系统.md`
- `02-call-chain-drafts/2026-04-11-codex主链草图-exec链.md`
- `01-source-notes/08 / 12 / 25 / 27 / 35 / 38 / 42 / 46 / 49 / 52 / 53 / 54 / 55 / 56 / 57`

## 这一卷不急着展开什么

- 不把 review / guardian 混进执行主线
- 不把 plugin / MCP 混进执行主线
- 不把全文写成函数逐行解释

---

# 卷五｜能力系统怎样把 Codex 长成平台

## 这一卷回答什么

回答：**Codex 怎样把 skills、MCP、plugins、apps、connectors 这些能力入口组织成平台化结构。**

## 为什么这一卷重要

前四卷讲的是系统主体、状态、控制面、执行面；卷五开始回答：

> **Codex 为什么不只是一个会跑的 agent，而是一套会长能力、会长接口、会长打包层的平台。**

## 前置知识

- 默认已经读完卷一到卷四
- 至少知道 control plane 和 execution subsystem 已立住

## 建议阅读顺序（草案）

1. skills / hooks / MCP 为什么不能混成一类
2. plugin 为什么是更高层打包单元
3. `rmcp-client` 与 `mcp-server` 各在什么位置
4. apps / connectors 怎样组成复合能力面
5. Codex 为什么已经长出平台层雏形

## 读完这一卷你会得到什么

- 能分清能力接入层和平台打包层
- 能理解 plugin、skills、MCP、apps、connectors 的层级关系
- 不会把 capability system 写成“扩展功能目录”

## 当前优先吸收的旧材料

- `00-guidebook/06-capability与高级子系统.md` 中 capability 部分
- `00-guidebook/07-model-client与provider请求主链.md`（暂作后置专题参考）
- `00-guidebook/08-codex-client-codex-api与backend-client分层.md`（暂作后置专题参考）
- `01-source-notes/07 / 13 / 14 / 19 / 21 / 26 / 30 / 31`

## 这一卷不急着展开什么

- 不把 guardian / realtime / collab 强塞进平台层
- 不把 model transport 直接当正文主轴
- 不把附录性质内容提到卷级主链里

---

# 卷六｜审查、协作与高级 runtime 为什么不能当作边角功能

## 这一卷回答什么

回答：**review、guardian、realtime、collab、memories、agents 这些看起来像“高级功能”的东西，为什么其实代表 Codex 正在长出更高层的运行组织能力。**

## 为什么这一卷重要

如果前五卷已经把主体、状态、控制面、执行面、能力面都立住了，最后这卷就负责回答：

> **Codex 怎样从一个单线程 coding agent 工具，继续长成审查基础设施、协作 runtime 和更高层控制组织结构。**

## 前置知识

- 默认已经读完前五卷
- 至少知道 capability system 与 unified-exec 已立住

## 建议阅读顺序（草案）

1. `/review` 和 guardian 为什么不是一回事
2. guardian 为什么像审查基础设施
3. realtime / collab / AgentControl 分别是什么层
4. memories 为什么更像启动管线
5. Codex 怎样从单线程 agent 工具长成更高阶 runtime

## 读完这一卷你会得到什么

- 能分清 review workflow 和 guardian infrastructure
- 能看懂 realtime、collab、AgentControl、memories 这些高级系统之间不是一层东西
- 会把它们看成 Codex 后半段“运行组织能力”的自然外延

## 当前优先吸收的旧材料

- `00-guidebook/09-review工作流与guardian审查基础设施.md`
- `00-guidebook/10-realtime-collab与memory迁移专题.md`
- `00-guidebook/15-guardian-analytics为何还没真正接上.md`
- `01-source-notes/22 / 24 / 28 / 29`

## 这一卷不急着展开什么

- 不再回头重讲系统主体边界
- 不再回头细拆 unified-exec 执行链
- 不把能力平台层再讲一遍

---

# 当前最重要的顺序结论

如果按 Claude Code 那套标准继续推进，下一步最值的顺序应该是：

1. **先把这 6 卷 index 定住**
2. **再为每卷拆 `README-writing-cards`**
3. **最后才开始逐篇迁移与重写**

也就是说：

> **先定卷级入口，再定篇级卡片，再动正文。**
