# cli/approvals

Source: https://docs.openclaw.ai/cli/approvals

Title: approvals - OpenClaw

URL Source: https://docs.openclaw.ai/cli/approvals

Markdown Content:
# approvals - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/approvals#content-area)

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

approvals

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

*   [openclaw approvals](https://docs.openclaw.ai/cli/approvals#openclaw-approvals)
*   [Common commands](https://docs.openclaw.ai/cli/approvals#common-commands)
*   [Replace approvals from a file](https://docs.openclaw.ai/cli/approvals#replace-approvals-from-a-file)
*   [Allowlist helpers](https://docs.openclaw.ai/cli/approvals#allowlist-helpers)
*   [Notes](https://docs.openclaw.ai/cli/approvals#notes)

Tools and execution

# approvals

# [​](https://docs.openclaw.ai/cli/approvals#openclaw-approvals)

`openclaw approvals`

Manage exec approvals for the **local host**, **gateway host**, or a **node host**. By default, commands target the local approvals file on disk. Use `--gateway` to target the gateway, or `--node` to target a specific node.Related:
*   Exec approvals: [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals)
*   Nodes: [Nodes](https://docs.openclaw.ai/nodes)

## [​](https://docs.openclaw.ai/cli/approvals#common-commands)

Common commands

```
openclaw approvals get
openclaw approvals get --node <id|name|ip>
openclaw approvals get --gateway
```

## [​](https://docs.openclaw.ai/cli/approvals#replace-approvals-from-a-file)

Replace approvals from a file

```
openclaw approvals set --file ./exec-approvals.json
openclaw approvals set --node <id|name|ip> --file ./exec-approvals.json
openclaw approvals set --gateway --file ./exec-approvals.json
```

## [​](https://docs.openclaw.ai/cli/approvals#allowlist-helpers)

Allowlist helpers

```
openclaw approvals allowlist add "~/Projects/**/bin/rg"
openclaw approvals allowlist add --agent main --node <id|name|ip> "/usr/bin/uptime"
openclaw approvals allowlist add --agent "*" "/usr/bin/uname"

openclaw approvals allowlist remove "~/Projects/**/bin/rg"
```

## [​](https://docs.openclaw.ai/cli/approvals#notes)

Notes

*   `--node` uses the same resolver as `openclaw nodes` (id, name, ip, or id prefix).
*   `--agent` defaults to `"*"`, which applies to all agents.
*   The node host must advertise `system.execApprovals.get/set` (macOS app or headless node host).
*   Approvals files are stored per host at `~/.openclaw/exec-approvals.json`.

[voicecall](https://docs.openclaw.ai/cli/voicecall)[browser](https://docs.openclaw.ai/cli/browser)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
