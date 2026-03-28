---
title: Mailbox Arrival V0
date: 2026-03-28
tags:
  - mailbox
  - arrival-policy
  - openclaw
  - product
  - protocol
---

# Mailbox Arrival V0

## 目标

只验证一件事：

> **mailbox 能不能在不靠人工轮询的情况下，适度冒头。**

V0 不追求智能，不追求完美，只追求：

- 能实现
- 能跑
- 有存在感
- 不太烦

---

## 规则

### 1. 有 `open obligation`

→ **立即注入主会话**

固定提示：

> Mailbox：有 1 条 open obligation，建议现在 `open <id>`。

### 2. 有新 `request`

→ **在下一个 safe-point 注入一次**

V0 的 safe-point 只认两个：

- agent 刚回复完一轮
- heartbeat 命中

固定提示：

> Mailbox：有 1 条新 request，建议在方便时 `open <id>`。

### 3. 普通 `info`

→ **不注入**

只保留在 mailbox 里。

---

## 不做的事

V0 明确不做：

- 不用 LLM
- 不做复杂决策
- 不做 urgency 推理
- 不做 summary enrich
- 不做 `open` 预取
- 不做复杂 safe-point
- 不做 health/business 双通道
- 不做动态优先级系统

---

## 本质实现

就 3 条 if-else：

```text
if open_obligation exists:
    inject obligation notice
else if new_request exists and now_is_safe_point:
    inject request notice once
else:
    do nothing
```

---

## 验证标准

V0 跑通只看 3 件事：

1. obligation 会不会自动冒头
2. request 会不会在 safe-point 冒头
3. 这个注入频率会不会烦

---

## 一句话总结

**V0 不是决策系统，只是一个“mailbox 会冒头”的最小提醒规则。**
