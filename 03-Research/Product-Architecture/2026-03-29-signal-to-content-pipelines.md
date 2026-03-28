---
title: Signal to Content Pipelines
date: 2026-03-29
tags:
  - daily-lane
  - signal-system
  - content-pipeline
  - architecture
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# Signal to Content Pipelines

这份笔记用于记录 2026-03-29 的一个阶段切换：`daily-lane` / signal system 的第一层召回骨架已经基本成形，接下来不再只是继续扩 lane，而是要从 signal pool 长出实际内容生产层。

换句话说，当前系统不再只是“怎么抓信号”，而要开始回答：

> **怎么把 signal 变成真正要交付的内容。**

---

## 一、当前阶段判断

如果类比推荐系统，那么当前已经基本完成的是：

- **召回层 / Recall Layer**
- 也就是 lane -> signal -> signal pool

目前已经收敛出的 lane 包括：

- `github-watch`
- `product-hunt-watch`
- `x-watchlist`
- `x-feed`
- `github-trending-weekly`

这些 lane 的共同职责是：

- 从不同来源召回原始信号
- 尽量无损保留
- 不在 signal 层过早做筛选、去重、推荐判断

这部分更像底层情报召回系统，而不是最终内容产品。

---

## 二、下一阶段不是继续扩召回，而是长出内容生产层

在贵平的明确要求下，下一步要开始面向实际交付物工作，而不只是继续扩充 signal lane。

当前已经明确的目标产物包括：

1. **每周整理一批待学习文章 / 待关注项目到 Obsidian**
2. **每隔两天写一篇公众号或小红书内容**
3. **每天推送飞书版本的 AI 日报**

因此，召回层之后必须长出新的“加工层 / 生产层”。

---

## 三、两条上层 pipeline（已拍定）

当前不采用“一条统一 pipeline 服务所有产物”，也不一上来拆成三套完全独立系统，而是先收成：

> **两条 pipeline**

### Pipeline 1：日报 pipeline

面向：

- 飞书 AI 日报

特征：

- 高时效
- 高频
- 更关注“今天最值得知道什么”
- 偏压缩、偏快报、偏决策支持

### Pipeline 2：知识 / 写作 pipeline

面向：

- Obsidian 的待学习 / 待关注清单
- 公众号 / 小红书选题与写作

特征：

- 偏长期积累
- 偏主题沉淀
- 偏表达素材管理
- 更关注“什么值得进入长期知识库、值得持续关注、值得写出来”

---

## 四、为什么先分成两条，而不是一条或三条

### 不选“一条统一 pipeline”

因为：

- 日报的时效逻辑
- 知识库沉淀逻辑
- 写作选题逻辑

并不相同。

如果强行先统一，很容易把：

- 快报
- 长期积累
- 表达选题

混成一个模糊的选择器。

### 不选“三条完全分开 pipeline”

因为当前又还太早。

如果现在直接拆成：

- 日报 pipeline
- Obsidian pipeline
- 写作 pipeline

会让系统在上层过早碎裂，失去共用的“知识/写作素材池”。

因此，当前最合适的形状是：

> **日报 pipeline** 与 **知识/写作 pipeline** 先分开。

---

## 五、后续 brainstorm 焦点

接下来更值得继续定义的问题，不再是 lane 怎么抓，而是：

### 对日报 pipeline

- 它的第一价值是什么
- 是“重点快报”还是“结构化全景概览”
- 如何从 signal pool 里挑出真正值得当天推送的少数信息

### 对知识 / 写作 pipeline

- 如何从 signal pool 中筛出“值得长期沉淀”的内容
- 如何形成待学习文章 / 待关注项目清单
- 如何从中继续长出公众号 / 小红书的选题与写作素材

---

## 六、一句话版结论

截至 2026-03-29，`daily-lane` / signal system 的下一阶段方向已明确为：

> 在已成形的 signal recall layer 之上，先长出两条内容生产 pipeline：一条面向高时效的飞书 AI 日报，一条面向 Obsidian 知识沉淀与公众号/小红书写作的长期积累与表达 pipeline。
