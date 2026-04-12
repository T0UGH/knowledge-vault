---
title: Hermes 像素办公室看板方案 Review Response Verdict
tags:
  - hermes
  - monitoring
  - architecture
  - review
  - verdict
---

# Hermes 像素办公室看板方案 Review Response Verdict

## 1. Overall Acceptance Judgment

**不接受将这轮 review 视为已关闭。**

这份 response 作为问题分拣和优先级重排是合格的，但作为 close-out 不合格。它大体听懂了 review，也接受了大多数方向性批评，但关键问题仍停留在“后续会补”的层面；而主设计文档里仍然保留着原 review 指出的核心矛盾。

当前状态只能算：

> review 已被理解，但没有被关闭。

## 2. Which Responses Are Sufficient

- **Scope drift**：接受协议内核收缩，并把 `workspace` / `task` / `resource_usage` 降级处理，这个方向是对的。见 `review-response.md:37-79`。
- **状态正交化方向**：把 `reachability_state`、`work_state`、`activity.kind` 分开，是正确修正。见 `review-response.md:81-127`。
- **推断字段加不确定性**：给 `activity` / `model` / `subagent` 增加 `source` / `confidence` 是合理回应。见 `review-response.md:243-271`。
- **Hub normalization**：承认 ingest 的 nested snapshot 和 board 的 flattened current state 是两层不同模型，这一点是对的。见 `review-response.md:273-294`。
- **freshness / pagination / metric scope**：这些补充项的优先级基本合理。见 `review-response.md:296-359`。

## 3. Which Responses Are Weak / Insufficient

- **安全响应最弱。** 原 review 要求的是把 board 的最小访问控制门槛抬高，不是“V1 立刻上完整登录系统”。见 `review.md:94-111`。response 把问题改写成“要不要上复杂 auth”，然后继续保留“长随机 URL”作为读侧控制。见 `review-response.md:197-241`。
- **Snapshot / delta 语义仍然只是 TODO 列表。** response 提到了 `collector_seq`、`state_version`、幂等键、full snapshot replacement、omission semantics，这说明问题抓对了。见 `review-response.md:129-160`。但它没有定义 delete / stale / tombstone / TTL 策略，也没有定义 delta 的版本契约。主文档对应部分仍然是空的。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1698-1778`、`1931-2056`。
- **ID 规则没有闭环。** response 承认 ID 必须写死，但没有真的选定 canonical 规则。见 `review-response.md:162-195`。主文档里 `runtime_id` 仍然是二选一，`channel_id` 仍然缺少明确 machine/runtime 作用域，`subagent_id` 也仍然是二选一生成思路。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1089-1097`、`1209-1218`、`1267-1278`。
- **状态语义回应仍停留在口头层。** response 说会拆成三层状态，这没问题。见 `review-response.md:81-127`。但主文档仍然同时保留 `machine.online`、`runtime.status`、`connected`、`activity.kind`，语义仍然重叠。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:840-895`、`1280-1295`、`1598-1606`。
- **协议收缩还没有真正落实。** 主文档仍把 `workspace`、`session`、`task`、`resource_usage` 放在 protocol / schema 语境里。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:550-565`、`700-978`。
- **API 面收缩也没有落实。** `/ingest/events` 和 `/board/filters` 仍然挂在 V0 文档里。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1764-1778`、`1914-1927`。

## 4. What Must Still Change In The Main Design Doc Before This Review Is Truly Closed

1. **重写 V0 协议核心。**
   只把 `collector`、`machine`、`runtime`、`activity` 作为 core，`channel` / `subagent` 作为 optional 子层。`workspace`、`task`、generalized `resource_usage` 不应继续作为协议核心对象出现。`session` 如果保留，也应降级成 Hermes-first 的辅助明细层。

2. **把状态模型改成真正正交的规格。**
   从 collector payload 去掉 `machine.online`；reachability 改成 hub-derived；`runtime.status` 不再混合 reachability / work state / activity；`connected` 要么删除，要么给出非常窄且稳定的定义；UI 需要明确的状态优先级规则。

3. **补齐 snapshot / delta 的严格一致性语义。**
   写死 `collector_seq`、`state_version`、幂等键、full-snapshot replacement 的边界、omitted child 的处理策略、delete/stale/tombstone/TTL 规则，以及 WebSocket 的版本与 resync 规则。

4. **把所有 ID 规则定成唯一的 canonical 规则。**
   不能继续写成“`A` 或 `B`”。必须给出每个对象的唯一生成方式、作用域、稳定性、rename 行为、重建行为、以及 ephemeral TTL。

5. **提高 board 读侧访问控制的最低标准。**
   “长随机 URL”不能继续作为唯一读侧控制。
   至少补一个真正的最小门槛，例如 signed URL、reverse-proxy auth、或 VPN/IP allowlisting。

6. **把“metadata only”改成真实的 allowlist / redaction contract。**
   文档必须列出哪些字段允许离开机器，哪些默认禁止。
   还要写清 `command_display`、workspace/channel/profile 名、路径、repo 名、私有代号、长参数、log-derived 文本如何处理。

7. **把 hub normalization 写成规则。**
   明确 nested snapshot 是 ingest 形状，hub current state 才是 board canonical state。
   还要写清 flatten 规则、absence 解释、以及 `runtime.sessions` / `channels` / `subagents` 与 board 顶层数组之间的对应关系。

8. **给 detail API 和 history 加边界。**
   详情接口和 history 必须写 `recent N`、上限、或 pagination 规则。

9. **把非必要 API 面从 V0 主路径移除或明确 deferred。**
   `/ingest/events`、`/board/filters`、可选 history 等，如果不属于第一版最小实现，就不要继续像既定接口一样挂在 V0 主文档里。

10. **补 freshness / smoothing 规则，并统一指标窗口语义。**
    写清 offline timeout、stale window、active window、waiting window、subagent 最小显示时长、debounce / hysteresis；同时补齐 `context_usage`、`token_usage`、`cost_estimate`、aggregate `metrics` 的窗口定义。

## 5. Final Verdict

这份 response **可以接受为中间态回应，不可以接受为结案回应**。

一句话定性：

> 它证明作者听懂了 review，但还没有把 review 变成新规格。

因此当前正确状态应是：

- **response：部分通过**
- **review closure：不通过**
- **下一步：先改主设计文档，再谈 review 关闭**

补充说明：`schema evolution` 和 `monitor 自监控` 可以继续作为后续项，它们不是本轮不关闭的主因。真正阻塞关闭的是 P0 级协议/状态/身份语义问题，以及安全边界仍然偏弱。
