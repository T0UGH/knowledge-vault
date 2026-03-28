# gateway/tools-invoke-http-api

Source: https://docs.openclaw.ai/gateway/tools-invoke-http-api

Title: Tools Invoke API - OpenClaw

URL Source: https://docs.openclaw.ai/gateway/tools-invoke-http-api

Markdown Content:
# Tools Invoke API - OpenClaw

[Skip to main content](https://docs.openclaw.ai/gateway/tools-invoke-http-api#content-area)

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

Protocols and APIs

Tools Invoke API

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Gateway

*   [Gateway Runbook](https://docs.openclaw.ai/gateway)
*   Configuration and operations 
*   Security and sandboxing 
*   Protocols and APIs 
    *   [Gateway Protocol](https://docs.openclaw.ai/gateway/protocol)
    *   [Bridge Protocol](https://docs.openclaw.ai/gateway/bridge-protocol)
    *   [OpenAI Chat Completions](https://docs.openclaw.ai/gateway/openai-http-api)
    *   [OpenResponses API](https://docs.openclaw.ai/gateway/openresponses-http-api)
    *   [Tools Invoke API](https://docs.openclaw.ai/gateway/tools-invoke-http-api)
    *   [CLI Backends](https://docs.openclaw.ai/gateway/cli-backends)
    *   [Local Models](https://docs.openclaw.ai/gateway/local-models)

*   Networking and discovery 

##### Remote access

*   [Remote Access](https://docs.openclaw.ai/gateway/remote)
*   [Remote Gateway Setup](https://docs.openclaw.ai/gateway/remote-gateway-readme)
*   [Tailscale](https://docs.openclaw.ai/gateway/tailscale)

##### Security

*   [Formal Verification (Security Models)](https://docs.openclaw.ai/security/formal-verification)
*   [Threat Model (MITRE ATLAS)](https://docs.openclaw.ai/security/THREAT-MODEL-ATLAS)
*   [Contributing to the Threat Model](https://docs.openclaw.ai/security/CONTRIBUTING-THREAT-MODEL)

##### Nodes and devices

*   [Nodes](https://docs.openclaw.ai/nodes)
*   [Node Troubleshooting](https://docs.openclaw.ai/nodes/troubleshooting)
*   [Media Understanding](https://docs.openclaw.ai/nodes/media-understanding)
*   [Image and Media Support](https://docs.openclaw.ai/nodes/images)
*   [Audio and Voice Notes](https://docs.openclaw.ai/nodes/audio)
*   [Camera Capture](https://docs.openclaw.ai/nodes/camera)
*   [Talk Mode](https://docs.openclaw.ai/nodes/talk)
*   [Voice Wake](https://docs.openclaw.ai/nodes/voicewake)
*   [Location Command](https://docs.openclaw.ai/nodes/location-command)
*   [Text-to-Speech](https://docs.openclaw.ai/tools/tts)

##### Web interfaces

*   [Web](https://docs.openclaw.ai/web)
*   [Control UI](https://docs.openclaw.ai/web/control-ui)
*   [Dashboard](https://docs.openclaw.ai/web/dashboard)
*   [WebChat](https://docs.openclaw.ai/web/webchat)
*   [TUI](https://docs.openclaw.ai/web/tui)

On this page

*   [Tools Invoke (HTTP)](https://docs.openclaw.ai/gateway/tools-invoke-http-api#tools-invoke-http)
*   [Authentication](https://docs.openclaw.ai/gateway/tools-invoke-http-api#authentication)
*   [Request body](https://docs.openclaw.ai/gateway/tools-invoke-http-api#request-body)
*   [Policy + routing behavior](https://docs.openclaw.ai/gateway/tools-invoke-http-api#policy-%2B-routing-behavior)
*   [Responses](https://docs.openclaw.ai/gateway/tools-invoke-http-api#responses)
*   [Example](https://docs.openclaw.ai/gateway/tools-invoke-http-api#example)

Protocols and APIs

# Tools Invoke API

# [​](https://docs.openclaw.ai/gateway/tools-invoke-http-api#tools-invoke-http)

Tools Invoke (HTTP)

OpenClaw’s Gateway exposes a simple HTTP endpoint for invoking a single tool directly. It is always enabled and uses Gateway auth plus tool policy, but callers that pass Gateway bearer auth are treated as trusted operators for that gateway.
*   `POST /tools/invoke`
*   Same port as the Gateway (WS + HTTP multiplex): `http://<gateway-host>:<port>/tools/invoke`

Default max payload size is 2 MB.
## [​](https://docs.openclaw.ai/gateway/tools-invoke-http-api#authentication)

Authentication

Uses the Gateway auth configuration. Send a bearer token:
*   `Authorization: Bearer <token>`

Notes:
*   When `gateway.auth.mode="token"`, use `gateway.auth.token` (or `OPENCLAW_GATEWAY_TOKEN`).
*   When `gateway.auth.mode="password"`, use `gateway.auth.password` (or `OPENCLAW_GATEWAY_PASSWORD`).
*   If `gateway.auth.rateLimit` is configured and too many auth failures occur, the endpoint returns `429` with `Retry-After`.
*   Treat this credential as a full-access operator secret for that gateway. It is not a scoped API token for a narrower `/tools/invoke` role.

## [​](https://docs.openclaw.ai/gateway/tools-invoke-http-api#request-body)

Request body

```
{
  "tool": "sessions_list",
  "action": "json",
  "args": {},
  "sessionKey": "main",
  "dryRun": false
}
```

Fields:
*   `tool` (string, required): tool name to invoke.
*   `action` (string, optional): mapped into args if the tool schema supports `action` and the args payload omitted it.
*   `args` (object, optional): tool-specific arguments.
*   `sessionKey` (string, optional): target session key. If omitted or `"main"`, the Gateway uses the configured main session key (honors `session.mainKey` and default agent, or `global` in global scope).
*   `dryRun` (boolean, optional): reserved for future use; currently ignored.

## [​](https://docs.openclaw.ai/gateway/tools-invoke-http-api#policy-+-routing-behavior)

Policy + routing behavior

Tool availability is filtered through the same policy chain used by Gateway agents:
*   `tools.profile` / `tools.byProvider.profile`
*   `tools.allow` / `tools.byProvider.allow`
*   `agents.<id>.tools.allow` / `agents.<id>.tools.byProvider.allow`
*   group policies (if the session key maps to a group or channel)
*   subagent policy (when invoking with a subagent session key)

If a tool is not allowed by policy, the endpoint returns **404**.Important boundary notes:
*   `POST /tools/invoke` is in the same trusted-operator bucket as other Gateway HTTP APIs such as `/v1/chat/completions`, `/v1/responses`, and `/api/channels/*`.
*   Exec approvals are operator guardrails, not a separate authorization boundary for this HTTP endpoint. If a tool is reachable here via Gateway auth + tool policy, `/tools/invoke` does not add an extra per-call approval prompt.
*   Do not share Gateway bearer credentials with untrusted callers. If you need separation across trust boundaries, run separate gateways (and ideally separate OS users/hosts).

Gateway HTTP also applies a hard deny list by default (even if session policy allows the tool):
*   `cron`
*   `sessions_spawn`
*   `sessions_send`
*   `gateway`
*   `whatsapp_login`

You can customize this deny list via `gateway.tools`:

```
{
  gateway: {
    tools: {
      // Additional tools to block over HTTP /tools/invoke
      deny: ["browser"],
      // Remove tools from the default deny list
      allow: ["gateway"],
    },
  },
}
```

To help group policies resolve context, you can optionally set:
*   `x-openclaw-message-channel: <channel>` (example: `slack`, `telegram`)
*   `x-openclaw-account-id: <accountId>` (when multiple accounts exist)

## [​](https://docs.openclaw.ai/gateway/tools-invoke-http-api#responses)

Responses

*   `200` → `{ ok: true, result }`
*   `400` → `{ ok: false, error: { type, message } }` (invalid request or tool input error)
*   `401` → unauthorized
*   `429` → auth rate-limited (`Retry-After` set)
*   `404` → tool not available (not found or not allowlisted)
*   `405` → method not allowed
*   `500` → `{ ok: false, error: { type, message } }` (unexpected tool execution error; sanitized message)

## [​](https://docs.openclaw.ai/gateway/tools-invoke-http-api#example)

Example

```
curl -sS http://127.0.0.1:18789/tools/invoke \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "tool": "sessions_list",
    "action": "json",
    "args": {}
  }'
```

[OpenResponses API](https://docs.openclaw.ai/gateway/openresponses-http-api)[CLI Backends](https://docs.openclaw.ai/gateway/cli-backends)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
