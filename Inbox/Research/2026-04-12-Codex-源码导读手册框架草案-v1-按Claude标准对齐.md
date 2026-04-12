---
title: Codex 源码导读手册框架草案 v1（按 Claude Code 标准对齐）
date: 2026-04-12
status: draft
tags:
  - Codex
  - guidebook
  - source-reading
  - Claude-Code-源码导读
---

# Codex 源码导读手册框架草案 v1（按 Claude Code 标准对齐）

## 这版草案和上一版的区别

这版不是泛泛地“参考 `claude-code-source-guide` 的风格”，而是**明确对齐 Obsidian 里 Claude Code 当时的成书标准**后，重出的 Codex 框架。

这次对齐的不是卷数，而是那套已经验证有效的方法：

1. **先把写作顺序重组为阅读顺序**
2. **先补总入口 / 阅读路径 / 术语页 / 映射表 / 并入规则**
3. **每卷先有导读和主问题，再拆正文**
4. **旧材料保留，但退居素材层 / 证据层**
5. **正文轻修，不先大规模重写**
6. **允许少量桥接页，但不允许无限加专题把主线打裂**

换句话说，这版 Codex 草案不是“先想卷名”，而是：

> **先把 Codex 从研究仓提升成可公开阅读的手册项目。**

---

# 一、先把目标定死：这次做的是“手册项目”，不是“继续整理笔记”

按照 Claude Code 那套标准，Codex 下一阶段的目标不该是：

- 继续往 `01-source-notes/` 里补更多笔记
- 继续把 `00-guidebook/` 修得更顺一点
- 继续加几个 topic / appendix / micro-gap

而应该切换成：

> **把现有 `codex-source-reading` 从 Phase 1 研究仓，重组为一套可公开阅读的 Codex 源码导读手册。**

这意味着后续工作的优先级应该变成：

1. 建立正式阅读入口
2. 建立卷级主线
3. 建立旧材料到新结构的映射关系
4. 建立新材料并入规则
5. 再开始逐卷逐篇迁移

也就是说：

> **先把内容骨架做对。**

这正是 Claude Code 当时从 `00-90` 过渡到 guidebook 形态时最核心的动作。

---

# 二、Codex 这边也要先补一套“写书控制层”

按照 Claude Code 的标准，Codex 不应该直接从卷正文开始，而应该先补这些控制层页面。

## 必须先有的控制层文件

### 1. 手册总入口
建议位置：
- `guidebookv2/README.md` 或等价正式入口

作用：
- 告诉新读者这是什么项目
- 说明适合谁读
- 给出推荐入口
- 展示整本书的卷级结构

### 2. 旧材料 → 新结构映射表
建议位置：
- `guidebookv2/00-旧结构到新结构映射表.md`

作用：
- 把 `00-guidebook/`、`01-source-notes/`、topic、appendix、micro-gap 全部映射进新的卷级结构
- 明确哪些是：
  - 正文主链
  - 桥接页素材
  - 附录/证据层素材
  - 保留但退出主阅读链的旧文

### 3. 阅读路径页
建议位置：
- `guidebookv2/01-最短阅读路径与完整阅读路径.md`

作用：
- 降低新读者进入门槛
- 提供最短路径 / 标准路径 / 完整路径

### 4. 术语页
建议位置：
- `guidebookv2/02-术语总表-第一版.md`

至少要覆盖：
- thread
- turn
- rollout
- app-server
- unified-exec
- transcript
- process store
- plugin
- skill
- MCP
- review
- guardian
- realtime
- collab
- AgentControl

### 5. 新材料并入规则页
建议位置：
- `guidebookv2/03-后续新文章怎么并入手册结构.md`

作用：
- 防止后续继续把结构打裂
- 定死：
  - 新文章先归卷
  - 先更新映射表
  - 先更新卷导读 / 卷目录
  - 再决定是否进入主阅读链

---

# 三、Codex 的正文主线建议改成“6 卷结构”

这不是照搬 Claude Code 的 6/7 卷，而是按 Codex 自己最自然的认知坡度来组织。

---

## 卷一｜Codex 到底是什么系统

### 这一卷回答什么
回答：**Codex 到底是什么形态的系统，真正主体在哪，哪些只是分发壳 / 入口壳 / 前端壳。**

### 这一卷为什么必须先读
因为 Codex 最容易被误读成：
- 一个 npm CLI
- 一个 TUI 工具
- 一个 app-server
- 一个“所有模块都差不多重要”的大仓库

卷一的任务就是先建立“主体边界感”。

### 读完这一卷应该得到的判断
- `codex-cli/` 只是 distribution shell
- `codex-rs/cli` 是统一入口层
- `tui` 是交互前端，不是 runtime owner
- `core` 是 runtime aggregation center
- `ThreadManager` 比 app-server 更接近 runtime owner
- app-server / mcp-server / remote app-server 是不同 exposure layer

### 主要吸收现有材料
- `00-guidebook/00-如何阅读这份导读.md`
- `00-guidebook/01-系统总图与分层.md`
- `03-boundary-judgments/2026-04-11-codex模块边界判断-v1.md`
- `00-index/01-代码路径索引-入口与分发.md`
- `01-source-notes/01~05`

### 卷内建议篇章方向
1. Codex 为什么不是一个单纯 CLI 工具
2. 从分发壳到 Rust runtime：主体到底在哪
3. TUI、app-server、mcp-server 各是什么
4. 为什么 `ThreadManager` 更像 runtime owner

---

## 卷二｜线程、状态与恢复是怎么成立的

### 这一卷回答什么
回答：**Codex 为什么不是“一次命令执行就结束”，而是一个可以持续、恢复、继续运行的线程化系统。**

### 这一卷为什么排第二
卷一回答“这是什么系统”，卷二就该回答“它为什么能持续存在”。

### 读完这一卷应该得到的判断
- rollout 是 durable truth source
- SQLite 更像 metadata / index / sidecar
- recovery 主要靠 replay
- thread 是真正的会话承载单元
- turn 是 runtime 轮次，不是简单文本轮次
- turn-history 是语义投影层，不是 event log 镜像

### 主要吸收现有材料
- `00-guidebook/02-状态持久化与恢复.md`
- `00-guidebook/04-turn-history语义层.md`
- `01-source-notes/06 / 11 / 17 / 18 / 34 / 43 / 47 / 50 / 51 / 58`

### 卷内建议篇章方向
1. rollout 为什么才是正文真相源
2. SQLite state 为什么不是恢复核心
3. thread / turn / pending request 是怎么形成的
4. recovery 为什么更像 replay
5. turn-history 为什么不是 event log 镜像

---

## 卷三｜Codex 的控制面怎么把 runtime 暴露出来

### 这一卷回答什么
回答：**thread runtime 成立之后，Codex 怎样把它稳定投影成 TUI、SDK、远端调用者可观察、可控制、可恢复连接的控制面。**

### 为什么它要单独成卷
这是 Codex 相比很多 coding agent 最不该被轻视的一层：
- app-server 很厚
- listener / protocol / request semantics 很关键
- TUI 越来越像跑在 app-server 之上

### 读完这一卷应该得到的判断
- app-server 是 control-plane facade，不是平行 runtime
- listener task 是 thread-event pump
- `bespoke_event_handling` 是协议投影层
- `thread_state_manager` / `thread_watch_manager` 是有意拆分
- callback-map resolution 和 `ServerRequestResolved` 不是同一语义层
- `DynamicToolCall` 走的是 item lifecycle，不是 resolved 语义

### 主要吸收现有材料
- `00-guidebook/03-app-server与thread-turn主线.md`
- `00-guidebook/14-ServerRequestResolved覆盖面与未迁移疑点.md`
- `03-boundary-judgments/2026-04-12-app-server-request-shape分类与收口.md`
- `03-boundary-judgments/2026-04-12-DynamicToolCall为什么不走ServerRequestResolved.md`
- `01-source-notes/10 / 15 / 16 / 32 / 33 / 36 / 39 / 40 / 44 / 45 / 48`

### 卷内建议篇章方向
1. app-server 为什么不是另一套 runtime
2. listener / projection / thread state 怎么闭环
3. `ServerRequestResolved` 到底覆盖了什么
4. `DynamicToolCall` 为什么不属于同一 resolved 模型
5. 为什么 Codex 的控制面不是薄 API 层

---

## 卷四｜统一执行子系统怎样把动作变成可管理会话

### 这一卷回答什么
回答：**Codex 在真正执行动作时，为什么不是 shell wrapper，而是一套 sessionful execution subsystem。**

### 为什么它必须独立成卷
Codex 当前所有材料里，最适合直接长成“Claude Code 卷三级别”主线的就是 unified-exec。

### 读完这一卷应该得到的判断
- `exec.rs` 是 primitive layer
- unified-exec 是 sessionful, agent-facing execution subsystem
- `UnifiedExecHandler` 是入口装配线
- `UnifiedExecRuntime` 是 approval/policy/run 的交汇点
- transcript 比 process store 更接近最终输出真相
- end event 是统一终态语义
- process store 需要持续对账

### 主要吸收现有材料
- `00-guidebook/05-unified-exec执行子系统.md`
- `02-call-chain-drafts/2026-04-11-codex主链草图-exec链.md`
- `01-source-notes/12 / 25 / 27 / 35 / 38 / 42 / 46 / 49 / 52 / 53 / 54 / 55 / 56 / 57`

### 卷内建议篇章方向
1. `exec.rs` 和 unified-exec 为什么不是一回事
2. `UnifiedExecHandler` 怎样把请求装成执行会话
3. approval / sandbox / policy 为什么在执行前进入主链
4. output 为什么先进入 transcript
5. process store 为什么不是最终权威源
6. unified-exec 为什么更像 execution control plane

---

## 卷五｜能力系统怎样把 Codex 长成平台

### 这一卷回答什么
回答：**Codex 怎样把 skills、MCP、plugins、apps、connectors 这些能力入口组织成平台化结构。**

### 为什么它是独立一卷
因为这些东西如果不单独收卷，很容易被写成“扩展功能总览”，失去层次感。

### 读完这一卷应该得到的判断
- skills / hooks / MCP / plugins / apps / connectors 不是同一层概念
- plugin 是更高层 capability packaging unit
- `rmcp-client` 和 `mcp-server` 是不同方向的线
- apps/connectors 是复合 capability surface

### 主要吸收现有材料
- `00-guidebook/06-capability与高级子系统.md` 的 capability 部分
- `01-source-notes/07 / 13 / 14 / 19 / 21 / 26 / 30`

### 卷内建议篇章方向
1. skills / hooks / MCP 为什么不能混成一类
2. plugin 为什么是更高层打包单元
3. `rmcp-client` 与 `mcp-server` 各在什么位置
4. apps / connectors 怎样组成复合能力面
5. Codex 为什么已经长出平台层雏形

---

## 卷六｜审查、协作与高级 runtime 为什么不能当作边角功能

### 这一卷回答什么
回答：**review、guardian、realtime、collab、memories、agents 这些看起来像“高级功能”的东西，为什么其实代表 Codex 正在长出更高层的运行组织能力。**

### 为什么把它放最后
因为这是全书的后半段收口：
- 当前 runtime 已经成立
- 控制面成立
- 执行面成立
- 能力面成立
- 接下来系统怎样进一步长出组织能力

### 读完这一卷应该得到的判断
- `/review` 和 guardian 是两套系统
- guardian protocol/UI 已可观察，但 analytics emission 未接上
- realtime 和 collab 不是一回事
- `AgentControl` 是 rooted thread-tree multi-agent control plane
- memories 是 startup pipeline，不是普通 turn helper
- external-agent-config 更像迁移基础设施

### 主要吸收现有材料
- `00-guidebook/09-review工作流与guardian审查基础设施.md`
- `00-guidebook/10-realtime-collab与memory迁移专题.md`
- `00-guidebook/15-guardian-analytics为何还没真正接上.md`
- `01-source-notes/22 / 24 / 28 / 29`

### 卷内建议篇章方向
1. `/review` 和 guardian 为什么不是一回事
2. guardian 为什么像审查基础设施
3. realtime / collab / AgentControl 分别是什么层
4. memories 为什么更像启动管线
5. Codex 怎样从单线程 agent 工具长成更高阶 runtime

---

# 四、旧材料要怎么处理：按 Claude Code 标准，必须“退居映射层”

Claude Code 当时很关键的一步，不是删除旧文，而是：

> **旧编号保留，但退居映射层；新结构成为主阅读链。**

Codex 这边也应该这样做。

## 当前建议

### 1. `00-guidebook/` 不删
但它不该继续被直接当成未来公开书稿，而应该视为：
- 旧正文层
- 过渡正文层
- 高密度内部 guidebook

### 2. `01-source-notes/` 保留为证据层
作用：
- 文章源码锚点
- 附录与函数索引来源
- 微专题回证据入口
- future agents 的高密度上下文仓

### 3. topic / appendix / micro-gap 继续保留
但它们要正式退出主阅读链，转成：
- 专题层
- 附录层
- 微缺口补洞层

而不是再和卷级主线并列。

---

# 五、从 Claude Code 标准倒推，Codex 现在最该补的产物清单

如果按 Claude Code 那套真正有效的成书标准执行，Codex 下一阶段最值得先补的不是正文，而是这几类文件：

## 第一批：控制层文件
1. `guidebookv2/README.md`
2. `guidebookv2/00-旧结构到新结构映射表.md`
3. `guidebookv2/01-最短阅读路径与完整阅读路径.md`
4. `guidebookv2/02-术语总表-第一版.md`
5. `guidebookv2/03-后续新文章怎么并入手册结构.md`

## 第二批：卷级导读页
- `guidebookv2/volume-1/index.md`
- `guidebookv2/volume-2/index.md`
- `guidebookv2/volume-3/index.md`
- `guidebookv2/volume-4/index.md`
- `guidebookv2/volume-5/index.md`
- `guidebookv2/volume-6/index.md`

每卷都要明确：
- 这一卷讲什么
- 为什么重要
- 前置知识
- 建议阅读顺序
- 读完你会得到什么

## 第三批：写作控制文档
每卷都要有：
- `README-writing-cards.md`
- `README-production-order.md`

这是 Codex 目前最缺的部分。

---

# 六、这一版的真正收口

如果只留一句话，这版按 Claude Code 标准对齐后的框架可以收成：

> **Codex 不该继续沿现有 `00-guidebook/Chapter+Topic+Appendix+Micro-gap` 结构往前修，而应该像 Claude Code 当时那样，先建立总入口、阅读路径、术语页、映射表、并入规则和卷级导读，再把现有高密度 guidebook 与 source notes 重新投影成 6 卷主线、专题层和证据层。**

再压缩一点：

> **先长写书控制层，再重组卷级主线，最后才写正文。**

---

# 七、下一步建议

如果这版方向对，后面最值的动作顺序应该是：

1. **先做旧材料 → 新卷结构映射表**
2. **再定 6 卷各自的 `index`**
3. **再为每卷拆 `README-writing-cards`**
4. **最后才开始逐篇迁移与重写**

也就是说：

> **先定骨架，再定目录，再定卡片，最后写书。**
