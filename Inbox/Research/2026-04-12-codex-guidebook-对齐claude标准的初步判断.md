---
title: Codex guidebook 对齐 Claude Code 成书标准的初步判断
date: 2026-04-12
status: draft
tags:
  - Codex
  - guidebook
  - source-reading
  - Claude-Code-源码导读
---

# Codex guidebook 对齐 Claude Code 成书标准的初步判断

## 这轮看了什么

这轮不是动手改仓库，而是先做一轮可行性判断，核心看了三组材料：

1. `~/workspace/codex-source-reading`
   - `README.md`
   - `index.md`
   - `STATUS.md`
   - `LEO_HANDOFF.md`
   - `00-guidebook/README.md`
   - `00-guidebook/00-如何阅读这份导读.md`
   - `00-guidebook/01-系统总图与分层.md`
   - `00-guidebook/05-unified-exec执行子系统.md`
   - 多篇 `01-source-notes/` 和 `03-boundary-judgments/`

2. `~/workspace/codex`
   - 真实源码仓库，用来核对 source notes 里的源码锚点是否还成立
   - 已确认关键路径如：
     - `codex-rs/cli/src/main.rs`
     - `codex-rs/core/src/tools/handlers/unified_exec.rs`
     - `codex-rs/app-server/src/lib.rs`
     - `codex-rs/core/src/state_db_bridge.rs`
     - `codex-rs/Cargo.toml`

3. `~/workspace/claude-code-source-guide`
   - 主要拿来对照当前已经跑通的 guidebookv2 成书标准
   - 重点参考：
     - `docs/guidebookv2/README.md`
     - `docs/guidebookv2/volume-3/index.md`
     - `docs/guidebookv2/volume-3/README-writing-cards.md`
     - `docs/guidebookv2/volume-3/12-why-execution-layer-must-grow-a-permission-pipeline-first.md`

---

## 先给结论

### 结论 1：这件事值得做

`codex-source-reading` 已经不是“原始笔记堆”，而是一套完成了 Phase 1 收敛的研究仓。它已经具备继续抬升为高质量导读手册的三个关键前提：

1. **证据层足够厚**
2. **边界判断已经开始稳定**
3. **真实源码仓库已经能在本地重新对锚**

所以，这不是一个要从零开始搭架子的项目，而是一个可以从“研究仓”继续抬升成“公开可读手册”的项目。

### 结论 2：现在最不缺的是材料

当前粗略统计：

- `01-source-notes/`：58 篇
- `02-call-chain-drafts/`：2 篇
- `03-boundary-judgments/`：3 篇
- `00-guidebook/`：18 篇

这些材料已经足以支撑一版成体系导读。后续真正缺的不是继续横向补料，而是：

- 卷级主问题设计
- 篇级边界切分
- 正文层与证据层的重新组织
- 对外阅读链而不是内部研究链

### 结论 3：当前仓库已经有 guidebook 雏形，但还没达到 `claude-code-source-guide` 的成书标准

当前 `codex-source-reading` 已经有：

- 导航层：`index.md`
- 正文层：`00-guidebook/`
- 证据层：`01-source-notes/`
- 调用链草图：`02-call-chain-drafts/`
- 边界判断：`03-boundary-judgments/`
- 阶段说明：`STATUS.md` / `LEO_HANDOFF.md`

说明前一个 agent 的方法论是对的：

> 先把系统总图、主链路、边界判断、函数级证据拆出来，再逐步形成 guidebook。

但它和 `claude-code-source-guide/guidebookv2` 的差距也很明显：

- 现在更像“有章节的内部工程 guidebook”
- 还不是“按认知坡度递进的整本书”

---

## 当前材料最值钱的地方

### 1. 高价值边界判断已经出现

例如这些判断，已经足够作为正式书稿的骨架句：

- `codex-cli/` 只是 distribution shell，不是 runtime 主体
- `codex-rs/cli` 是统一入口层
- `core` 是 runtime aggregation center
- `ThreadManager` 比 app-server 更接近 runtime owner
- `app-server` 是 control-plane facade，不是平行 runtime
- rollout 是 durable history truth source
- recovery 更像 replay，而不是 ORM 式重建
- `exec.rs` 是 primitive layer，`unified_exec` 是 sessionful execution subsystem
- transcript 比 process store 更接近最终语义真相

这些都不是零散观点，而是已经可以转化为卷级/篇级判断的稳定骨架。

### 2. 函数级证据层已经具备“成书后回锚”的价值

很多 source note 不是泛泛而谈，而是直接落到关键函数，例如：

- `UnifiedExecHandler::handle(...)`
- `UnifiedExecRuntime::run(...)`
- `reconstruct_history_from_rollout(...)`
- `process_chunk(...)`
- `refresh_process_state(...)`
- `ThreadHistoryBuilder::handle_event(...)`
- `active_turn_snapshot(...)`

这意味着后面做篇级文章时，不需要再重新下潜一遍才能找锚点。

### 3. guidebook 层已经开始从“目录展开”转向“系统主线展开”

`00-guidebook/README.md` 里已经明确提出：

- 不按目录树写
- 不按调研顺序写
- 正文和证据层要分离
- 正文应该先给判断，再讲主线，再给证据入口

这和 `claude-code-source-guide` 后来成熟时的工作流高度一致。

---

## 当前离 Claude Code 成书标准还差什么

### 1. 缺“卷”，只有“章”

现在 Codex 这边的组织更像：

- Chapter 0 ~ 6
- Topic 7 ~ 10
- Appendix 11 ~ 13
- Micro-topic 14 ~ 15

这适合内部知识工程和研究收束，但不适合直接作为对外公开阅读链。

与之相比，Claude Code 那边真正拉开差距的不是篇数，而是先建立了：

- 卷级主问题
- 卷与卷之间的认知递进
- 每卷内部的文章级问题链
- 每篇文章在整卷里的职责边界

Codex 这边如果要对齐这个标准，第一步必须把当前 chapter/topic/appendix 体系重新投影成卷级主线，而不是直接修现有 chapter。

### 2. 现在的正文 still 偏“大章压大面”

例如 `00-guidebook/05-unified-exec执行子系统.md` 一章已经同时在处理：

- `exec.rs` vs `unified_exec`
- handler
- runtime
- spawn 边界
- process/session 生命周期
- transcript
- end event
- store reconciliation
- sandbox / policy / environment

这很适合内部 guidebook，但如果按 `claude-code-source-guide` 的成书标准来做，问题是：

> 一章里承担的问题太多，单章认知密度过高，不利于切成公开可读的稳定问题链。

也就是说，现在更像“高质量工程综述”，还不是“高质量篇章化手册”。

### 3. 缺写书控制层

Claude Code 后来变稳，一个关键原因不是文章多，而是长出了：

- 卷级 `index.md`
- `README-writing-cards.md`
- `README-production-order.md`
- 每篇的导读 / 卷内位置 / 上一篇 / 下一篇
- 主问题 / 核心判断句 / 这篇不讲什么 / 导流关系

Codex 这边目前虽然有：

- `index.md`
- `STATUS.md`
- `LEO_HANDOFF.md`
- `00-guidebook/README.md`

但它还没有真正长出这套“写书控制面”。

因此，当前最大缺口不是证据，而是：

> **还没有一套像 guidebookv2 那样的卷级-篇级写作控制体系。**

---

## 哪些东西可以直接继承，哪些不能直接继承

### 可以直接继承的

1. **边界判断**
   - 这些是后续成书时最值钱的骨架。

2. **函数级笔记**
   - 可以直接继续作为证据层、附录、篇级源码锚点。

3. **当前 guidebook 里已经写稳的结论段**
   - 很多不是要重写，而是要重分边界、拆章、重排叙事顺序。

### 不能直接继承的

1. **现有 Chapter/Topic/Appendix 切法**
   - 适合内部 guidebook，不适合直接作为最终公开手册结构。

2. **“微缺口 / deep-dive / appendix” 作为主阅读链的一部分**
   - 这些更适合保留为补充层，而不是主线骨架。

3. **旧的绝对路径锚点**
   - 当前许多 `source_files` 还是 `/Users/wangguiping/workspace/codex/...`
   - 后续若正式推进，需要统一改成当前有效路径或 repo-relative 策略。

---

## 当前最重要的战略判断

### 判断 1：不要直接开始修现有 `00-guidebook/` 章节

如果直接修这些 chapter，大概率只是把“内部工程 guidebook”修得更顺，不会自然长成 `claude-code-source-guide` 那种对外成书状态。

### 判断 2：第一步应该先做“卷级重组”，而不是“篇级润色”

如果要正式做，建议顺序应该是：

1. 先定义这本 Codex 手册的卷级主问题
2. 再把现有 `00-guidebook/` 视为“旧正文层 / 材料层”
3. 然后建立新的：
   - 卷级 `index`
   - `README-writing-cards`
   - `README-production-order`
4. 最后才进入逐篇迁移与重写

### 判断 3：这项目最像的是“继承 Claude Code 的方法”，不是“复制 Claude Code 的卷数”

Codex 不一定要照搬 7 卷，但应该继承 Claude Code 那套最值钱的方法：

- 先认知递进，再材料归档
- 先卷级主问题，再篇级边界
- source notes 只做证据仓，不反向决定正文结构
- 一篇只回答一个问题
- 每篇都要有导读、边界、不讲什么、源码锚点、导流关系

---

## 当前阶段的最稳结论

如果只留一句话，这轮判断可以收成：

> **`codex-source-reading` 已经具备抬升为高质量 Codex 源码导读手册的全部关键前提；它现在缺的不是更多材料，而是一次按 Claude Code 成书标准进行的卷级重组与写作控制层建立。**

## 下一步建议（仅记录，不在这轮执行）

最值得做的下一步不是直接写正文，而是：

1. 为 Codex 这本书做一版“卷级框架草案”
2. 明确每卷主问题
3. 把当前 `00-guidebook/` 章节映射到新的卷级结构
4. 再决定哪些保留为正文、哪些下沉为附录/证据层
