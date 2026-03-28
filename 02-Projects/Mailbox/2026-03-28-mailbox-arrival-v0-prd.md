---
title: Mailbox Arrival V0 PRD
date: 2026-03-28
tags:
  - mailbox
  - prd
  - arrival-policy
  - openclaw
  - phase1
status: draft
---

# Mailbox Arrival V0 PRD

## 1. 背景

当前 `agent-mailbox` 虽然已经在协议、CLI、plugin 与注入链路上逐步收口，但真正影响使用意愿的核心问题并不是“消息能否送达”，而是：

> **mailbox 的消息能否以低摩擦方式进入主 agent 工作流，而又不制造高频打断。**

如果 mailbox 完全依赖人工轮询，它最终会退化成一个“理论存在、实践没人看”的 inbox。  
如果 mailbox 太激进地即时推送，它又会变成一个不断打断主会话的噪音源。

因此，需要一个**极简可落地**的 Arrival V0，用最小规则验证 mailbox 是否能“适度冒头”。

---

## 2. 产品目标

Mailbox Arrival V0 的目标只有一个：

> **验证 mailbox 能否在不依赖人工轮询的前提下，适度进入主工作流，并建立存在感。**

V0 不是一个智能决策系统，不追求复杂优先级，不追求最佳策略，只追求：

- 能实现
- 能跑起来
- 能让用户感知到 mailbox 的存在
- 不至于制造明显打断疲劳

---

## 3. 非目标

V0 明确不解决以下问题：

- 不做 LLM 驱动的 arrival 决策
- 不做复杂 urgency 推理
- 不做 summary enrich
- 不做 `open` 预取
- 不做 health/business 双通道策略
- 不做动态优先级排序
- 不做复杂 safe-point 判定
- 不做多轮自治 mailbox 处理循环

换句话说：

> V0 不是“智能邮箱调度器”，而只是一个最小提醒规则。

---

## 4. 用户问题

当前用户侧真实痛点是：

1. 如果 mailbox 不会自己冒头，系统很容易沦为死 inbox
2. 如果 mailbox 乱冒头，又会打断主 agent 正常工作
3. 如果注入前还需要昂贵模型推理，成本会快速失控
4. 用户更关心“存在感”是否成立，而不是第一版是否足够聪明

---

## 5. 设计原则

### 5.1 Delivery is cheap, interpretation is expensive

消息到达层必须便宜；复杂理解留到真正 `open` 或后续处理阶段。

### 5.2 先验证存在感，再优化聪明度

第一版优先验证 mailbox 是否会自然浮现，而不是先做复杂策略。

### 5.3 只用规则，不用 LLM

Arrival V0 的判定与注入全部走固定规则和固定模板，不引入大模型。

### 5.4 能少做就少做

每多一层策略，都会提高实现复杂度与失效风险。V0 必须足够薄。

---

## 6. V0 规则定义

### 6.1 `open obligation`

当存在 `open obligation` 时：

- **立即注入主会话**
- 使用固定模板提醒

模板：

> Mailbox：有 1 条 open obligation，建议现在 `open <id>`。

### 6.2 新 `request`

当存在新 `request` 时：

- **在下一个 safe-point 注入一次**
- 使用固定模板提醒

模板：

> Mailbox：有 1 条新 request，建议在方便时 `open <id>`。

### 6.3 普通 `info`

当消息类型为普通 `info` 时：

- **不注入主会话**
- 仅保留在 mailbox 中，等待后续被查看

---

## 7. Safe-point 定义（V0）

为了控制复杂度，V0 的 safe-point 只认两个：

1. **agent 刚回复完一轮**
2. **heartbeat 命中**

V0 不引入更多复杂状态，例如：

- tool chain 收口
- 子任务阶段结束
- busy/idle/critical section 推理
- 动态时间阈值

这些都留到后续版本再讨论。

---

## 8. 注入文案策略

V0 使用**固定模板**，不使用大模型总结。

要求：

- 文案短
- 可执行
- 不带复杂解释
- 不尝试理解原始消息全文

V0 的注入目标不是“解释清楚所有内容”，而是：

> **让主 agent 知道 mailbox 现在值得看一眼。**

---

## 9. 伪代码

```text
if open_obligation exists:
    inject("Mailbox：有 1 条 open obligation，建议现在 open <id>。")
else if new_request exists and now_is_safe_point:
    inject("Mailbox：有 1 条新 request，建议在方便时 open <id>。")
else:
    do nothing
```

---

## 10. 成功标准

V0 是否成功，只看 3 个问题：

1. **obligation 会不会自动冒头**
2. **request 会不会在 safe-point 冒头**
3. **这种注入频率会不会让人觉得烦**

如果这 3 个点成立，说明 mailbox 已经具备了“存在感”这一最基础能力。

---

## 11. 风险与限制

### 风险 1：request 仍可能存在感不足

由于 safe-point 只定义得很薄，某些 request 仍可能不够及时。

### 风险 2：提醒文案过于薄

固定模板虽然便宜稳，但可能在某些场景下信息量不足。

### 风险 3：缺少 health 通道

V0 暂时不处理 failed outbound / sync 异常 等健康类信号的独立注入策略。

### 风险 4：无法区分 request 内部优先级

V0 只验证“会不会冒头”，不验证“冒头是否最优”。

---

## 12. 后续版本方向（非 V0 范围）

如果 V0 跑通，后续可以按顺序考虑：

1. 扩展 safe-point 定义
2. 引入 health/business 双通道
3. 对 obligation 允许一次轻量 `open` enrich summary
4. 给 request 增加更细优先级策略
5. 最后才考虑是否需要轻量 decision layer

原则仍然是：

> **先跑通最小提醒规则，再逐步增加复杂度。**

---

## 13. 一句话总结

Mailbox Arrival V0 不是一个决策系统，而是：

> **一个让 mailbox 在不靠人工轮询的前提下，开始自然冒头的最小提醒规则。**
