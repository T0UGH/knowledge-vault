# cli/node

Source: https://docs.openclaw.ai/cli/node

Title: node - OpenClaw

URL Source: https://docs.openclaw.ai/cli/node

Markdown Content:
# node - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/node#content-area)

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

Tools and execution

node

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
*   Channels and messaging 
*   Tools and execution 
    *   [approvals](https://docs.openclaw.ai/cli/approvals)
    *   [browser](https://docs.openclaw.ai/cli/browser)
    *   [cron](https://docs.openclaw.ai/cli/cron)
    *   [node](https://docs.openclaw.ai/cli/node)
    *   [nodes](https://docs.openclaw.ai/cli/nodes)
    *   [Sandbox CLI](https://docs.openclaw.ai/cli/sandbox)

*   Configuration 
*   Plugins and skills 
*   Interfaces 
*   Utility 

##### RPC and API

*   [RPC Adapters](https://docs.openclaw.ai/reference/rpc)
*   [Device Model Database](https://docs.openclaw.ai/reference/device-models)

##### Templates

*   [Default AGENTS.md](https://docs.openclaw.ai/reference/AGENTS.default)
*   [AGENTS.md Template](https://docs.openclaw.ai/reference/templates/AGENTS)
*   [BOOT.md Template](https://docs.openclaw.ai/reference/templates/BOOT)
*   [BOOTSTRAP.md Template](https://docs.openclaw.ai/reference/templates/BOOTSTRAP)
*   [HEARTBEAT.md Template](https://docs.openclaw.ai/reference/templates/HEARTBEAT)
*   [IDENTITY Template](https://docs.openclaw.ai/reference/templates/IDENTITY)
*   [SOUL.md Template](https://docs.openclaw.ai/reference/templates/SOUL)
*   [TOOLS.md Template](https://docs.openclaw.ai/reference/templates/TOOLS)
*   [USER Template](https://docs.openclaw.ai/reference/templates/USER)

##### Technical reference

*   [Pi Integration Architecture](https://docs.openclaw.ai/pi)
*   [Onboarding Reference](https://docs.openclaw.ai/reference/wizard)
*   [Token Use and Costs](https://docs.openclaw.ai/reference/token-use)
*   [SecretRef Credential Surface](https://docs.openclaw.ai/reference/secretref-credential-surface)
*   [Prompt Caching](https://docs.openclaw.ai/reference/prompt-caching)
*   [API Usage and Costs](https://docs.openclaw.ai/reference/api-usage-costs)
*   [Transcript Hygiene](https://docs.openclaw.ai/reference/transcript-hygiene)
*   [Memory configuration reference](https://docs.openclaw.ai/reference/memory-config)
*   [Date and Time](https://docs.openclaw.ai/date-time)

##### Concept internals

*   [TypeBox](https://docs.openclaw.ai/concepts/typebox)
*   [Markdown Formatting](https://docs.openclaw.ai/concepts/markdown-formatting)
*   [Typing Indicators](https://docs.openclaw.ai/concepts/typing-indicators)
*   [Usage Tracking](https://docs.openclaw.ai/concepts/usage-tracking)
*   [Timezones](https://docs.openclaw.ai/concepts/timezone)

##### Project

*   [Credits](https://docs.openclaw.ai/reference/credits)

##### Release policy

*   [Release Policy](https://docs.openclaw.ai/reference/RELEASING)
*   [Tests](https://docs.openclaw.ai/reference/test)

On this page

*   [openclaw node](https://docs.openclaw.ai/cli/node#openclaw-node)
*   [Why use a node host?](https://docs.openclaw.ai/cli/node#why-use-a-node-host)
*   [Browser proxy (zero-config)](https://docs.openclaw.ai/cli/node#browser-proxy-zero-config)
*   [Run (foreground)](https://docs.openclaw.ai/cli/node#run-foreground)
*   [Gateway auth for node host](https://docs.openclaw.ai/cli/node#gateway-auth-for-node-host)
*   [Service (background)](https://docs.openclaw.ai/cli/node#service-background)
*   [Pairing](https://docs.openclaw.ai/cli/node#pairing)
*   [Exec approvals](https://docs.openclaw.ai/cli/node#exec-approvals)

Tools and execution

# node

# [​](https://docs.openclaw.ai/cli/node#openclaw-node)

`openclaw node`

Run a **headless node host** that connects to the Gateway WebSocket and exposes `system.run` / `system.which` on this machine.
## [​](https://docs.openclaw.ai/cli/node#why-use-a-node-host)

Why use a node host?

Use a node host when you want agents to **run commands on other machines** in your network without installing a full macOS companion app there.Common use cases:
*   Run commands on remote Linux/Windows boxes (build servers, lab machines, NAS).
*   Keep exec **sandboxed** on the gateway, but delegate approved runs to other hosts.
*   Provide a lightweight, headless execution target for automation or CI nodes.

Execution is still guarded by **exec approvals** and per‑agent allowlists on the node host, so you can keep command access scoped and explicit.
## [​](https://docs.openclaw.ai/cli/node#browser-proxy-zero-config)

Browser proxy (zero-config)

Node hosts automatically advertise a browser proxy if `browser.enabled` is not disabled on the node. This lets the agent use browser automation on that node without extra configuration.By default, the proxy exposes the node’s normal browser profile surface. If you set `nodeHost.browserProxy.allowProfiles`, the proxy becomes restrictive: non-allowlisted profile targeting is rejected, and persistent profile create/delete routes are blocked through the proxy.Disable it on the node if needed:

```
{
  nodeHost: {
    browserProxy: {
      enabled: false,
    },
  },
}
```

## [​](https://docs.openclaw.ai/cli/node#run-foreground)

Run (foreground)

```
openclaw node run --host <gateway-host> --port 18789
```

Options:
*   `--host <host>`: Gateway WebSocket host (default: `127.0.0.1`)
*   `--port <port>`: Gateway WebSocket port (default: `18789`)
*   `--tls`: Use TLS for the gateway connection
*   `--tls-fingerprint <sha256>`: Expected TLS certificate fingerprint (sha256)
*   `--node-id <id>`: Override node id (clears pairing token)
*   `--display-name <name>`: Override the node display name

## [​](https://docs.openclaw.ai/cli/node#gateway-auth-for-node-host)

Gateway auth for node host

`openclaw node run` and `openclaw node install` resolve gateway auth from config/env (no `--token`/`--password` flags on node commands):
*   `OPENCLAW_GATEWAY_TOKEN` / `OPENCLAW_GATEWAY_PASSWORD` are checked first.
*   Then local config fallback: `gateway.auth.token` / `gateway.auth.password`.
*   In local mode, node host intentionally does not inherit `gateway.remote.token` / `gateway.remote.password`.
*   If `gateway.auth.token` / `gateway.auth.password` is explicitly configured via SecretRef and unresolved, node auth resolution fails closed (no remote fallback masking).
*   In `gateway.mode=remote`, remote client fields (`gateway.remote.token` / `gateway.remote.password`) are also eligible per remote precedence rules.
*   Node host auth resolution only honors `OPENCLAW_GATEWAY_*` env vars.

## [​](https://docs.openclaw.ai/cli/node#service-background)

Service (background)

Install a headless node host as a user service.

```
openclaw node install --host <gateway-host> --port 18789
```

Options:
*   `--host <host>`: Gateway WebSocket host (default: `127.0.0.1`)
*   `--port <port>`: Gateway WebSocket port (default: `18789`)
*   `--tls`: Use TLS for the gateway connection
*   `--tls-fingerprint <sha256>`: Expected TLS certificate fingerprint (sha256)
*   `--node-id <id>`: Override node id (clears pairing token)
*   `--display-name <name>`: Override the node display name
*   `--runtime <runtime>`: Service runtime (`node` or `bun`)
*   `--force`: Reinstall/overwrite if already installed

Manage the service:

```
openclaw node status
openclaw node stop
openclaw node restart
openclaw node uninstall
```

Use `openclaw node run` for a foreground node host (no service).Service commands accept `--json` for machine-readable output.
## [​](https://docs.openclaw.ai/cli/node#pairing)

Pairing

The first connection creates a pending device pairing request (`role: node`) on the Gateway. Approve it via:

```
openclaw devices list
openclaw devices approve <requestId>
```

If the node retries pairing with changed auth details (role/scopes/public key), the previous pending request is superseded and a new `requestId` is created. Run `openclaw devices list` again before approval.The node host stores its node id, token, display name, and gateway connection info in `~/.openclaw/node.json`.
## [​](https://docs.openclaw.ai/cli/node#exec-approvals)

Exec approvals

`system.run` is gated by local exec approvals:
*   `~/.openclaw/exec-approvals.json`
*   [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals)
*   `openclaw approvals --node <id|name|ip>` (edit from the Gateway)

[cron](https://docs.openclaw.ai/cli/cron)[nodes](https://docs.openclaw.ai/cli/nodes)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
