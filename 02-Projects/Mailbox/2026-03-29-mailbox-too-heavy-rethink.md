---
title: Mailbox 太重了，应该重新思考边界
date: 2026-03-29
tags:
  - mailbox
  - architecture
  - rethink
  - openclaw
status: draft
---

# Mailbox 太重了，应该重新思考边界

## 当前判断

当前对 Mailbox 系统的核心反思是：

> 问题可能不是“没做完”，而是“做得太全了，因此不好用”。

现状中至少已经存在这些层：

- `openclaw-mailbox-server`
- `openclaw-mailbox-cli`
- `openclaw-mailbox-plugin`
- `openclaw-mailbox-skill`
- 以及 workspace 内的 plugin 副本

如果从工程分层看，这些层各自都有理由；但从真实使用路径看，已经明显偏重。

---

## 当前阶段的结构判断

### 建议保留

#### 1. `openclaw-mailbox-server`
负责：
- message / thread / obligation 真相
- reply / seen / delivered 语义
- representative
- HTTP API

判断：
- 从职责上看是最清楚的一层
- 不是当前“太重”的主要来源

#### 2. `openclaw-mailbox-cli`
负责：
- `pick-mail`
- `open`
- `reply`
- `status`
- 本地 retry / checkpoint / cache

判断：
- 值得保留
- 但应瘦身，避免继续膨胀为半个 runtime framework

---

### 最可疑的重层

#### 3. `openclaw-mailbox-skill`
当前承载：
- snapshot
- decision envelope
- urgency
- recommended actions
- next_check_hint
- health/business split

判断：
- 对当前真实需求来说，这层明显过重
- 最多保留成很薄的规则文档 / evaluator 库
- 不值得继续当作一个核心独立层发展

#### 4. `openclaw-mailbox-plugin`
当前承载：
- heartbeat-skill
- heartbeat-block
- runtime-state
- plugin lifecycle
- adapter
- openclaw entry

判断：
- 现在最重的一层就在这里
- 未来如果保留，应该收缩成一个极简 arrival adapter
- 只负责：触发检查 → 读最小状态 → 按规则 inject 一句提醒

---

## 更激进的信号

用户已经明确提出一个更激进的方向：

> 也许不只是 skill/plugin 太重，甚至连 server 层都不想要了。

这说明接下来不该再默认站在“保留现有体系”的立场上小修小补，而应该重新问：

### Mailbox 的最小本质到底是什么？

可能的重新定义方向：
- 不是 multi-agent governance substrate
- 不是完整异步通信操作系统
- 而只是：
  - 一个轻量异步 inbox
  - 一个 obligation surfacing 机制
  - 一个让主 session 低摩擦感知待处理事项的入口

---

## 需要下一步 brainstorm 的核心问题

### 问题 1
Mailbox 最小要解决的到底是什么？

候选答案可能包括：
- A. agent 之间异步发消息
- B. 主会话里的“待处理项冒头”
- C. 一种跨 agent / 跨机器的 inbox
- D. 本质上只是一个 lightweight task handoff / receipt 机制

### 问题 2
Server 是不是必须存在？

如果不要 server，可选替代可能包括：
- 本地文件系统 inbox
- git-backed mailbox
- sqlite file + local tool
- append-only event log
- 甚至只是 structured notes + receipts

### 问题 3
什么东西必须保留，什么东西应该推倒重建？

需要明确：
- 哪些语义是真需求
- 哪些只是之前为了“完整体系”而长出来的官僚层

---

## 当前阶段结论

当前最合理的动作不是继续修补现有 Mailbox Phase 1，而是：

> 正式进入一次“从本质重新定义 mailbox”的 brainstorm。

这次 brainstorm 不应该默认保留：
- skill
- plugin runtime
- decision envelope
- 甚至不应该默认保留 server

而应该先回答：

> 如果今天从零开始，只为了“让待处理事项以低摩擦方式进入主工作流”，最小系统应该长什么样？
