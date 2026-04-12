# Critical Engineering Review: Hermes Pixel Office Monitor Board V1

Source reviewed: `Inbox/Research/2026-04-12-hermes-pixel-office-monitor-board-v1.md`

## 1. Executive Summary

This is a strong product concept with a mostly sound deployment direction: outbound collectors, a cloud hub, snapshot-first ingestion, and a board-friendly query layer are all good V1 instincts. The UI information architecture is also directionally right: `runtime` as the main visual entity is much more stable than `session`, and treating `channel`/`subagent` as secondary layers should keep the board readable.

The main weakness is architectural coherence. The document starts as a Hermes-specific V1, then upgrades itself into a generic multi-runtime observation platform, but the protocol is not yet reduced to a minimal, validated core. As written, the biggest delivery risk is not the pixel-office UI. It is state correctness: identity rules, snapshot overwrite semantics, hub-derived liveness vs collector-derived status, and heuristic inference for `activity`/`subagent`.

Recommendation: keep the adaptor boundary, but narrow V1 much harder. Preserve the generic direction, while explicitly deferring unstable abstractions until Hermes ingestion works end to end.

## 2. Strengths

- The network topology is correct for the stated environment. `collector -> cloud hub -> board` is the right model for home machines without public IPs. Refs: lines 161-203.
- Separating the visual shell from the Hermes-specific data layer is the right architectural boundary. Refs: lines 23-30, 118-138.
- `snapshot-first` is a pragmatic V1 choice and avoids premature event-stream complexity. Refs: lines 191-203, 818-824, 1662-1670.
- The UI model is mostly coherent. `runtime` as the primary actor and `channel` as a secondary object should reduce visual churn. Refs: lines 1348-1370, 1535-1546.
- The document is honest about weak spots such as subagent inference and model derivation. Refs: lines 1037-1040, 1250-1266.

## 3. Major Risks / Issues

### P0-1. Scope drift: the design mixes a Hermes V1 with a generic platform before the abstraction is stable

The early document is clearly Hermes-specific, while later sections reposition the system as a generic "Agent Runtime Monitor Protocol" platform. That direction is sensible, but the abstraction is incomplete: `workspace`, `task`, and `resource_usage` are declared as core objects, yet they never receive formal schema definitions. Refs: lines 1-30, 519-565, 826-990.

Impact:

- High risk of rework after the second adaptor.
- V1 may spend time solving abstract cross-runtime problems that Hermes does not yet force.
- The protocol is currently neither fully generic nor deliberately Hermes-first.

### P0-2. State semantics are conflated across liveness, health, and work activity

`machine.online` is always `true` in collector snapshots and is later hub-derived. `runtime.status` includes `offline`, which is also hub-derived. `connected` is another required boolean. `activity.kind` then overlaps again with state (`reading`, `blocked`, `error`, `waiting_*`). Refs: lines 849-850, 863-872, 886-895, 1284-1291.

Impact:

- Contradictory states are easy to produce.
- UI precedence rules will become ad hoc.
- Different adaptors will map similar facts into different fields.

### P0-3. Snapshot and delta consistency rules are missing

The API defines snapshot ingestion and board deltas, but not the ordering model. There is no monotonic collector sequence, no hub state revision, no explicit idempotency contract, and no delete semantics when a later full snapshot omits a previously seen `channel`, `session`, or `subagent`. Refs: lines 826-839, 1698-1730, 1931-1977, 2011-2056.

Impact:

- Stale entities may never disappear.
- Out-of-order snapshots may regress the board.
- WebSocket consumers cannot reason about missed or superseded updates.

### P0-4. Identity and lifecycle rules are underdefined

`runtime_id` may be `machine_id + profile_name` or `HERMES_HOME hash`. `channel_id` is `platform:chat_id[:thread_id]` without explicit machine/runtime scoping. `subagent_id` may be a child session ID or a synthesized parent-task index. These are not yet rigorous primary-key rules. Refs: lines 1091, 1211-1218, 1267-1278.

Impact:

- Collision risk across machines, profiles, or future runtime types.
- Rename/reconfiguration may silently create new entities.
- History, drill-down links, and board diffs will be fragile.

### P0-5. The access model is weaker than the sensitivity of the data being exposed

The board is Internet-exposed behind a long random URL, while the payload still includes channel names, profile/workspace labels, model names, sanitized command summaries, and optionally log-derived activity. That is operationally sensitive metadata even without raw prompts or message bodies. Refs: lines 381-404, 412-433, 781-799, 1048-1054, 1144-1155.

Impact:

- Leak of repo names, workflows, tool usage, and organization structure.
- The system may feel "safe" because it avoids full transcripts, while still exposing enough metadata to matter.

### P1-1. Heuristic inference is being promoted to first-class UI truth too early

The most visible parts of the board depend on inferred `activity`, inferred `runtime.status`, inferred model source precedence, and observational `subagent` detection. These are the least stable parts of the system, yet the design currently treats them as crisp states rather than best-effort views. Refs: lines 1106-1117, 1123-1155, 1250-1278, 1282-1296.

### P1-2. The data shape is duplicated between nested snapshots and flattened board state

In the protocol, `channels`, `subagents`, and `sessions` live under `runtime`. In the board API, those same entities are also served as top-level arrays. That is fine only if normalization rules are explicit, but they currently are not. Refs: lines 876-878, 1833-1842.

## 4. Contradictions / Gaps

- The protocol layer declares `workspace`, `task`, and `resource_usage` as core objects, but the formal v0 schema does not define them. Refs: lines 550-565 vs 826-990.
- Early UI guidance treats `reading` as an `active` substate, while later protocol/UI sections make it a first-class top-level state. Refs: lines 343, 863, 1598-1606.
- `machine.online` is sent from the collector but defined as a hub-derived truth. Refs: lines 849-850.
- Heartbeat is optional, but offline semantics rely on hub-side absence detection. Timeout thresholds and source-of-truth rules are not defined. Refs: lines 1745-1762, 1286, 1606.
- The document says "metadata only", but also proposes optional log parsing and human-readable command summaries. There is no explicit sanitization contract or allowlist. Refs: lines 395-405, 1048-1054, 1144-1148.
- History is mentioned as "recent N snapshots" and "optional compact history", but retention, cap, and intended consumers are not defined. Refs: lines 1813-1821, 2011-2015.
- Detail endpoints can easily become unbounded because `sessions`, `channels`, and `subagents` are returned without limits, pagination, or "recent N" contracts. Refs: lines 1889-1912.

## 5. Concrete Recommendations

### P0

- Reduce the v0 protocol kernel to the minimum cross-runtime set: `collector`, `machine`, `runtime`, `activity`, plus optional `channel` and `subagent` references. Explicitly defer `workspace`, `task`, and generalized `resource_usage` until a second adaptor proves they are shared abstractions.
- Split state into orthogonal dimensions:
  - `reachability_state`: `online` / `offline` / `stale`
  - `work_state`: `idle` / `active` / `waiting` / `blocked` / `error`
  - `activity.kind`: `reading` / `writing` / `tool_call` / ...
  Remove `machine.online` from collector payloads and make hub-derived health explicit.
- Add strict snapshot semantics:
  - `collector_seq` monotonic per collector
  - `state_version` monotonic for board/current-state reads
  - idempotency keyed by `collector_id + snapshot_id`
  - explicit "full snapshot replaces previous children" rules
  - delete/tombstone policy for omitted `channel`/`session`/`subagent`
- Define canonical ID rules for every entity:
  - stability requirements
  - scope boundaries
  - rename/regeneration behavior
  - TTL for ephemeral entities
- Raise the minimum access-control bar for the board. If full auth is still deferred, use at least one of reverse-proxy auth, expiring signed URLs, or VPN/IP allowlisting. Also make redaction part of the protocol contract, not a best-effort collector detail.

### P1

- Add `confidence` or `source_quality` to inferred fields such as `activity`, `model`, and `subagent`. The UI should be able to render uncertainty instead of false precision.
- Choose one canonical ingest shape and document hub normalization rules. If snapshots are nested, specify exactly how the hub flattens them and how absence is interpreted.
- Define freshness and smoothing rules: offline timeout, active-window duration, minimum visible duration for subagents, and debounce/hysteresis for state changes.
- Bound drill-down APIs with caps or pagination and specify "recent N" behavior for sessions/subagents.
- Standardize metric scope. `token_usage`, `context_usage`, `cost_estimate`, and aggregate `metrics` should all carry precise window semantics.

### P2

- Add a schema-evolution section covering unknown fields, backward compatibility, and protocol-version transitions.
- Add operability requirements for the monitor system itself: collector lag, hub ingest errors, redaction failures, token rotation workflow, and hub health visibility.
- Defer nonessential surface area such as `/board/filters`, predeclared `/ingest/events`, and optional history endpoints until the base snapshot pipeline is proven stable.

## 6. Open Questions

- Is V1 optimized for "Hermes works quickly and reliably" or for "the generic protocol survives multiple runtimes"? The current draft tries to optimize both.
- What is the canonical meaning of `channel` for runtimes that do not naturally expose channel/thread/source concepts?
- Is `workspace_id` a real cross-runtime object, or just a display alias for profile/home in Hermes?
- Are `session` and `subagent` meant to be historical objects, or only current-state projections?
- When a snapshot omits a previously reported `subagent` or `channel`, should the hub delete it immediately, mark it stale, or keep it until TTL expiry?
- What exact fields are allowed to leave the machine after sanitization? Is there a written allowlist?
- What source wins when current model detection conflicts across session state, config, and observed activity?
- What is the minimum second adaptor that will be used to validate the protocol before the abstraction is declared stable?
