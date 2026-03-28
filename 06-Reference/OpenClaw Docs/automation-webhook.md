# automation/webhook

Source: https://docs.openclaw.ai/automation/webhook

Title: Webhooks - OpenClaw

URL Source: https://docs.openclaw.ai/automation/webhook

Markdown Content:
# Webhooks - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/webhook#content-area)

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

Webhooks

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

*   [Webhooks](https://docs.openclaw.ai/automation/webhook#webhooks)
*   [Enable](https://docs.openclaw.ai/automation/webhook#enable)
*   [Auth](https://docs.openclaw.ai/automation/webhook#auth)
*   [Endpoints](https://docs.openclaw.ai/automation/webhook#endpoints)
*   [POST /hooks/wake](https://docs.openclaw.ai/automation/webhook#post-%2Fhooks%2Fwake)
*   [POST /hooks/agent](https://docs.openclaw.ai/automation/webhook#post-%2Fhooks%2Fagent)
*   [Session key policy (breaking change)](https://docs.openclaw.ai/automation/webhook#session-key-policy-breaking-change)
*   [POST /hooks/<name> (mapped)](https://docs.openclaw.ai/automation/webhook#post-%2Fhooks%2F%3Cname%3E-mapped)
*   [Responses](https://docs.openclaw.ai/automation/webhook#responses)
*   [Examples](https://docs.openclaw.ai/automation/webhook#examples)
*   [Use a different model](https://docs.openclaw.ai/automation/webhook#use-a-different-model)
*   [Security](https://docs.openclaw.ai/automation/webhook#security)

Automation

# Webhooks

# [​](https://docs.openclaw.ai/automation/webhook#webhooks)

Webhooks

Gateway can expose a small HTTP webhook endpoint for external triggers.
## [​](https://docs.openclaw.ai/automation/webhook#enable)

Enable

```
{
  hooks: {
    enabled: true,
    token: "shared-secret",
    path: "/hooks",
    // Optional: restrict explicit `agentId` routing to this allowlist.
    // Omit or include "*" to allow any agent.
    // Set [] to deny all explicit `agentId` routing.
    allowedAgentIds: ["hooks", "main"],
  },
}
```

Notes:
*   `hooks.token` is required when `hooks.enabled=true`.
*   `hooks.path` defaults to `/hooks`.

## [​](https://docs.openclaw.ai/automation/webhook#auth)

Auth

Every request must include the hook token. Prefer headers:
*   `Authorization: Bearer <token>` (recommended)
*   `x-openclaw-token: <token>`
*   Query-string tokens are rejected (`?token=...` returns `400`).
*   Treat `hooks.token` holders as full-trust callers for the hook ingress surface on that gateway. Hook payload content is still untrusted, but this is not a separate non-owner auth boundary.

## [​](https://docs.openclaw.ai/automation/webhook#endpoints)

Endpoints

### [​](https://docs.openclaw.ai/automation/webhook#post-/hooks/wake)

`POST /hooks/wake`

Payload:

```
{ "text": "System line", "mode": "now" }
```

*   `text`**required** (string): The description of the event (e.g., “New email received”).
*   `mode` optional (`now` | `next-heartbeat`): Whether to trigger an immediate heartbeat (default `now`) or wait for the next periodic check.

Effect:
*   Enqueues a system event for the **main** session
*   If `mode=now`, triggers an immediate heartbeat

### [​](https://docs.openclaw.ai/automation/webhook#post-/hooks/agent)

`POST /hooks/agent`

Payload:

```
{
  "message": "Run this",
  "name": "Email",
  "agentId": "hooks",
  "sessionKey": "hook:email:msg-123",
  "wakeMode": "now",
  "deliver": true,
  "channel": "last",
  "to": "+15551234567",
  "model": "openai/gpt-5.2-mini",
  "thinking": "low",
  "timeoutSeconds": 120
}
```

*   `message`**required** (string): The prompt or message for the agent to process.
*   `name` optional (string): Human-readable name for the hook (e.g., “GitHub”), used as a prefix in session summaries.
*   `agentId` optional (string): Route this hook to a specific agent. Unknown IDs fall back to the default agent. When set, the hook runs using the resolved agent’s workspace and configuration.
*   `sessionKey` optional (string): The key used to identify the agent’s session. By default this field is rejected unless `hooks.allowRequestSessionKey=true`.
*   `wakeMode` optional (`now` | `next-heartbeat`): Whether to trigger an immediate heartbeat (default `now`) or wait for the next periodic check.
*   `deliver` optional (boolean): If `true`, the agent’s response will be sent to the messaging channel. Defaults to `true`. Responses that are only heartbeat acknowledgments are automatically skipped.
*   `channel` optional (string): The messaging channel for delivery. Core channels: `last`, `whatsapp`, `telegram`, `discord`, `slack`, `signal`, `imessage`, `irc`, `googlechat`, `line`. Extension channels (plugins): `msteams`, `mattermost`, and others. Defaults to `last`.
*   `to` optional (string): The recipient identifier for the channel (e.g., phone number for WhatsApp/Signal, chat ID for Telegram, channel ID for Discord/Slack/Mattermost (plugin), conversation ID for Microsoft Teams). Defaults to the last recipient in the main session.
*   `model` optional (string): Model override (e.g., `anthropic/claude-sonnet-4-6` or an alias). Must be in the allowed model list if restricted.
*   `thinking` optional (string): Thinking level override (e.g., `low`, `medium`, `high`).
*   `timeoutSeconds` optional (number): Maximum duration for the agent run in seconds.

Effect:
*   Runs an **isolated** agent turn (own session key)
*   Always posts a summary into the **main** session
*   If `wakeMode=now`, triggers an immediate heartbeat

## [​](https://docs.openclaw.ai/automation/webhook#session-key-policy-breaking-change)

Session key policy (breaking change)

`/hooks/agent` payload `sessionKey` overrides are disabled by default.
*   Recommended: set a fixed `hooks.defaultSessionKey` and keep request overrides off.
*   Optional: allow request overrides only when needed, and restrict prefixes.

Recommended config:

```
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    defaultSessionKey: "hook:ingress",
    allowRequestSessionKey: false,
    allowedSessionKeyPrefixes: ["hook:"],
  },
}
```

Compatibility config (legacy behavior):

```
{
  hooks: {
    enabled: true,
    token: "${OPENCLAW_HOOKS_TOKEN}",
    allowRequestSessionKey: true,
    allowedSessionKeyPrefixes: ["hook:"], // strongly recommended
  },
}
```

### [​](https://docs.openclaw.ai/automation/webhook#post-/hooks/%3Cname%3E-mapped)

`POST /hooks/<name>` (mapped)

Custom hook names are resolved via `hooks.mappings` (see configuration). A mapping can turn arbitrary payloads into `wake` or `agent` actions, with optional templates or code transforms.Mapping options (summary):
*   `hooks.presets: ["gmail"]` enables the built-in Gmail mapping.
*   `hooks.mappings` lets you define `match`, `action`, and templates in config.
*   `hooks.transformsDir` + `transform.module` loads a JS/TS module for custom logic.
    *   `hooks.transformsDir` (if set) must stay within the transforms root under your OpenClaw config directory (typically `~/.openclaw/hooks/transforms`).
    *   `transform.module` must resolve within the effective transforms directory (traversal/escape paths are rejected).

*   Use `match.source` to keep a generic ingest endpoint (payload-driven routing).
*   TS transforms require a TS loader (e.g. `bun` or `tsx`) or precompiled `.js` at runtime.
*   Set `deliver: true` + `channel`/`to` on mappings to route replies to a chat surface (`channel` defaults to `last` and falls back to WhatsApp).
*   `agentId` routes the hook to a specific agent; unknown IDs fall back to the default agent.
*   `hooks.allowedAgentIds` restricts explicit `agentId` routing. Omit it (or include `*`) to allow any agent. Set `[]` to deny explicit `agentId` routing.
*   `hooks.defaultSessionKey` sets the default session for hook agent runs when no explicit key is provided.
*   `hooks.allowRequestSessionKey` controls whether `/hooks/agent` payloads may set `sessionKey` (default: `false`).
*   `hooks.allowedSessionKeyPrefixes` optionally restricts explicit `sessionKey` values from request payloads and mappings.
*   `allowUnsafeExternalContent: true` disables the external content safety wrapper for that hook (dangerous; only for trusted internal sources).
*   `openclaw webhooks gmail setup` writes `hooks.gmail` config for `openclaw webhooks gmail run`. See [Gmail Pub/Sub](https://docs.openclaw.ai/automation/gmail-pubsub) for the full Gmail watch flow.

## [​](https://docs.openclaw.ai/automation/webhook#responses)

Responses

*   `200` for `/hooks/wake`
*   `200` for `/hooks/agent` (async run accepted)
*   `401` on auth failure
*   `429` after repeated auth failures from the same client (check `Retry-After`)
*   `400` on invalid payload
*   `413` on oversized payloads

## [​](https://docs.openclaw.ai/automation/webhook#examples)

Examples

```
curl -X POST http://127.0.0.1:18789/hooks/wake \
  -H 'Authorization: Bearer SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"text":"New email received","mode":"now"}'
```

```
curl -X POST http://127.0.0.1:18789/hooks/agent \
  -H 'x-openclaw-token: SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"message":"Summarize inbox","name":"Email","wakeMode":"next-heartbeat"}'
```

### [​](https://docs.openclaw.ai/automation/webhook#use-a-different-model)

Use a different model

Add `model` to the agent payload (or mapping) to override the model for that run:

```
curl -X POST http://127.0.0.1:18789/hooks/agent \
  -H 'x-openclaw-token: SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"message":"Summarize inbox","name":"Email","model":"openai/gpt-5.2-mini"}'
```

If you enforce `agents.defaults.models`, make sure the override model is included there.

```
curl -X POST http://127.0.0.1:18789/hooks/gmail \
  -H 'Authorization: Bearer SECRET' \
  -H 'Content-Type: application/json' \
  -d '{"source":"gmail","messages":[{"from":"Ada","subject":"Hello","snippet":"Hi"}]}'
```

## [​](https://docs.openclaw.ai/automation/webhook#security)

Security

*   Keep hook endpoints behind loopback, tailnet, or trusted reverse proxy.
*   Use a dedicated hook token; do not reuse gateway auth tokens.
*   Prefer a dedicated hook agent with strict `tools.profile` and sandboxing so hook ingress has a narrower blast radius.
*   Repeated auth failures are rate-limited per client address to slow brute-force attempts.
*   If you use multi-agent routing, set `hooks.allowedAgentIds` to limit explicit `agentId` selection.
*   Keep `hooks.allowRequestSessionKey=false` unless you require caller-selected sessions.
*   If you enable request `sessionKey`, restrict `hooks.allowedSessionKeyPrefixes` (for example, `["hook:"]`).
*   Avoid including sensitive raw payloads in webhook logs.
*   Hook payloads are treated as untrusted and wrapped with safety boundaries by default. If you must disable this for a specific hook, set `allowUnsafeExternalContent: true` in that hook’s mapping (dangerous).

[Automation Troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)[Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
