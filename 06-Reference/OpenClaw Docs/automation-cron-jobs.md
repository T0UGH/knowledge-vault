# automation/cron-jobs

Source: https://docs.openclaw.ai/automation/cron-jobs

Title: Cron Jobs - OpenClaw

URL Source: https://docs.openclaw.ai/automation/cron-jobs

Markdown Content:
# Cron Jobs - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/cron-jobs#content-area)

[OpenClaw home page![Image 1: dark logo](https://mintcdn.com/clawdhub/dpADRo8IUoiDztzJ/assets/pixel-lobster.svg?fit=max&auto=format&n=dpADRo8IUoiDztzJ&q=85&s=8fdf719fb6d3eaad7c65231385bf28e5)![Image 2: dark logo](https://mintcdn.com/clawdhub/dpADRo8IUoiDztzJ/assets/pixel-lobster.svg?fit=max&auto=format&n=dpADRo8IUoiDztzJ&q=85&s=8fdf719fb6d3eaad7c65231385bf28e5)](https://docs.openclaw.ai/)

![Image 3: US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

⌘K

*   [GitHub](https://github.com/openclaw/openclaw)
*   [Releases](https://github.com/openclaw/openclaw/releases)
*   [Discord](https://discord.com/invite/clawd)

Search...

Navigation

Automation

Cron Jobs

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Tools and Plugins](https://docs.openclaw.ai/tools)

##### Plugins

*   [Install and Configure](https://docs.openclaw.ai/tools/plugin)
*   [Community Plugins](https://docs.openclaw.ai/plugins/community)
*   [Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)
*   Building Plugins 
*   SDK Reference 

##### Skills

*   [Skills](https://docs.openclaw.ai/tools/skills)
*   [Creating Skills](https://docs.openclaw.ai/tools/creating-skills)
*   [Skills Config](https://docs.openclaw.ai/tools/skills-config)
*   [Slash Commands](https://docs.openclaw.ai/tools/slash-commands)
*   [ClawHub](https://docs.openclaw.ai/tools/clawhub)
*   [OpenProse](https://docs.openclaw.ai/prose)

##### Automation

*   [Hooks](https://docs.openclaw.ai/automation/hooks)
*   [Standing Orders](https://docs.openclaw.ai/automation/standing-orders)
*   [Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs)
*   [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)
*   [Automation Troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)
*   [Webhooks](https://docs.openclaw.ai/automation/webhook)
*   [Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub)
*   [Polls](https://docs.openclaw.ai/automation/poll)
*   [Auth Monitoring](https://docs.openclaw.ai/automation/auth-monitoring)

##### Tools

*   [apply_patch Tool](https://docs.openclaw.ai/tools/apply-patch)
*   Web Browser 
*   Web Tools 
*   [BTW Side Questions](https://docs.openclaw.ai/tools/btw)
*   [Diffs](https://docs.openclaw.ai/tools/diffs)
*   [Elevated Mode](https://docs.openclaw.ai/tools/elevated)
*   [Exec Tool](https://docs.openclaw.ai/tools/exec)
*   [Exec Approvals](https://docs.openclaw.ai/tools/exec-approvals)
*   [LLM Task](https://docs.openclaw.ai/tools/llm-task)
*   [Lobster](https://docs.openclaw.ai/tools/lobster)
*   [Tool-loop detection](https://docs.openclaw.ai/tools/loop-detection)
*   [PDF Tool](https://docs.openclaw.ai/tools/pdf)
*   [Reactions](https://docs.openclaw.ai/tools/reactions)
*   [Thinking Levels](https://docs.openclaw.ai/tools/thinking)

##### Agent coordination

*   [Agent Send](https://docs.openclaw.ai/tools/agent-send)
*   [Sub-Agents](https://docs.openclaw.ai/tools/subagents)
*   [ACP Agents](https://docs.openclaw.ai/tools/acp-agents)
*   [Multi-Agent Sandbox & Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools)

On this page

*   [Cron jobs (Gateway scheduler)](https://docs.openclaw.ai/automation/cron-jobs#cron-jobs-gateway-scheduler)
*   [TL;DR](https://docs.openclaw.ai/automation/cron-jobs#tldr)
*   [Quick start (actionable)](https://docs.openclaw.ai/automation/cron-jobs#quick-start-actionable)
*   [Tool-call equivalents (Gateway cron tool)](https://docs.openclaw.ai/automation/cron-jobs#tool-call-equivalents-gateway-cron-tool)
*   [Where cron jobs are stored](https://docs.openclaw.ai/automation/cron-jobs#where-cron-jobs-are-stored)
*   [Beginner-friendly overview](https://docs.openclaw.ai/automation/cron-jobs#beginner-friendly-overview)
*   [Concepts](https://docs.openclaw.ai/automation/cron-jobs#concepts)
*   [Jobs](https://docs.openclaw.ai/automation/cron-jobs#jobs)
*   [Schedules](https://docs.openclaw.ai/automation/cron-jobs#schedules)
*   [Main vs isolated execution](https://docs.openclaw.ai/automation/cron-jobs#main-vs-isolated-execution)
*   [Main session jobs (system events)](https://docs.openclaw.ai/automation/cron-jobs#main-session-jobs-system-events)
*   [Isolated jobs (dedicated cron sessions)](https://docs.openclaw.ai/automation/cron-jobs#isolated-jobs-dedicated-cron-sessions)
*   [Payload shapes (what runs)](https://docs.openclaw.ai/automation/cron-jobs#payload-shapes-what-runs)
*   [Announce delivery flow](https://docs.openclaw.ai/automation/cron-jobs#announce-delivery-flow)
*   [Webhook delivery flow](https://docs.openclaw.ai/automation/cron-jobs#webhook-delivery-flow)
*   [Model and thinking overrides](https://docs.openclaw.ai/automation/cron-jobs#model-and-thinking-overrides)
*   [Lightweight bootstrap context](https://docs.openclaw.ai/automation/cron-jobs#lightweight-bootstrap-context)
*   [Delivery (channel + target)](https://docs.openclaw.ai/automation/cron-jobs#delivery-channel-%2B-target)
*   [Telegram delivery targets (topics / forum threads)](https://docs.openclaw.ai/automation/cron-jobs#telegram-delivery-targets-topics-%2F-forum-threads)
*   [JSON schema for tool calls](https://docs.openclaw.ai/automation/cron-jobs#json-schema-for-tool-calls)
*   [cron.add params](https://docs.openclaw.ai/automation/cron-jobs#cron-add-params)
*   [cron.update params](https://docs.openclaw.ai/automation/cron-jobs#cron-update-params)
*   [cron.run and cron.remove params](https://docs.openclaw.ai/automation/cron-jobs#cron-run-and-cron-remove-params)
*   [Storage & history](https://docs.openclaw.ai/automation/cron-jobs#storage-%26-history)
*   [Retry policy](https://docs.openclaw.ai/automation/cron-jobs#retry-policy)
*   [Transient errors (retried)](https://docs.openclaw.ai/automation/cron-jobs#transient-errors-retried)
*   [Permanent errors (no retry)](https://docs.openclaw.ai/automation/cron-jobs#permanent-errors-no-retry)
*   [Default behavior (no config)](https://docs.openclaw.ai/automation/cron-jobs#default-behavior-no-config)
*   [Configuration](https://docs.openclaw.ai/automation/cron-jobs#configuration)
*   [Maintenance](https://docs.openclaw.ai/automation/cron-jobs#maintenance)
*   [Defaults](https://docs.openclaw.ai/automation/cron-jobs#defaults)
*   [How it works](https://docs.openclaw.ai/automation/cron-jobs#how-it-works)
*   [Performance caveat for high volume schedulers](https://docs.openclaw.ai/automation/cron-jobs#performance-caveat-for-high-volume-schedulers)
*   [Customize examples](https://docs.openclaw.ai/automation/cron-jobs#customize-examples)
*   [CLI quickstart](https://docs.openclaw.ai/automation/cron-jobs#cli-quickstart)
*   [Gateway API surface](https://docs.openclaw.ai/automation/cron-jobs#gateway-api-surface)
*   [Troubleshooting](https://docs.openclaw.ai/automation/cron-jobs#troubleshooting)
*   [”Nothing runs”](https://docs.openclaw.ai/automation/cron-jobs#%E2%80%9Dnothing-runs%E2%80%9D)
*   [A recurring job keeps delaying after failures](https://docs.openclaw.ai/automation/cron-jobs#a-recurring-job-keeps-delaying-after-failures)
*   [Telegram delivers to the wrong place](https://docs.openclaw.ai/automation/cron-jobs#telegram-delivers-to-the-wrong-place)
*   [Subagent announce delivery retries](https://docs.openclaw.ai/automation/cron-jobs#subagent-announce-delivery-retries)

Automation

# Cron Jobs

# [​](https://docs.openclaw.ai/automation/cron-jobs#cron-jobs-gateway-scheduler)

Cron jobs (Gateway scheduler)

> **Cron vs Heartbeat?** See [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat) for guidance on when to use each.

Cron is the Gateway’s built-in scheduler. It persists jobs, wakes the agent at the right time, and can optionally deliver output back to a chat.If you want _“run this every morning”_ or _“poke the agent in 20 minutes”_, cron is the mechanism.Troubleshooting: [/automation/troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)
## [​](https://docs.openclaw.ai/automation/cron-jobs#tldr)

TL;DR

*   Cron runs **inside the Gateway** (not inside the model).
*   Jobs persist under `~/.openclaw/cron/` so restarts don’t lose schedules.
*   Two execution styles:
    *   **Main session**: enqueue a system event, then run on the next heartbeat.
    *   **Isolated**: run a dedicated agent turn in `cron:<jobId>` or a custom session, with delivery (announce by default or none).
    *   **Current session**: bind to the session where the cron is created (`sessionTarget: "current"`).
    *   **Custom session**: run in a persistent named session (`sessionTarget: "session:custom-id"`).

*   Wakeups are first-class: a job can request “wake now” vs “next heartbeat”.
*   Webhook posting is per job via `delivery.mode = "webhook"` + `delivery.to = "<url>"`.
*   Legacy fallback remains for stored jobs with `notify: true` when `cron.webhook` is set, migrate those jobs to webhook delivery mode.
*   For upgrades, `openclaw doctor --fix` can normalize legacy cron store fields before the scheduler touches them.

## [​](https://docs.openclaw.ai/automation/cron-jobs#quick-start-actionable)

Quick start (actionable)

Create a one-shot reminder, verify it exists, and run it immediately:

```
openclaw cron add \
  --name "Reminder" \
  --at "2026-02-01T16:00:00Z" \
  --session main \
  --system-event "Reminder: check the cron docs draft" \
  --wake now \
  --delete-after-run

openclaw cron list
openclaw cron run <job-id>
openclaw cron runs --id <job-id>
```

Schedule a recurring isolated job with delivery:

```
openclaw cron add \
  --name "Morning brief" \
  --cron "0 7 * * *" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Summarize overnight updates." \
  --announce \
  --channel slack \
  --to "channel:C1234567890"
```

## [​](https://docs.openclaw.ai/automation/cron-jobs#tool-call-equivalents-gateway-cron-tool)

Tool-call equivalents (Gateway cron tool)

For the canonical JSON shapes and examples, see [JSON schema for tool calls](https://docs.openclaw.ai/automation/cron-jobs#json-schema-for-tool-calls).
## [​](https://docs.openclaw.ai/automation/cron-jobs#where-cron-jobs-are-stored)

Where cron jobs are stored

Cron jobs are persisted on the Gateway host at `~/.openclaw/cron/jobs.json` by default. The Gateway loads the file into memory and writes it back on changes, so manual edits are only safe when the Gateway is stopped. Prefer `openclaw cron add/edit` or the cron tool call API for changes.
## [​](https://docs.openclaw.ai/automation/cron-jobs#beginner-friendly-overview)

Beginner-friendly overview

Think of a cron job as: **when** to run + **what** to do.
1.   **Choose a schedule**
    *   One-shot reminder → `schedule.kind = "at"` (CLI: `--at`)
    *   Repeating job → `schedule.kind = "every"` or `schedule.kind = "cron"`
    *   If your ISO timestamp omits a timezone, it is treated as **UTC**.

2.   **Choose where it runs**
    *   `sessionTarget: "main"` → run during the next heartbeat with main context.
    *   `sessionTarget: "isolated"` → run a dedicated agent turn in `cron:<jobId>`.
    *   `sessionTarget: "current"` → bind to the current session (resolved at creation time to `session:<sessionKey>`).
    *   `sessionTarget: "session:custom-id"` → run in a persistent named session that maintains context across runs.

Default behavior (unchanged):
    *   `systemEvent` payloads default to `main`
    *   `agentTurn` payloads default to `isolated`

To use current session binding, explicitly set `sessionTarget: "current"`.
3.   **Choose the payload**
    *   Main session → `payload.kind = "systemEvent"`
    *   Isolated session → `payload.kind = "agentTurn"`

Optional: one-shot jobs (`schedule.kind = "at"`) delete after success by default. Set `deleteAfterRun: false` to keep them (they will disable after success).
## [​](https://docs.openclaw.ai/automation/cron-jobs#concepts)

Concepts

### [​](https://docs.openclaw.ai/automation/cron-jobs#jobs)

Jobs

A cron job is a stored record with:
*   a **schedule** (when it should run),
*   a **payload** (what it should do),
*   optional **delivery mode** (`announce`, `webhook`, or `none`).
*   optional **agent binding** (`agentId`): run the job under a specific agent; if missing or unknown, the gateway falls back to the default agent.

Jobs are identified by a stable `jobId` (used by CLI/Gateway APIs). In agent tool calls, `jobId` is canonical; legacy `id` is accepted for compatibility. One-shot jobs auto-delete after success by default; set `deleteAfterRun: false` to keep them.
### [​](https://docs.openclaw.ai/automation/cron-jobs#schedules)

Schedules

Cron supports three schedule kinds:
*   `at`: one-shot timestamp via `schedule.at` (ISO 8601).
*   `every`: fixed interval (ms).
*   `cron`: 5-field cron expression (or 6-field with seconds) with optional IANA timezone.

Cron expressions use `croner`. If a timezone is omitted, the Gateway host’s local timezone is used.To reduce top-of-hour load spikes across many gateways, OpenClaw applies a deterministic per-job stagger window of up to 5 minutes for recurring top-of-hour expressions (for example `0 * * * *`, `0 */2 * * *`). Fixed-hour expressions such as `0 7 * * *` remain exact.For any cron schedule, you can set an explicit stagger window with `schedule.staggerMs` (`0` keeps exact timing). CLI shortcuts:
*   `--stagger 30s` (or `1m`, `5m`) to set an explicit stagger window.
*   `--exact` to force `staggerMs = 0`.

### [​](https://docs.openclaw.ai/automation/cron-jobs#main-vs-isolated-execution)

Main vs isolated execution

#### [​](https://docs.openclaw.ai/automation/cron-jobs#main-session-jobs-system-events)

Main session jobs (system events)

Main jobs enqueue a system event and optionally wake the heartbeat runner. They must use `payload.kind = "systemEvent"`.
*   `wakeMode: "now"` (default): event triggers an immediate heartbeat run.
*   `wakeMode: "next-heartbeat"`: event waits for the next scheduled heartbeat.

This is the best fit when you want the normal heartbeat prompt + main-session context. See [Heartbeat](https://docs.openclaw.ai/gateway/heartbeat).
#### [​](https://docs.openclaw.ai/automation/cron-jobs#isolated-jobs-dedicated-cron-sessions)

Isolated jobs (dedicated cron sessions)

Isolated jobs run a dedicated agent turn in session `cron:<jobId>` or a custom session.Key behaviors:
*   Prompt is prefixed with `[cron:<jobId> <job name>]` for traceability.
*   Each run starts a **fresh session id** (no prior conversation carry-over), unless using a custom session.
*   Custom sessions (`session:xxx`) persist context across runs, enabling workflows like daily standups that build on previous summaries.
*   Default behavior: if `delivery` is omitted, isolated jobs announce a summary (`delivery.mode = "announce"`).
*   `delivery.mode` chooses what happens:
    *   `announce`: deliver a summary to the target channel and post a brief summary to the main session.
    *   `webhook`: POST the finished event payload to `delivery.to` when the finished event includes a summary.
    *   `none`: internal only (no delivery, no main-session summary).

*   `wakeMode` controls when the main-session summary posts:
    *   `now`: immediate heartbeat.
    *   `next-heartbeat`: waits for the next scheduled heartbeat.

Use isolated jobs for noisy, frequent, or “background chores” that shouldn’t spam your main chat history.
### [​](https://docs.openclaw.ai/automation/cron-jobs#payload-shapes-what-runs)

Payload shapes (what runs)

Two payload kinds are supported:
*   `systemEvent`: main-session only, routed through the heartbeat prompt.
*   `agentTurn`: isolated-session only, runs a dedicated agent turn.

Common `agentTurn` fields:
*   `message`: required text prompt.
*   `model` / `thinking`: optional overrides (see below).
*   `timeoutSeconds`: optional timeout override.
*   `lightContext`: optional lightweight bootstrap mode for jobs that do not need workspace bootstrap file injection.

Delivery config:
*   `delivery.mode`: `none` | `announce` | `webhook`.
*   `delivery.channel`: `last` or a specific channel.
*   `delivery.to`: channel-specific target (announce) or webhook URL (webhook mode).
*   `delivery.bestEffort`: avoid failing the job if announce delivery fails.

Announce delivery suppresses messaging tool sends for the run; use `delivery.channel`/`delivery.to` to target the chat instead. When `delivery.mode = "none"`, no summary is posted to the main session.If `delivery` is omitted for isolated jobs, OpenClaw defaults to `announce`.
#### [​](https://docs.openclaw.ai/automation/cron-jobs#announce-delivery-flow)

Announce delivery flow

When `delivery.mode = "announce"`, cron delivers directly via the outbound channel adapters. The main agent is not spun up to craft or forward the message.Behavior details:
*   Content: delivery uses the isolated run’s outbound payloads (text/media) with normal chunking and channel formatting.
*   Heartbeat-only responses (`HEARTBEAT_OK` with no real content) are not delivered.
*   If the isolated run already sent a message to the same target via the message tool, delivery is skipped to avoid duplicates.
*   Missing or invalid delivery targets fail the job unless `delivery.bestEffort = true`.
*   A short summary is posted to the main session only when `delivery.mode = "announce"`.
*   The main-session summary respects `wakeMode`: `now` triggers an immediate heartbeat and `next-heartbeat` waits for the next scheduled heartbeat.

#### [​](https://docs.openclaw.ai/automation/cron-jobs#webhook-delivery-flow)

Webhook delivery flow

When `delivery.mode = "webhook"`, cron posts the finished event payload to `delivery.to` when the finished event includes a summary.Behavior details:
*   The endpoint must be a valid HTTP(S) URL.
*   No channel delivery is attempted in webhook mode.
*   No main-session summary is posted in webhook mode.
*   If `cron.webhookToken` is set, auth header is `Authorization: Bearer <cron.webhookToken>`.
*   Deprecated fallback: stored legacy jobs with `notify: true` still post to `cron.webhook` (if configured), with a warning so you can migrate to `delivery.mode = "webhook"`.

### [​](https://docs.openclaw.ai/automation/cron-jobs#model-and-thinking-overrides)

Model and thinking overrides

Isolated jobs (`agentTurn`) can override the model and thinking level:
*   `model`: Provider/model string (e.g., `anthropic/claude-sonnet-4-20250514`) or alias (e.g., `opus`)
*   `thinking`: Thinking level (`off`, `minimal`, `low`, `medium`, `high`, `xhigh`; GPT-5.2 + Codex models only)

Note: You can set `model` on main-session jobs too, but it changes the shared main session model. We recommend model overrides only for isolated jobs to avoid unexpected context shifts.Resolution priority:
1.   Job payload override (highest)
2.   Hook-specific defaults (e.g., `hooks.gmail.model`)
3.   Agent config default

### [​](https://docs.openclaw.ai/automation/cron-jobs#lightweight-bootstrap-context)

Lightweight bootstrap context

Isolated jobs (`agentTurn`) can set `lightContext: true` to run with lightweight bootstrap context.
*   Use this for scheduled chores that do not need workspace bootstrap file injection.
*   In practice, the embedded runtime runs with `bootstrapContextMode: "lightweight"`, which keeps cron bootstrap context empty on purpose.
*   CLI equivalents: `openclaw cron add --light-context ...` and `openclaw cron edit --light-context`.

### [​](https://docs.openclaw.ai/automation/cron-jobs#delivery-channel-+-target)

Delivery (channel + target)

Isolated jobs can deliver output to a channel via the top-level `delivery` config:
*   `delivery.mode`: `announce` (channel delivery), `webhook` (HTTP POST), or `none`.
*   `delivery.channel`: `whatsapp` / `telegram` / `discord` / `slack` / `signal` / `imessage` / `irc` / `googlechat` / `line` / `last`, plus extension channels like `msteams` / `mattermost` (plugins).
*   `delivery.to`: channel-specific recipient target.

`announce` delivery is only valid for isolated jobs (`sessionTarget: "isolated"`). `webhook` delivery is valid for both main and isolated jobs.If `delivery.channel` or `delivery.to` is omitted, cron can fall back to the main session’s “last route” (the last place the agent replied).Target format reminders:
*   Slack/Discord/Mattermost (plugin) targets should use explicit prefixes (e.g. `channel:<id>`, `user:<id>`) to avoid ambiguity. Mattermost bare 26-char IDs are resolved **user-first** (DM if user exists, channel otherwise) — use `user:<id>` or `channel:<id>` for deterministic routing.
*   Telegram topics should use the `:topic:` form (see below).

#### [​](https://docs.openclaw.ai/automation/cron-jobs#telegram-delivery-targets-topics-/-forum-threads)

Telegram delivery targets (topics / forum threads)

Telegram supports forum topics via `message_thread_id`. For cron delivery, you can encode the topic/thread into the `to` field:
*   `-1001234567890` (chat id only)
*   `-1001234567890:topic:123` (preferred: explicit topic marker)
*   `-1001234567890:123` (shorthand: numeric suffix)

Prefixed targets like `telegram:...` / `telegram:group:...` are also accepted:
*   `telegram:group:-1001234567890:topic:123`

## [​](https://docs.openclaw.ai/automation/cron-jobs#json-schema-for-tool-calls)

JSON schema for tool calls

Use these shapes when calling Gateway `cron.*` tools directly (agent tool calls or RPC). CLI flags accept human durations like `20m`, but tool calls should use an ISO 8601 string for `schedule.at` and milliseconds for `schedule.everyMs`.
### [​](https://docs.openclaw.ai/automation/cron-jobs#cron-add-params)

cron.add params

One-shot, main session job (system event):

```
{
  "name": "Reminder",
  "schedule": { "kind": "at", "at": "2026-02-01T16:00:00Z" },
  "sessionTarget": "main",
  "wakeMode": "now",
  "payload": { "kind": "systemEvent", "text": "Reminder text" },
  "deleteAfterRun": true
}
```

Recurring, isolated job with delivery:

```
{
  "name": "Morning brief",
  "schedule": { "kind": "cron", "expr": "0 7 * * *", "tz": "America/Los_Angeles" },
  "sessionTarget": "isolated",
  "wakeMode": "next-heartbeat",
  "payload": {
    "kind": "agentTurn",
    "message": "Summarize overnight updates.",
    "lightContext": true
  },
  "delivery": {
    "mode": "announce",
    "channel": "slack",
    "to": "channel:C1234567890",
    "bestEffort": true
  }
}
```

Recurring job bound to current session (auto-resolved at creation):

```
{
  "name": "Daily standup",
  "schedule": { "kind": "cron", "expr": "0 9 * * *" },
  "sessionTarget": "current",
  "payload": {
    "kind": "agentTurn",
    "message": "Summarize yesterday's progress."
  }
}
```

Recurring job in a custom persistent session:

```
{
  "name": "Project monitor",
  "schedule": { "kind": "every", "everyMs": 300000 },
  "sessionTarget": "session:project-alpha-monitor",
  "payload": {
    "kind": "agentTurn",
    "message": "Check project status and update the running log."
  }
}
```

Notes:
*   `schedule.kind`: `at` (`at`), `every` (`everyMs`), or `cron` (`expr`, optional `tz`).
*   `schedule.at` accepts ISO 8601. Tool/API values without a timezone are treated as UTC; the CLI also accepts `openclaw cron add|edit --at "<offset-less-iso>" --tz <iana>` for local wall-clock one-shots.
*   `everyMs` is milliseconds.
*   `sessionTarget`: `"main"`, `"isolated"`, `"current"`, or `"session:<custom-id>"`.
*   `"current"` is resolved to `"session:<sessionKey>"` at creation time.
*   Custom sessions (`session:xxx`) maintain persistent context across runs.
*   Optional fields: `agentId`, `description`, `enabled`, `deleteAfterRun` (defaults to true for `at`), `delivery`.
*   `wakeMode` defaults to `"now"` when omitted.

### [​](https://docs.openclaw.ai/automation/cron-jobs#cron-update-params)

cron.update params

```
{
  "jobId": "job-123",
  "patch": {
    "enabled": false,
    "schedule": { "kind": "every", "everyMs": 3600000 }
  }
}
```

Notes:
*   `jobId` is canonical; `id` is accepted for compatibility.
*   Use `agentId: null` in the patch to clear an agent binding.

### [​](https://docs.openclaw.ai/automation/cron-jobs#cron-run-and-cron-remove-params)

cron.run and cron.remove params

```
{ "jobId": "job-123", "mode": "force" }
```

```
{ "jobId": "job-123" }
```

## [​](https://docs.openclaw.ai/automation/cron-jobs#storage-&-history)

Storage & history

*   Job store: `~/.openclaw/cron/jobs.json` (Gateway-managed JSON).
*   Run history: `~/.openclaw/cron/runs/<jobId>.jsonl` (JSONL, auto-pruned by size and line count).
*   Isolated cron run sessions in `sessions.json` are pruned by `cron.sessionRetention` (default `24h`; set `false` to disable).
*   Override store path: `cron.store` in config.

## [​](https://docs.openclaw.ai/automation/cron-jobs#retry-policy)

Retry policy

When a job fails, OpenClaw classifies errors as **transient** (retryable) or **permanent** (disable immediately).
### [​](https://docs.openclaw.ai/automation/cron-jobs#transient-errors-retried)

Transient errors (retried)

*   Rate limit (429, too many requests, resource exhausted)
*   Provider overload (for example Anthropic `529 overloaded_error`, overload fallback summaries)
*   Network errors (timeout, ECONNRESET, fetch failed, socket)
*   Server errors (5xx)
*   Cloudflare-related errors

### [​](https://docs.openclaw.ai/automation/cron-jobs#permanent-errors-no-retry)

Permanent errors (no retry)

*   Auth failures (invalid API key, unauthorized)
*   Config or validation errors
*   Other non-transient errors

### [​](https://docs.openclaw.ai/automation/cron-jobs#default-behavior-no-config)

Default behavior (no config)

**One-shot jobs (`schedule.kind: "at"`):**
*   On transient error: retry up to 3 times with exponential backoff (30s → 1m → 5m).
*   On permanent error: disable immediately.
*   On success or skip: disable (or delete if `deleteAfterRun: true`).

**Recurring jobs (`cron` / `every`):**
*   On any error: apply exponential backoff (30s → 1m → 5m → 15m → 60m) before the next scheduled run.
*   Job stays enabled; backoff resets after the next successful run.

Configure `cron.retry` to override these defaults (see [Configuration](https://docs.openclaw.ai/automation/cron-jobs#configuration)).
## [​](https://docs.openclaw.ai/automation/cron-jobs#configuration)

Configuration

```
{
  cron: {
    enabled: true, // default true
    store: "~/.openclaw/cron/jobs.json",
    maxConcurrentRuns: 1, // default 1
    // Optional: override retry policy for one-shot jobs
    retry: {
      maxAttempts: 3,
      backoffMs: [60000, 120000, 300000],
      retryOn: ["rate_limit", "overloaded", "network", "server_error"],
    },
    webhook: "https://example.invalid/legacy", // deprecated fallback for stored notify:true jobs
    webhookToken: "replace-with-dedicated-webhook-token", // optional bearer token for webhook mode
    sessionRetention: "24h", // duration string or false
    runLog: {
      maxBytes: "2mb", // default 2_000_000 bytes
      keepLines: 2000, // default 2000
    },
  },
}
```

Run-log pruning behavior:
*   `cron.runLog.maxBytes`: max run-log file size before pruning.
*   `cron.runLog.keepLines`: when pruning, keep only the newest N lines.
*   Both apply to `cron/runs/<jobId>.jsonl` files.

Webhook behavior:
*   Preferred: set `delivery.mode: "webhook"` with `delivery.to: "https://..."` per job.
*   Webhook URLs must be valid `http://` or `https://` URLs.
*   When posted, payload is the cron finished event JSON.
*   If `cron.webhookToken` is set, auth header is `Authorization: Bearer <cron.webhookToken>`.
*   If `cron.webhookToken` is not set, no `Authorization` header is sent.
*   Deprecated fallback: stored legacy jobs with `notify: true` still use `cron.webhook` when present.

Disable cron entirely:
*   `cron.enabled: false` (config)
*   `OPENCLAW_SKIP_CRON=1` (env)

## [​](https://docs.openclaw.ai/automation/cron-jobs#maintenance)

Maintenance

Cron has two built-in maintenance paths: isolated run-session retention and run-log pruning.
### [​](https://docs.openclaw.ai/automation/cron-jobs#defaults)

Defaults

*   `cron.sessionRetention`: `24h` (set `false` to disable run-session pruning)
*   `cron.runLog.maxBytes`: `2_000_000` bytes
*   `cron.runLog.keepLines`: `2000`

### [​](https://docs.openclaw.ai/automation/cron-jobs#how-it-works)

How it works

*   Isolated runs create session entries (`...:cron:<jobId>:run:<uuid>`) and transcript files.
*   The reaper removes expired run-session entries older than `cron.sessionRetention`.
*   For removed run sessions no longer referenced by the session store, OpenClaw archives transcript files and purges old deleted archives on the same retention window.
*   After each run append, `cron/runs/<jobId>.jsonl` is size-checked:
    *   if file size exceeds `runLog.maxBytes`, it is trimmed to the newest `runLog.keepLines` lines.

### [​](https://docs.openclaw.ai/automation/cron-jobs#performance-caveat-for-high-volume-schedulers)

Performance caveat for high volume schedulers

High-frequency cron setups can generate large run-session and run-log footprints. Maintenance is built in, but loose limits can still create avoidable IO and cleanup work.What to watch:
*   long `cron.sessionRetention` windows with many isolated runs
*   high `cron.runLog.keepLines` combined with large `runLog.maxBytes`
*   many noisy recurring jobs writing to the same `cron/runs/<jobId>.jsonl`

What to do:
*   keep `cron.sessionRetention` as short as your debugging/audit needs allow
*   keep run logs bounded with moderate `runLog.maxBytes` and `runLog.keepLines`
*   move noisy background jobs to isolated mode with delivery rules that avoid unnecessary chatter
*   review growth periodically with `openclaw cron runs` and adjust retention before logs become large

### [​](https://docs.openclaw.ai/automation/cron-jobs#customize-examples)

Customize examples

Keep run sessions for a week and allow bigger run logs:

```
{
  cron: {
    sessionRetention: "7d",
    runLog: {
      maxBytes: "10mb",
      keepLines: 5000,
    },
  },
}
```

Disable isolated run-session pruning but keep run-log pruning:

```
{
  cron: {
    sessionRetention: false,
    runLog: {
      maxBytes: "5mb",
      keepLines: 3000,
    },
  },
}
```

Tune for high-volume cron usage (example):

```
{
  cron: {
    sessionRetention: "12h",
    runLog: {
      maxBytes: "3mb",
      keepLines: 1500,
    },
  },
}
```

## [​](https://docs.openclaw.ai/automation/cron-jobs#cli-quickstart)

CLI quickstart

One-shot reminder (UTC ISO, auto-delete after success):

```
openclaw cron add \
  --name "Send reminder" \
  --at "2026-01-12T18:00:00Z" \
  --session main \
  --system-event "Reminder: submit expense report." \
  --wake now \
  --delete-after-run
```

One-shot reminder (main session, wake immediately):

```
openclaw cron add \
  --name "Calendar check" \
  --at "20m" \
  --session main \
  --system-event "Next heartbeat: check calendar." \
  --wake now
```

Recurring isolated job (announce to WhatsApp):

```
openclaw cron add \
  --name "Morning status" \
  --cron "0 7 * * *" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Summarize inbox + calendar for today." \
  --announce \
  --channel whatsapp \
  --to "+15551234567"
```

Recurring cron job with explicit 30-second stagger:

```
openclaw cron add \
  --name "Minute watcher" \
  --cron "0 * * * * *" \
  --tz "UTC" \
  --stagger 30s \
  --session isolated \
  --message "Run minute watcher checks." \
  --announce
```

Recurring isolated job (deliver to a Telegram topic):

```
openclaw cron add \
  --name "Nightly summary (topic)" \
  --cron "0 22 * * *" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Summarize today; send to the nightly topic." \
  --announce \
  --channel telegram \
  --to "-1001234567890:topic:123"
```

Isolated job with model and thinking override:

```
openclaw cron add \
  --name "Deep analysis" \
  --cron "0 6 * * 1" \
  --tz "America/Los_Angeles" \
  --session isolated \
  --message "Weekly deep analysis of project progress." \
  --model "opus" \
  --thinking high \
  --announce \
  --channel whatsapp \
  --to "+15551234567"
```

Agent selection (multi-agent setups):

```
# Pin a job to agent "ops" (falls back to default if that agent is missing)
openclaw cron add --name "Ops sweep" --cron "0 6 * * *" --session isolated --message "Check ops queue" --agent ops

# Switch or clear the agent on an existing job
openclaw cron edit <jobId> --agent ops
openclaw cron edit <jobId> --clear-agent
```

Manual run (force is the default, use `--due` to only run when due):

```
openclaw cron run <jobId>
openclaw cron run <jobId> --due
```

`cron.run` now acknowledges once the manual run is queued, not after the job finishes. Successful queue responses look like `{ ok: true, enqueued: true, runId }`. If the job is already running or `--due` finds nothing due, the response stays `{ ok: true, ran: false, reason }`. Use `openclaw cron runs --id <jobId>` or the `cron.runs` gateway method to inspect the eventual finished entry.Edit an existing job (patch fields):

```
openclaw cron edit <jobId> \
  --message "Updated prompt" \
  --model "opus" \
  --thinking low
```

Force an existing cron job to run exactly on schedule (no stagger):

```
openclaw cron edit <jobId> --exact
```

Run history:

```
openclaw cron runs --id <jobId> --limit 50
```

Immediate system event without creating a job:

```
openclaw system event --mode now --text "Next heartbeat: check battery."
```

## [​](https://docs.openclaw.ai/automation/cron-jobs#gateway-api-surface)

Gateway API surface

*   `cron.list`, `cron.status`, `cron.add`, `cron.update`, `cron.remove`
*   `cron.run` (force or due), `cron.runs` For immediate system events without a job, use [`openclaw system event`](https://docs.openclaw.ai/cli/system).

## [​](https://docs.openclaw.ai/automation/cron-jobs#troubleshooting)

Troubleshooting

### [​](https://docs.openclaw.ai/automation/cron-jobs#%E2%80%9Dnothing-runs%E2%80%9D)

”Nothing runs”

*   Check cron is enabled: `cron.enabled` and `OPENCLAW_SKIP_CRON`.
*   Check the Gateway is running continuously (cron runs inside the Gateway process).
*   For `cron` schedules: confirm timezone (`--tz`) vs the host timezone.

### [​](https://docs.openclaw.ai/automation/cron-jobs#a-recurring-job-keeps-delaying-after-failures)

A recurring job keeps delaying after failures

*   OpenClaw applies exponential retry backoff for recurring jobs after consecutive errors: 30s, 1m, 5m, 15m, then 60m between retries.
*   Backoff resets automatically after the next successful run.
*   One-shot (`at`) jobs retry transient errors (rate limit, overloaded, network, server_error) up to 3 times with backoff; permanent errors disable immediately. See [Retry policy](https://docs.openclaw.ai/automation/cron-jobs#retry-policy).

### [​](https://docs.openclaw.ai/automation/cron-jobs#telegram-delivers-to-the-wrong-place)

Telegram delivers to the wrong place

*   For forum topics, use `-100…:topic:<id>` so it’s explicit and unambiguous.
*   If you see `telegram:...` prefixes in logs or stored “last route” targets, that’s normal; cron delivery accepts them and still parses topic IDs correctly.

### [​](https://docs.openclaw.ai/automation/cron-jobs#subagent-announce-delivery-retries)

Subagent announce delivery retries

*   When a subagent run completes, the gateway announces the result to the requester session.
*   If the announce flow returns `false` (e.g. requester session is busy), the gateway retries up to 3 times with tracking via `announceRetryCount`.
*   Announces older than 5 minutes past `endedAt` are force-expired to prevent stale entries from looping indefinitely.
*   If you see repeated announce deliveries in logs, check the subagent registry for entries with high `announceRetryCount` values.

[Standing Orders](https://docs.openclaw.ai/automation/standing-orders)[Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
