---
title: Signal Repo Phase 1 Brainstorm
date: 2026-03-28
tags:
  - signal-repo
  - product-architecture
  - github-watch
  - phase1
status: draft
---

# Signal Repo / Phase 1 讨论整理

## 这套系统是什么
当前共识：这不是一个单纯的“日报生成器”或“仓库收藏夹”，而是一个以 **signal 为中心** 的情报系统。

更准确地说，它是一个：

- 以 **lane / route / channel** 为第一公民的多路召回系统
- 底层尽量保留原始 signal 和证据
- 上层再逐步形成候选项、日报项、长期观察对象
- 关键决策点保持 **半自动**，而不是全自动

一句话版本：

> 日报只是下游产物之一；系统本体是“按 lane 组织 signal、保留证据、支持人工判断”的情报底座。

---

## 已对齐的系统原则

### 1. lane-first
系统的第一公民更接近：

- lane / route / channel
- signal

而不是：

- repo
- user
- run
- 日报 item

原因：同样是 GitHub，也可能存在不同任务意图，例如：

- GitHub 长期跟踪
- GitHub 新项目发现
- GitHub release 监听
- GitHub 文档/README 变化追踪

lane 的价值在于：先定义“为什么收这路信息、这路信息如何处理”，再去接对象和产物。

### 2. signal-first
repo 不是唯一主角，repo 只是 signal 的重要来源和关联对象之一。

更准确的关系是：

- 外部事件/页面/文档变化 → signal
- signal 关联 object（例如 repo）
- 少量 object 升级为长期 watchlist

### 3. 保留原始证据
用户不希望底层只有被加工后的结论，希望能回到：

- release 原文
- changelog 原文 / 关键段落
- README 快照或 diff
- 源链接和时间戳

### 4. 前台弱分区，后台强标签
对外产物不一定要重分区，但系统内部必须保留：

- lane
- source type
- repo/object 关联
- 采集时间
- 原始证据位置

### 5. 半自动
长期观察对象的升级/降级、最终产物选择等，不应全自动执行。
系统先提出建议，人再拍板。

---

## Phase 1 的目标
当前明确选择的是 **最小验证版**。

### 核心命题
要先验证的是：

> 先把信息组织成 lane + signal，这个骨架本身是否成立。

并且第一版：

- **candidate 可以先不要**
- 重点不是自动判断，而是先验证 signal 模型和组织方式是否顺手

### 成功标准
用户确认希望 Phase 1 同时满足：

1. 不同来源的 signal 能稳定地按 lane 收进来
2. 原始证据不丢
3. 能方便回看某一路最近收到了什么
4. 能基于这套结构继续做人工判断

所以 Phase 1 不是“纯采集 demo”，而是一个：

> 可采、可看、可供人工继续决策的最小情报底座。

---

## Phase 1 的 lane 范围
当前收得非常窄：

- 只做 **1 条 lane**
- 名字可暂定为：`github-watch`

这条 lane 的目标不是做全 GitHub 发现，而是只服务于：

> 对既有长期关注 repo 的稳定跟踪

watch 对象来源共识：

- 先从现有核心 watchlist 导入
- 同时允许手工增删

---

## Phase 1 的 signal 范围
当前用户先选了：

- release
- changelog
- README 变化

但这三者在系统里的角色仍需继续收敛。

### README
当前共识：

- README 是“当前状态说明”，不是变化账本
- 但 README 的变化本身可以作为 signal
- 第一版先不自动判断“是否关键变化”
- 只要 README 内容和上次快照不同，就记一条 signal
- 先保存 diff / 快照证据，重要性留给人工判断

### changelog
当前最新对齐：

- changelog 不等于 README
- changelog 也不完全等于 release notes
- changelog 更像“项目维护者记录变化的连续账本”

一句话版本：

> README 讲“我是谁”，release 讲“我发了”，changelog 讲“我到底改了什么”。

对 changelog 的系统理解：

- 它比 README 更适合作为变化证据
- 它比 release 更连续，但缺少统一 API 和标准结构
- 它适合做变化证据层，或在 release 缺位时作为变化来源

在这轮对齐里，用户先选择：

> changelog 在第一版里更像 **和 release 并列的一种独立 signal**。

这意味着 Phase 1 暂不把 changelog 仅仅视为 release 附件，而是认真把它当作 repo 变化来源之一。

---

## 文件与存储形态
用户明确要求：**必须文件优先**。

原因：

- 人可直接读
- AI 可直接检索和消费
- 更容易审计、迁移、重组

### 时间组织单位
- 主单位：**按天组织**

### 目录粒度
选择的是：

- 一天一个索引文件
- 多个独立 signal 文件
- 原始证据单独保存

可参考结构：

```text
signals/
  github-watch/
    2026-03-28/
      index.md
      signals/
        2026-03-28T08-30-12-anthropics-claude-code-release-v2.1.86.md
        2026-03-28T08-45-03-openclaw-openclaw-readme-change.md
        2026-03-28T09-10-20-sst-opencode-changelog-update.md
      evidence/
        anthropics-claude-code-release-v2.1.86.md
        openclaw-openclaw-readme.before.md
        openclaw-openclaw-readme.after.md
        sst-opencode-changelog.before.md
        sst-opencode-changelog.after.md
```

### signal 文件格式
选择的是：

- **Markdown**
- 开头是元数据块
- 后面是正文内容
- 风格类似 skill.md：结构化头部 + human-readable body

### 每日索引文件
选择的是：

- **目录 + 极简摘要**

至少包含：

- 今日有哪些 signal
- 每条 signal 链接
- repo 名
- signal 类型
- 一句很短的变化概述

---

## signal 的唯一性
当前共识：

> 一次 source event 就是一条独立 signal。

也就是：

- 一个 release = 一条 signal
- 一次 README 变化 = 一条 signal
- 一次 changelog 新增/更新 = 一条 signal

第一版先不做“同 repo 同天合并”。
底层先保真、先简单，浏览层以后再考虑轻度聚合。

---

## 从旧 GitHub Daily 系统得到的启发
这轮讨论还回看了之前的 GitHub AI Daily 实现，得到几个关键结论：

### 1. 旧系统本质上是 release-first
之前的 GitHub Daily 实现，对核心仓库主要依赖：

- `gh release view`
- release body
- 从 release body 中抽 bullet
- 再做 repo-specific 中文总结

这说明旧系统本质上不是“通用 repo signal 仓”，而是：

> 以日报产物为目标的 release-first 方案。

### 2. 不是所有 repo 都是 release-driven
已经明确踩过的坑包括：

- 有些 repo 的主要变化在 README / docs
- 有些 repo 更像 manifest/marketplace 驱动
- 有些 repo 不稳定发 release
- 有些 repo（如当时评估的 openclaw/skills）太重，难以稳定追踪

### 3. release 有了，不等于“更新点可直接用”
旧系统后面仍然要写 repo-specific summarizer，说明：

- release body 往往太长、太杂、太英文
- 原始 source 到“中文高信号结论”之间还有较重的加工层

### 4. README / changelog 是旧系统没有真正优雅处理的部分
这也是当前想单独做 signal repo / phase1 的根本原因之一。

---

## 最开始参考的那篇文档：Git scraping
这轮最早、也最有启发的外部材料，是 Simon Willison 的文章：

- **Git scraping: track changes over time by scraping to a Git repository**
- 链接：<https://simonwillison.net/2020/Oct/9/git-scraping/>

这篇文章的核心思路不是“做通知系统”，而是：

1. 有些信息的价值不只在当前状态，而在于“它怎么变了”
2. Git 本身就是很适合保存文本变化历史的工具
3. 用定时任务（例如 GitHub Actions）周期性抓取某个资源
4. 对抓到的结果做适度规范化（如 pretty-print JSON）以提高 diff 可读性
5. 只有内容变化时才提交到 Git
6. 最终得到的是一个可回放、可比较、可审计的变化历史仓库

这篇文档对当前 signal repo 设计最大的启发有三点：

### 启发 1：变化历史本身就是资产
系统不应该只保留“今天的结论”，而应该保留“它相对昨天变了什么”。

### 启发 2：文件 + Git 历史是成立的底座
不一定一上来就需要数据库或复杂事件系统；只要组织得好，文件仓 + Git 就足以承载第一版的变化追踪。

### 启发 3：README / changelog 这类文本对象非常适合做 snapshot-diff
相比 release 这种天然事件，README 和 changelog 更像“状态文本”；它们特别适合用“抓快照 → 对比变化 → 留存证据”的方式建模。

这也是为什么当前 Phase 1 会收敛到：

- 文件优先
- 高保真保留证据
- README 变化可直接作为 signal
- changelog 倾向被认真看作独立变化来源

换句话说，这篇文档给当前系统提供的不是具体 schema，而是一种更底层的方法论：

> 对于文本型信号，最先要做的不是抽象成漂亮结论，而是先把“可比较的历史”保存下来。

---

## 外部参考：其他人通常怎么做
这轮还补看了一些外部做法，发现大致分成三派：

### 1. release 通知派
代表：

- NewReleases.io
- Release Alert

特点：

- 盯 release/tag/package version
- 目标是通知“发版了”
- 稳定，但对 README / 定位变化无能为力

### 2. git-scraping / snapshot-diff 派
代表：

- Simon Willison 的 git scraping 方法

特点：

- 定时抓取某个文本资源
- 规范化后存入 git
- 只要内容变化就形成新历史
- 天然适合 README / docs / 页面类信号

### 3. dependency / ecosystem-aware 派
代表：

- Dependabot / dependabot-core / dependabot CLI

特点：

- 围绕依赖、版本、manifest、release notes 运作
- changelog / release notes 更像升级说明的证据层

这轮外部参考给出的最大启发是：

> release-first 和 snapshot-diff 都是成熟路线；但“把 release、README、changelog 一起纳入一个 lane-first、文件优先的 signal 仓”这件事，反而较少看到现成模板。

这说明当前方向有独特价值，但第一版必须刻意保持最小。

---

## 当前仍待继续问清的问题
虽然已经收得很窄，但仍有几个设计点需要继续拍板：

1. changelog 在 Phase 1 里虽然先选成独立 signal，但其判定口径还要继续细化
   - 是“文件变了就算”
   - 还是“新增了新版本段/新条目才算”
2. release / changelog / README 三类 signal 的优先级与相互去重关系
3. signal 元数据块的最小 schema
4. `github-watch` 每日索引文件的精确模板
5. 后续是否需要专门的 object 视图（当前用户更重视 lane 时间线）

---

## 当前阶段的简化判断
截至 2026-03-28 这轮讨论，可以把当前阶段定义为：

> 先做一个文件优先、按天组织、lane-first 的 GitHub watch signal 仓；只跟踪已有 watchlist repo，先把 release / changelog / README 变化稳定记录下来，并保留足够高保真的证据，供后续人工浏览和判断。

这不是最终系统，而是验证这套骨架是否顺手的第一步。
