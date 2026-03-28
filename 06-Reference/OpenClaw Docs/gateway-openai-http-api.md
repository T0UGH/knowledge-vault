# gateway/openai-http-api

Source: https://docs.openclaw.ai/gateway/openai-http-api

Title: OpenAI Chat Completions - OpenClaw

URL Source: https://docs.openclaw.ai/gateway/openai-http-api

Markdown Content:
# OpenAI Chat Completions - OpenClaw

[Skip to main content](https://docs.openclaw.ai/gateway/openai-http-api#content-area)

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

OpenAI Chat Completions

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

*   [OpenAI Chat Completions (HTTP)](https://docs.openclaw.ai/gateway/openai-http-api#openai-chat-completions-http)
*   [Authentication](https://docs.openclaw.ai/gateway/openai-http-api#authentication)
*   [Security boundary (important)](https://docs.openclaw.ai/gateway/openai-http-api#security-boundary-important)
*   [Agent-first model contract](https://docs.openclaw.ai/gateway/openai-http-api#agent-first-model-contract)
*   [Enabling the endpoint](https://docs.openclaw.ai/gateway/openai-http-api#enabling-the-endpoint)
*   [Disabling the endpoint](https://docs.openclaw.ai/gateway/openai-http-api#disabling-the-endpoint)
*   [Session behavior](https://docs.openclaw.ai/gateway/openai-http-api#session-behavior)
*   [Why this surface matters](https://docs.openclaw.ai/gateway/openai-http-api#why-this-surface-matters)
*   [Model list and agent routing](https://docs.openclaw.ai/gateway/openai-http-api#model-list-and-agent-routing)
*   [Streaming (SSE)](https://docs.openclaw.ai/gateway/openai-http-api#streaming-sse)
*   [Open WebUI quick setup](https://docs.openclaw.ai/gateway/openai-http-api#open-webui-quick-setup)
*   [Examples](https://docs.openclaw.ai/gateway/openai-http-api#examples)

Protocols and APIs

# OpenAI Chat Completions

# [​](https://docs.openclaw.ai/gateway/openai-http-api#openai-chat-completions-http)

OpenAI Chat Completions (HTTP)

OpenClaw’s Gateway can serve a small OpenAI-compatible Chat Completions endpoint.This endpoint is **disabled by default**. Enable it in config first.
*   `POST /v1/chat/completions`
*   Same port as the Gateway (WS + HTTP multiplex): `http://<gateway-host>:<port>/v1/chat/completions`

When the Gateway’s OpenAI-compatible HTTP surface is enabled, it also serves:
*   `GET /v1/models`
*   `GET /v1/models/{id}`
*   `POST /v1/embeddings`
*   `POST /v1/responses`

Under the hood, requests are executed as a normal Gateway agent run (same codepath as `openclaw agent`), so routing/permissions/config match your Gateway.
## [​](https://docs.openclaw.ai/gateway/openai-http-api#authentication)

Authentication

Uses the Gateway auth configuration. Send a bearer token:
*   `Authorization: Bearer <token>`

Notes:
*   When `gateway.auth.mode="token"`, use `gateway.auth.token` (or `OPENCLAW_GATEWAY_TOKEN`).
*   When `gateway.auth.mode="password"`, use `gateway.auth.password` (or `OPENCLAW_GATEWAY_PASSWORD`).
*   If `gateway.auth.rateLimit` is configured and too many auth failures occur, the endpoint returns `429` with `Retry-After`.

## [​](https://docs.openclaw.ai/gateway/openai-http-api#security-boundary-important)

Security boundary (important)

Treat this endpoint as a **full operator-access** surface for the gateway instance.
*   HTTP bearer auth here is not a narrow per-user scope model.
*   A valid Gateway token/password for this endpoint should be treated like an owner/operator credential.
*   Requests run through the same control-plane agent path as trusted operator actions.
*   There is no separate non-owner/per-user tool boundary on this endpoint; once a caller passes Gateway auth here, OpenClaw treats that caller as a trusted operator for this gateway.
*   If the target agent policy allows sensitive tools, this endpoint can use them.
*   Keep this endpoint on loopback/tailnet/private ingress only; do not expose it directly to the public internet.

See [Security](https://docs.openclaw.ai/gateway/security) and [Remote access](https://docs.openclaw.ai/gateway/remote).
## [​](https://docs.openclaw.ai/gateway/openai-http-api#agent-first-model-contract)

Agent-first model contract

OpenClaw treats the OpenAI `model` field as an **agent target**, not a raw provider model id.
*   `model: "openclaw"` routes to the configured default agent.
*   `model: "openclaw/default"` also routes to the configured default agent.
*   `model: "openclaw/<agentId>"` routes to a specific agent.

Optional request headers:
*   `x-openclaw-model: <provider/model-or-bare-id>` overrides the backend model for the selected agent.
*   `x-openclaw-agent-id: <agentId>` remains supported as a compatibility override.
*   `x-openclaw-session-key: <sessionKey>` fully controls session routing.
*   `x-openclaw-message-channel: <channel>` sets the synthetic ingress channel context for channel-aware prompts and policies.

Compatibility aliases still accepted:
*   `model: "openclaw:<agentId>"`
*   `model: "agent:<agentId>"`

## [​](https://docs.openclaw.ai/gateway/openai-http-api#enabling-the-endpoint)

Enabling the endpoint

Set `gateway.http.endpoints.chatCompletions.enabled` to `true`:

```
{
  gateway: {
    http: {
      endpoints: {
        chatCompletions: { enabled: true },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/gateway/openai-http-api#disabling-the-endpoint)

Disabling the endpoint

Set `gateway.http.endpoints.chatCompletions.enabled` to `false`:

```
{
  gateway: {
    http: {
      endpoints: {
        chatCompletions: { enabled: false },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/gateway/openai-http-api#session-behavior)

Session behavior

By default the endpoint is **stateless per request** (a new session key is generated each call).If the request includes an OpenAI `user` string, the Gateway derives a stable session key from it, so repeated calls can share an agent session.
## [​](https://docs.openclaw.ai/gateway/openai-http-api#why-this-surface-matters)

Why this surface matters

This is the highest-leverage compatibility set for self-hosted frontends and tooling:
*   Most Open WebUI, LobeChat, and LibreChat setups expect `/v1/models`.
*   Many RAG systems expect `/v1/embeddings`.
*   Existing OpenAI chat clients can usually start with `/v1/chat/completions`.
*   More agent-native clients increasingly prefer `/v1/responses`.

## [​](https://docs.openclaw.ai/gateway/openai-http-api#model-list-and-agent-routing)

Model list and agent routing

What does `/v1/models` return?

An OpenClaw agent-target list.The returned ids are `openclaw`, `openclaw/default`, and `openclaw/<agentId>` entries. Use them directly as OpenAI `model` values.

Does `/v1/models` list agents or sub-agents?

It lists top-level agent targets, not backend provider models and not sub-agents.Sub-agents remain internal execution topology. They do not appear as pseudo-models.

Why is `openclaw/default` included?

`openclaw/default` is the stable alias for the configured default agent.That means clients can keep using one predictable id even if the real default agent id changes between environments.

How do I override the backend model?

Use `x-openclaw-model`.Examples: `x-openclaw-model: openai/gpt-5.4``x-openclaw-model: gpt-5.4`If you omit it, the selected agent runs with its normal configured model choice.

How do embeddings fit this contract?

`/v1/embeddings` uses the same agent-target `model` ids.Use `model: "openclaw/default"` or `model: "openclaw/<agentId>"`. When you need a specific embedding model, send it in `x-openclaw-model`. Without that header, the request passes through to the selected agent’s normal embedding setup.

## [​](https://docs.openclaw.ai/gateway/openai-http-api#streaming-sse)

Streaming (SSE)

Set `stream: true` to receive Server-Sent Events (SSE):
*   `Content-Type: text/event-stream`
*   Each event line is `data: <json>`
*   Stream ends with `data: [DONE]`

## [​](https://docs.openclaw.ai/gateway/openai-http-api#open-webui-quick-setup)

Open WebUI quick setup

For a basic Open WebUI connection:
*   Base URL: `http://127.0.0.1:18789/v1`
*   Docker on macOS base URL: `http://host.docker.internal:18789/v1`
*   API key: your Gateway bearer token
*   Model: `openclaw/default`

Expected behavior:
*   `GET /v1/models` should list `openclaw/default`
*   Open WebUI should use `openclaw/default` as the chat model id
*   If you want a specific backend provider/model for that agent, set the agent’s normal default model or send `x-openclaw-model`

Quick smoke:

```
curl -sS http://127.0.0.1:18789/v1/models \
  -H 'Authorization: Bearer YOUR_TOKEN'
```

If that returns `openclaw/default`, most Open WebUI setups can connect with the same base URL and token.
## [​](https://docs.openclaw.ai/gateway/openai-http-api#examples)

Examples

Non-streaming:

```
curl -sS http://127.0.0.1:18789/v1/chat/completions \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "model": "openclaw/default",
    "messages": [{"role":"user","content":"hi"}]
  }'
```

Streaming:

```
curl -N http://127.0.0.1:18789/v1/chat/completions \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Content-Type: application/json' \
  -H 'x-openclaw-model: openai/gpt-5.4' \
  -d '{
    "model": "openclaw/research",
    "stream": true,
    "messages": [{"role":"user","content":"hi"}]
  }'
```

List models:

```
curl -sS http://127.0.0.1:18789/v1/models \
  -H 'Authorization: Bearer YOUR_TOKEN'
```

Fetch one model:

```
curl -sS http://127.0.0.1:18789/v1/models/openclaw%2Fdefault \
  -H 'Authorization: Bearer YOUR_TOKEN'
```

Create embeddings:

```
curl -sS http://127.0.0.1:18789/v1/embeddings \
  -H 'Authorization: Bearer YOUR_TOKEN' \
  -H 'Content-Type: application/json' \
  -H 'x-openclaw-model: openai/text-embedding-3-small' \
  -d '{
    "model": "openclaw/default",
    "input": ["alpha", "beta"]
  }'
```

Notes:
*   `/v1/models` returns OpenClaw agent targets, not raw provider catalogs.
*   `openclaw/default` is always present so one stable id works across environments.
*   Backend provider/model overrides belong in `x-openclaw-model`, not the OpenAI `model` field.
*   `/v1/embeddings` supports `input` as a string or array of strings.

[Bridge Protocol](https://docs.openclaw.ai/gateway/bridge-protocol)[OpenResponses API](https://docs.openclaw.ai/gateway/openresponses-http-api)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
