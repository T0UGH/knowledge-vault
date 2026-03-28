---
title: GitHub Trending Weekly Phase 1 Design
date: 2026-03-29
tags:
  - daily-lane
  - github
  - trending
  - lane
  - phase1
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# GitHub Trending Weekly / Phase 1 Design

这份笔记用于承接 2026-03-28~2026-03-29 对第四条 lane 的讨论结果。原本这条 lane 想收成更泛的 `github-discovery` 或 `github-star-growth`，但在实际讨论后，为了保持第一版边界清晰、实现稳定且不假装拥有严格的 7d star growth 口径，最终收成：

> **`github-trending-weekly`**

也就是：第一版只围绕 GitHub Trending Weekly 总榜工作。

---

## 一、定位

`github-trending-weekly` 是一条：

> **榜单暴露记录型、stateless 的 lane。**

它的第一价值不是“生成值得推荐的 repo 候选”，而是：

> **收集本周出现在 GitHub Trending Weekly 上的 repo 信号。**

也就是说：

- 先召回
- 后筛选
- 筛选不在这一层做

因此，这条 lane 不是 candidate 层，也不是推荐层，而是“榜单暴露记录层”。

---

## 二、为什么不是严格 star growth

讨论中曾经想把这条 lane 收成“7 天内 star 增长显著的 repo”。但在实际评估后，确认：

1. GitHub 没有一个足够标准、官方、稳定、可长期依赖的“7d star growth 榜单 API”可直接作为底座。
2. 第三方 newsletter / curated 源（例如 Star History 的 RSS）虽然可用，但更像精选/编辑型内容，不适合当作这条 lane 的第一版主来源。
3. 如果不自己维护 baseline / snapshot，就不应把第一版命名成严格的 `star-growth`，否则语义会虚。

因此，第一版应诚实收成：

> **GitHub Trending Weekly**

它不是严格的 star growth 榜，但可以作为“本周热起来的 repo 信号”的稳定近似来源。

---

## 三、signal 单元

第一版明确采用：

> **一个 repo = 一条 signal**

也就是说：

- 不把整个 weekly trending 榜单做成一条 batch signal
- 也不把“repo × 复杂上下文”预先膨胀成多条 signal
- 只要某个 repo 出现在本次 weekly trending 抓取里，就生成一条 signal

这个粒度与前面几条 lane 保持一致：

- 一个对象的一次出现记录 = 一条 signal

---

## 四、signal 语义

这条 lane 的 signal 不是裸 repo 卡片，而是：

> **某个 repo 在 GitHub Trending Weekly 上出现了一次。**

因此，repo 本身不是唯一内容，signal 里还应保留它的 trending 上下文。

至少包括：

- `since=weekly`
- 排名 / 位置（如果页面稳定可得）
- 语言（如果页面稳定可得）
- trending 页面展示的 stars / description / 其他上下文

这些信息不是装饰，而是这条 lane 的本体证据。

---

## 五、状态策略

第一版明确采用：

> **stateless**

也就是：

- 不做 `state/`
- 不记录上周榜单快照用于 diff
- 不做“新上榜 / 掉榜 / 排名变化”判断
- 不做榜单比较系统

当前只记录：

> **这次 weekly trending 上出现了什么。**

榜单变化分析留给后续层，而不是第一版 lane。

---

## 六、正文策略

这条 lane 的正文采用：

> **结构化正文 + 至少拉 README**

原因很简单：

- trending 页面本身只能告诉你“它上榜了”
- 但很多 repo 只靠榜单描述并不足以看懂它到底是干什么的
- README 是理解 repo 的最低必要证据

因此，第一版 signal 不应只保留：

- repo 名
- 一行 description
- 榜单位置

而应至少补充：

- README 内容（或 README-based 结构化证据）

这样 signal 才真正可读。

### README 的角色

在这条 lane 里，README 不是独立 signal，而是：

> **当前 trending signal 的正文证据组成部分。**

也就是说：

- 这条 signal 仍然是“trending signal”
- README 用于帮助理解 repo 是什么
- 它不表达“README 发生变化”这个独立事件

---

## 七、语言策略

第一版明确：

> **不分语言**

也就是：

- 只抓 GitHub Trending Weekly 总榜
- 不做 `weekly × 多语言` 的并行召回
- 不扩成多个榜单维度

原因是第一版最重要的是先把：

- 来源单一
- 边界清楚
- 信号粒度稳定

这三件事立住。

---

## 八、一句话版结论

截至 2026-03-29，第四条 lane 的 Phase 1 最终收成：

> **`github-trending-weekly`** —— 一条榜单暴露记录型、stateless 的 lane；围绕 GitHub Trending Weekly 总榜，将每个上榜 repo 记录为一条 signal，并在 signal 中保留 weekly trending 上下文，同时至少补充 README 作为正文证据，使其成为可读、可检视的 repo 信号记录；不做榜单 diff、不做新上榜/掉榜分析、不做语言分路，也不把这条 lane 直接做成 candidate / 推荐层。
