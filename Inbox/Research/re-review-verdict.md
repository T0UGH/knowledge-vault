---
title: Hermes 像素办公室看板方案 Re-review Verdict
tags:
  - hermes
  - monitoring
  - architecture
  - review
  - verdict
---

# Hermes 像素办公室看板方案 Re-review Verdict

## 1. Closure Decision

**Fail.**

这次修订已经明显推进，但**还不足以关闭原 review**。阻塞点不再是“方向没听懂”，而是主设计文档里仍保留了数段**与新规格互相冲突的旧字段/旧状态模型**，导致文档还不是一份自洽的 V0 规格。

## 2. What Improved

- **协议内核已明显收缩到可接受范围**：V0 core 现在收敛为 `collector` / `machine` / `runtime` / `activity` / `snapshot`，`channel` / `subagent` 降为 optional，`workspace` / `task` / generalized `resource_usage` 退出核心层。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:554-572`。
- **状态模型在正式 schema 里基本修正了**：`machine.online` 被移除，`runtime` 改成 `reachability_state + work_state + activity.kind`，并给推断字段补了 `source` / `confidence`。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:893-943`。
- **snapshot / delta 规则已大幅补齐**：补上了 `collector_seq`、幂等键、full snapshot replacement、`state_version`、`stale -> TTL 删除`、`resync_required`。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:857-879`、`2068-2104`。
- **安全边界比之前强得多**：长随机 URL 不再是唯一读侧控制，allowlist / redaction contract 也写出来了。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:788-830`。
- **V0 面收缩和边界补充基本到位**：`/ingest/events`、`/board/filters` 已明确 deferred，detail/history 也加了 `recent 10` 边界。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1812-1826`、`1962-1976`、`2105-2111`。

## 3. What Still Blocks Closure

- **旧协议草案段落还在正文里，且与正式字段草案冲突。** `统一协议 v0：建议字段方向` 仍写着 `workspace_id`、`status`、`current_activity`，并把 `idle` 放进 `activity.kind`；但后面的正式 V0 schema 已改成 `workspace_label` / `reachability_state` / `work_state` / `activity`。这不是注释级问题，而是规格冲突。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:707-779` 对比 `847-943`。
- **Hermes 映射层没有完全跟上新状态模型。** 文档仍保留 `runtime.workspace_id`、`runtime.connected`、`runtime.status` 的映射表，以及单独的 `Hermes -> runtime.status` 章节；这直接把已删除字段重新带回规范。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1137-1145`、`1328-1343`。
- **`channel_id` 仍未写成唯一 canonical 规则。** 前文公式写成 `runtime_id + ":" + platform + ":" + chat_id ...`，但映射表又写成 `platform:chat_id[:thread_id]`。既然后面有 `/board/channels/:channel_id` 顶层路由，这个冲突仍然是 closure blocker。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1259-1266`、`1948-1960`。
- **API 示例仍残留旧状态字段。** `GET /board/runtimes` 还按 `status` 过滤，WebSocket delta 示例也仍发 `"status": "active"`；同时文档后面要求 `GET /board/state` 和 WebSocket delta 都带 `state_version`，但示例没有体现。见 `2026-04-12-hermes-pixel-office-monitor-board-v1.md:1923-1929`、`1881-1890`、`2002-2015`、`2070-2076`。

## 4. Exact Remaining Required Changes

1. 删除或重写 `统一协议 v0：建议字段方向` 整段，使其与后面的正式 V0 schema 完全一致。
2. 在 Hermes adaptor 映射里彻底移除 `runtime.workspace_id`、`runtime.connected`、`runtime.status` 和 `Hermes -> runtime.status` 章节；统一改写为 `workspace_label`（若保留）、`reachability_state`、`work_state`、`activity`。
3. 把 `channel_id` 写成唯一且全局可用的 canonical 规则，并在公式、字段表、API 路由说明里保持完全一致。
4. 把所有 API 过滤条件和示例 payload 改到新状态模型：不要再出现泛 `status`；要明确用 `reachability_state` / `work_state`；`GET /board/state` 和 WebSocket delta 示例都要补上 `state_version`。
5. 做一次全文字段名清理，确保旧字段名不再以“建议”“示例”“映射表”形式残留在正文中。

## 5. Final Verdict

**Re-review result: fail.**

这版已经接近可关闭状态，但还差最后一轮“全文一致性收口”。在旧字段模型被完全清干净之前，原 review 不应视为关闭。
