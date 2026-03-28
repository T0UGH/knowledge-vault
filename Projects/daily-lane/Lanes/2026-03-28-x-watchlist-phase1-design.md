---
title: X Watchlist Phase 1 Design
date: 2026-03-28
tags:
  - daily-lane
  - x
  - watchlist
  - lane
  - phase1
status: draft
source_repo: https://github.com/T0UGH/daily-lane
---

# X Watchlist / Phase 1 Design

这份笔记用于承接 2026-03-28 在 daily-lane 上对第三条 lane 的设计讨论，目标是把 `x-watchlist` 收成一个可以继续写 spec / implementation plan 的 Phase 1 版本。

---

## 一、定位

`x-watchlist` 是一条：

> **账号监控型、watchlist-first、stateless 的 lane。**

它的第一价值不是开放信号发现，也不是摘要/推荐，而是：

> **看指定账号在近 24 小时里发了什么。**

因此，这条 lane 更像“监控已知重要账号的输出归档层”，而不是 `x-feed` 那种开放流消费层。

---

## 二、输入组织

watchlist 采用：

- **账号列表 + 分组标签**

也就是：

- 核心输入仍然是账号
- 每个账号可以附带 `group/tag`
- 分组语义不只存在于配置层，也应直接进入 signal file

这样既保持 Phase 1 的简单性，又不丢掉后续消费需要的重要上下文。

---

## 三、signal 单元

第一版明确采用：

> **一条 post = 一条 signal**

这里的 post 包括：

- 原创
- reply
- repost

当前不在 signal 层先裁掉 reply / repost，原因是：

- 这条 lane 的目标是尽量完整保留 watchlist 账号的输出
- 用户明确偏好“要全、要简单”，不希望收集层先做太多主观裁剪

这也意味着：

- 很短的一条内容是 post
- 很长的一条专业性长文也是 post
- thread 的复杂性不通过“长短”处理，而是通过关系字段处理

---

## 四、状态与时间窗口

### 1. 第一版不做 `state/`

`x-watchlist` 当前明确收成：

> **stateless lane**

也就是：

- 不维护 latest_seen
- 不做账号级 state
- 不做“只抓新增”的同步逻辑
- 不保存上次抓取位置

### 2. 时间窗口采用近 24 小时滚动窗口

由于不做 state，这条 lane 不采用自然日边界，而采用：

> **近 24 小时滚动窗口**

原因是：

- 比自然日更不容易漏抓
- 和 stateless 更匹配
- 更适合未来定时运行

同日重复运行的幂等问题，应由文件命名/覆盖规则来解决，而不是靠 state。

---

## 五、signal 锚点

每条 signal 至少保留两个稳定锚点：

- `post_id`
- `author_handle`

这两个字段分别承担：

- `post_id`：稳定唯一标识
- `author_handle`：人类可读归属与索引锚点

在此基础上，可继续保留其他上下文字段，但这两个应视为第一版最核心的对象标识。

---

## 六、正文策略

`x-watchlist` 的正文采用：

> **结构化正文**

不是只贴原文，也不是富上下文聚合。

原因是：

- X 上单条内容天然上下文不足
- 纯原文会让回看和检索变得不稳定
- 但第一版又不该滑向 thread 聚合或摘要层

因此，正文至少应承载：

- 原文
- 作者
- 发布时间
- post 类型（原创 / reply / repost）
- 链接
- 回复/转发对象锚点（如果有）
- 媒体/外链（如果能稳定拿到）

目标是：

- 保持原始内容优先
- 同时让 signal file 自足可读

---

## 七、thread 策略

第一版不做 thread 聚合，但保留最小 thread 关系。

也就是：

> **一条 post 仍然是一条 signal，但保留最小 thread 关系字段。**

例如：

- `conversation_id`
- `in_reply_to_post_id`
- `post_type`
- `is_reply` / `is_repost`（若实现方式更偏布尔表达）

这使得：

- thread 上下文没有被完全抹掉
- 但系统不需要在第一版承担 thread 合并逻辑

---

## 八、group/tag 的位置

由于 watchlist 的输入组织已经明确是“账号列表 + 分组标签”，因此第一版建议：

> **group/tag 直接落进 signal file。**

而不是只停留在配置层或 index 推导层。

这样后续无论是：

- 检索
- index 分组查看
- 下游消费
- 后续 candidate / brief 层

都能直接利用这些结构信息。

---

## 九、Phase 1 一句话版结论

截至 2026-03-28，`x-watchlist` 的 Phase 1 可以概括为：

> 以“账号列表 + 分组标签”作为 watchlist 输入，围绕近 24 小时滚动窗口，收集指定 X 账号发出的原创 / reply / repost，并将每条 post 作为一条独立 signal 保留下来；signal file 至少保留 `post_id` 与 `author_handle` 作为稳定锚点，正文采用结构化正文，保留最小 thread 关系与 group/tag，不做 `state/`、latest_seen 同步、thread 聚合与 AI 筛选。
