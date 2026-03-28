---
title: X Feed Phase 1 Design
date: 2026-03-28
tags:
  - daily-lane
  - x
  - feed
  - lane
  - phase1
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# X Feed / Phase 1 Design

这份笔记用于承接 2026-03-28 在 daily-lane 上对 `x-feed` 这条 lane 的设计讨论，目标是把它收成一个可以继续写 spec / implementation plan 的 Phase 1 版本。

---

## 一、定位

`x-feed` 是一条：

> **个人 feed 暴露记录型、stateless 的 lane。**

它的第一价值不是“发现值得注意的内容”，而是：

> **我在 feed 里实际刷到的内容归档层。**

这意味着：

- 先召回
- 再筛选
- 筛选不是这一层做的

也因此，`x-feed` 不应被做成推荐层、候选层或发现层。它是 feed 暴露的原始记录层。

---

## 二、signal 单元

第一版明确采用：

> **一条 feed 中出现的 post = 一条 signal**

当前不采用：

- session/batch 作为 signal 单元
- 作者曝光作为 signal 单元

也就是说，signal 的最小粒度仍然是单条内容本身。

这里的 post 包括：

- original
- reply
- repost

不在 signal 层先裁掉 reply / repost，因为这条 lane 的目标就是尽量完整保留“feed 实际暴露了什么”。

---

## 三、feed 特有证据

`x-feed` 和 `x-watchlist` 最大的区别，不在 post 本身，而在：

> **这条内容是在什么 feed 场景下、以什么方式被暴露给我的。**

因此，第一版 signal 应尽量保留以下 feed 特有证据：

### 1. `feed_surface` / `feed_context`

至少要保留它出现在哪种 feed 场景，例如：

- For You
- Following
- 以及将来可能扩展到的其他 surface

### 2. 曝光顺序 / 位置

这不是推荐排序层，而是 feed 暴露本身的一部分证据。

因此 signal 中应尽量保留：

- 这条内容在本次刷流中出现的位置

### 3. session / run 锚点

由于 feed 是流式暴露，第一版仍然应为每条 signal 保留：

- `session_id`
- 或 `run_id`
- 或等价字段

用来表达：

> 这是在哪一次刷流里看到的。

### 4. 平台推荐 / 暴露原因提示

只要能稳定拿到，就应尽量保留诸如：

- because you follow ...
- liked by ...
- recommended for you ...
- trending in ...
- popular in your network ...

这类平台提示。

这些信息不是装饰，而是 `x-feed` 这条 lane 的核心原始证据之一。

---

## 四、状态策略

第一版明确为：

> **stateless lane**

也就是：

- 不做 `state/`
- 不维护 latest_seen
- 不做已见内容对比
- 不把这条 lane 做成同步器

每次刷流就是一次 session，session 本身作为原始上下文被记录，但不通过 `state/` 追踪历史。

---

## 五、正文策略

`x-feed` 的正文采用：

> **和 `x-watchlist` 类似的结构化正文**

也就是：

- 不只是纯原文
- 不做富上下文扩写
- 不为 `x-feed` 单独发明完全不同的文体

正文至少应承载：

- 原文
- 作者
- 发布时间
- post 类型
- 链接
- feed 暴露上下文
- session / 位置
- 媒体/外链（如果能稳定获取）

也就是说，`x-feed` 的特殊性主要体现在：

- metadata / 上下文字段
- 暴露证据层

而不是完全不同的正文格式。

---

## 六、一句话版结论

截至 2026-03-28，`x-feed` 的 Phase 1 可以概括为：

> 将个人 X feed 视为“暴露记录层”而不是“发现筛选层”；围绕一次次刷流 session，保留 feed 中实际出现的 posts，并将每条出现的 post 记录为一条 signal；signal 尽量保留 `feed_surface` / `feed_context`、曝光顺序、session/run 锚点与平台推荐原因提示，同时保留 original / reply / repost 三类内容，不做 `state/`、latest_seen、AI 判断与筛选。
