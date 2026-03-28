---
title: RSS Lane Phase 1 Design
date: 2026-03-29
tags:
  - daily-lane
  - rss
  - newsletter
  - lane
  - phase1
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# RSS Lane / Phase 1 Design

这份笔记用于记录 2026-03-29 对新 lane 的收敛结果。当前 `daily-lane` 的召回层已经基本覆盖：

- GitHub repo 变化（`github-watch`）
- Product Hunt（`product-hunt-watch`）
- X watchlist（`x-watchlist`）
- X feed（`x-feed`）
- GitHub Trending Weekly（`github-trending-weekly`）

在这个基础上，下一步最值得补的一层，不是再加一个高噪音社交平台，而是：

> **主动订阅型、低算法噪音、适合长期积累的高密度内容层。**

因此，下一条 lane 收成：

> **`rss-lane`**

---

## 一、lane 定位

`rss-lane` 是一条：

> **订阅源召回型、RSS-first、文章级 signal lane。**

它的职责不是做推荐，也不是做阅读器产品，而是：

> **从指定 RSS/feed source 中收集新发布内容，并把每篇 entry 沉淀成一条可检视 signal。**

它服务的上层方向主要有两条：

1. **日报 pipeline**：补充结构化外部观点源
2. **知识 / 写作 pipeline**：形成待学习文章池与写作素材池

---

## 二、为什么第一版是 RSS-first

第一版不直接收成更广义的“newsletter ingestion system”，而是明确：

> **RSS-first**

原因：

1. RSS/feed 的 source 边界更稳定
2. 获取协议更统一，脚本实现更稳
3. 更适合先把 signal 协议定住
4. 很多优质 newsletter / blog 本身就提供 RSS/Atom feed，可先覆盖相当一部分高价值源

因此，Phase 1 的边界不是“所有订阅内容入口”，而是：

> **先把可稳定获取的 RSS/Atom 源跑顺。**

---

## 三、source 范围

Phase 1 使用白名单 feed，而不是开放式全网抓取。

当前建议的第一批核心 source whitelist 为：

1. Simon Willison — `https://simonwillison.net/atom/everything/`
2. Latent Space — `https://www.latent.space/feed`
3. One Useful Thing — `https://www.oneusefulthing.org/feed`
4. Interconnects — `https://www.interconnects.ai/feed`
5. OpenAI News — `https://openai.com/news/rss.xml`
6. GitHub Blog — `https://github.blog/feed/`
7. Chip Huyen — `https://huyenchip.com/feed.xml`

这些源的共同特征是：

- 与贵平当前的 AI coding / agent / workflow 观察面高度相关
- 内容密度较高
- 同时适合日报与知识/写作两条上层 pipeline

---

## 四、signal 单元

Phase 1 采用：

> **一篇 feed entry = 一条 signal**

也就是说：

- 不把整期 feed 折成一个 batch signal
- 不把 source 级别事件当作 signal 单元
- 只要某个 source 发布了一篇 entry，就形成一条 signal

这个粒度与前面其他 lane 保持一致：

- 一个对象的一次出现 / 发布事件 = 一条 signal

---

## 五、signal 语义

这条 lane 的 signal 语义不是：

- “发现了一篇值得看的文章”
- “这是一篇推荐内容”

而是更克制地定义为：

> **某个订阅源发布了一篇新内容。**

也就是说，RSS lane 只负责记录发布事实与正文证据；
至于值不值得读、值不值得进入日报或写作选题，留给后续加工层判断。

---

## 六、正文策略

Phase 1 明确采用：

> **默认抓原文全文。**

也就是：

- feed 用于发现 entry
- 但 signal 不只停留在 feed title / summary / link
- 默认继续打开原文网页，把正文抓下来作为 signal 的主体证据

### 为什么不只保留 feed 摘要

因为很多 feed：

- 只给很短摘要
- 可读性不足
- 不足以直接支持日报或知识/写作 pipeline

因此这条 lane 第一版不是纯 RSS metadata collector，而是：

> **RSS 发现 + 原文抓取的文章 signal lane。**

---

## 七、状态策略

Phase 1 明确采用：

> **stateless**

也就是：

- 不维护复杂已读/未读系统
- 不维护订阅游标
- 不维护长期 seen 集合
- 不把 lane 做成完整 feed reader

当前只记录：

> **本次 run 抓到的 entry signal。**

这意味着：

- 同一篇文章在不同 run 中理论上可能重复出现
- 去重与选择压力留给后续加工层或运行策略控制

这个取舍与整个 signal-layer 哲学一致：

- 先收
- 后选
- 不在召回层过早引入复杂状态与判断

---

## 八、锚点 / 命名策略

RSS 世界中的 `guid` / `id` 质量并不总是稳定，因此 Phase 1 采用：

> **优先 `entry_id/guid`，没有则退化到 `canonical_url`。**

也就是说：

1. 如果 feed entry 自带稳定 `id` / `guid`，优先用它作为锚点
2. 如果没有稳定 id，再退化到文章 canonical URL

这样既能尽量利用 feed 原生标识，也能适应现实世界里较脏的 feed 数据。

---

## 九、这条 lane 的意义

`rss-lane` 补上的不是“又一种资讯源”，而是：

> **稳定订阅源 + 长文字正文层**

这恰好补上了当前系统的缺口：

- `github-watch` / `github-trending-weekly` 更偏 repo / repo 变化
- `x-watchlist` / `x-feed` 更偏社交短内容
- `product-hunt-watch` 更偏产品发布与发现

而 RSS/blog/feed 这一层更适合承接：

- 长文解释
- 方法论
- 深度观察
- 适合沉淀的外部观点源

因此它会成为召回层到内容生产层之间非常关键的一块拼图。

---

## 十、一句话版结论

截至 2026-03-29，`rss-lane` 的 Phase 1 已明确为：

> 一条 RSS-first 的订阅源召回型 lane；以白名单 feed 为输入，将每篇 feed entry 记录为一条 signal，signal 语义是“某个订阅源发布了一篇新内容”；feed 负责发现 entry，但默认继续抓取原文全文作为正文证据；Phase 1 保持 stateless，不做订阅器式游标或长期已读状态；锚点策略为优先 `entry_id/guid`，没有则退化到 `canonical_url`。
