---
title: Product Hunt Watch Phase 1 Design
date: 2026-03-28
tags:
  - daily-lane
  - product-hunt
  - lane
  - phase1
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# Product Hunt Watch / Phase 1 Design

这份笔记用于承接 2026-03-28 在 `daily-lane` / signal system 上的继续设计，目标是把 Product Hunt 追踪正式收成第二条 lane 的 Phase 1 规格，并作为后续交给 Arc 落实现的依据。

---

## 一、定位

`product-hunt-watch` 是一条：

> **topic-first、stateless、按命中事件记录的 lane。**

它不是推荐系统，不是日报系统，也不是 Product Hunt 浏览产品层。

它在 Phase 1 的职责只是：

- 按配置好的 Product Hunt topics 进行收集
- 将命中的产品信号写成 markdown signal files
- 以 lane-first / date-partitioned 的方式沉淀到 signal pool

也就是说，这条 lane 只负责：

```text
Product Hunt topics -> signal -> signal pool
```

暂时不承担：

- 推荐排序
- 候选项筛选
- AI 判断
- 跨天去重
- 状态变化跟踪
- 下游日报生成

---

## 二、为什么先做 Product Hunt

在 `github-watch` 已经完成之后，下一条最适合接入的 lane 不是 X，也不是 GitHub discovery，而是 Product Hunt。原因是：

1. 来源单一，结构相对清晰
2. 比 X 更少登录态 / 刷流复杂度
3. 比 GitHub discovery 更不容易立刻滑向 candidate / ranking 层
4. 很适合作为一条 **非 GitHub、非 release 型、且不依赖 state 的 lane 样板**

因此，`product-hunt-watch` 的价值不仅是“多接一个来源”，更是验证：

> 当前这套 lane-first / signal-first 骨架，是否可以承接第二种来源形态。

---

## 三、Phase 1 设计结论

### 1. 入口方式：topic-first

Product Hunt Phase 1 不做“全站抓取”，也不做关键词为主的召回，而是明确走：

> **topic-first**

也就是：

- 先选择一小组稳定 topic
- lane 只对这些 topic 下的结果进行收集
- 不在收集层引入 AI 决策
- 不把关键词匹配作为第一层主轴

### 2. topic 配置：可配，但第一版只放 3 个

topic 应作为 lane 配置输入，而不是写死在代码里。

Phase 1 的初始 topic 定为：

- `Artificial Intelligence`
- `Developer Tools`
- `Productivity`

这组 topic 的意图是：

- `Artificial Intelligence`：覆盖 AI / agent 主线
- `Developer Tools`：覆盖 Claude Code / coding workflow / dev tooling
- `Productivity`：承接工作流与效率工具，不让范围过窄

### 3. signal 单元：一个「产品 × topic」命中事件

这是这条 lane 最关键的设计决定。

Phase 1 不把 signal 定义成“一个产品”，也不定义成“一个页面批次”，而是定义成：

> **一个产品在某个 topic 下的一次命中事件。**

也就是：

- 如果某个产品同时命中两个 topic
- 那么生成两条 signal
- 不在 signal 层提前揉成一条“产品总记录”

原因是：

- 这条 lane 的第一价值，不是“今天有哪些产品”
- 而是：**“今天通过哪些 topic 命中了哪些产品”**

也因此，这条 lane 的 signal 语义，比 `github-watch` 更偏向：

> **topic-scoped launch signal**

### 4. 重复策略：按天允许重复，不做跨天去重

Phase 1 允许同一个 `产品 × topic` 在不同日期重复记录。

只要某个命中事件在当天收集中再次成立，就可以在当天日期目录下生成 signal。

当前明确不做：

- 全局唯一化
- 跨天去重
- 首次出现判断
- 基于 rank / votes 变化的二次筛选

原因很简单：

- 用户原则是“可以重复，但要全，要简单”
- Product Hunt 这条 lane 更适合按命中事件归档，而不是过早进入状态跟踪逻辑

### 5. Phase 1 明确为 stateless lane

`product-hunt-watch` 第一版不引入 `state/`。

也就是：

- 不保存上次页面快照
- 不做 yesterday vs today diff
- 不跟踪 votes / rank 的变化历史
- 不定义“变化才记 signal”

这条 lane 的第一版应明确收成：

> **stateless lane**

它和 `github-watch` 正好形成互补：

- `github-watch` = stateful lane
- `product-hunt-watch` = stateless lane

这有助于验证 signal system 对两类 lane 的承载能力。

### 6. signal 正文：结构化正文，而不是纯 metadata 卡片

Product Hunt 页面通常不像 GitHub release 那样自带一篇完整 release note，因此 signal 正文不能假设存在天然“文章原文”。

但这不意味着 signal 只能退化成几个 metadata 字段。

Phase 1 的结论是：

> **signal file 必须有结构化正文。**

这个正文不是 AI 总结，而是把 Product Hunt 页面上的原始关键信息按固定模板组织出来。

推荐正文结构至少包含：

- Product name
- Tagline
- Description
- Topic
- Rank / Votes snapshot（如果拿得到）
- Product Hunt link
- Website link（如果拿得到）
- Makers（如果拿得到）

这让 signal file 保持：

- 人类可读
- AI 可消费
- 原始证据优先
- 不退化为索引卡片

---

## 四、signal file 约束

### 1. frontmatter 最小字段

延续整个 signal system 的统一约束，至少保留：

- `type`
- `lane`
- `source`
- `url`
- `fetched_at`
- `title`

对 Product Hunt，建议再加最少的 source-specific 字段：

- `topic`
- `product_name`
- `rank`（如果可稳定取得）
- `votes`（如果可稳定取得）

### 2. type 建议

第一版建议统一为：

- `launch`

这里的 `launch` 不强调产品是否首次在 Product Hunt 全站出现，而强调：

- 这是一次 lane 命中的产品出现记录

### 3. 文件命名建议

由于 signal 单元是 `产品 × topic`，命名应能表达 topic 维度。

建议命名骨架类似：

```text
{product-slug}__{topic-slug}__launch.md
```

如果担心同日同产品同 topic 多次抓取产生覆盖，允许追加日期或抓取锚点，例如：

```text
{product-slug}__{topic-slug}__launch__2026-03-28.md
```

第一版更推荐后一种，避免未来补跑时发生歧义。

---

## 五、signal pool 结构

Product Hunt lane 继续沿用当前系统骨架：

```text
signals/
  product-hunt-watch/
    YYYY-MM-DD/
      index.md
      signals/
        <product-topic-signal-files>
```

第一版没有 `state/`。

这意味着它是当前系统中第一条明确的 **stateless lane**。

---

## 六、index.md 最低要求

`index.md` 依然只做索引，不做推荐排序或浏览产品层。

但它必须足够“有用”，最低应包含：

- lane
- date
- generated_at
- status
- Run Summary
- signals 表
- 每个 topic 的命中概况（可选，但很有价值）

对 Product Hunt 这条 lane，signals 表最低可以包含：

- File
- Type
- Product
- Topic
- Title

如果 rank / votes 稳定可得，也可以出现在表里，但不是硬要求。

---

## 七、Phase 1 明确不做什么

当前明确不做：

- 关键词二次筛选
- AI 质量判断
- 主题聚合与推荐排序
- 去重 / 合并 / 候选项生成
- 基于 votes / rank 变化的事件判断
- makers comment / 评论区深抓
- 评论、FAQ、gallery 等富正文增强

这些都属于未来可能继续长出的能力，但不属于当前最小验证版。

---

## 八、最低验收标准

`product-hunt-watch` 第一版可以视为通过，至少要满足：

1. 能读取配置中的 2-3 个 topic
2. 能对每个 topic 稳定收集产品列表
3. 能按 `产品 × topic` 生成 markdown signal files
4. signal file 同时具备机器可解析 frontmatter 与结构化正文
5. 能写入 lane-first / date-partitioned signal pool
6. 能生成当天 `index.md`
7. 不依赖 `state/`
8. 不引入 AI 决策、排序或去重逻辑

---

## 九、一句话版 spec

截至 2026-03-28，`product-hunt-watch` 的 Phase 1 可以概括为：

> 以 Product Hunt topic 作为配置化入口，先围绕 `Artificial Intelligence`、`Developer Tools`、`Productivity` 三个 topic，收集当天命中的产品，并将每个「产品 × topic」命中事件记录为一条带结构化正文的 markdown signal file；按天沉淀到 `product-hunt-watch` lane 的 signal pool 中，不做 `state/`、跨天去重、AI 判断与推荐排序。
