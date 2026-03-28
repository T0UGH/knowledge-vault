# cli/qr

Source: https://docs.openclaw.ai/cli/qr

Title: qr - OpenClaw

URL Source: https://docs.openclaw.ai/cli/qr

Markdown Content:
# qr - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/qr#content-area)

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

Channels and messaging

qr

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
*   Channels and messaging 
    *   [channels](https://docs.openclaw.ai/cli/channels)
    *   [devices](https://docs.openclaw.ai/cli/devices)
    *   [directory](https://docs.openclaw.ai/cli/directory)
    *   [pairing](https://docs.openclaw.ai/cli/pairing)
    *   [qr](https://docs.openclaw.ai/cli/qr)
    *   [voicecall](https://docs.openclaw.ai/cli/voicecall)

*   Tools and execution 
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

*   [openclaw qr](https://docs.openclaw.ai/cli/qr#openclaw-qr)
*   [Usage](https://docs.openclaw.ai/cli/qr#usage)
*   [Options](https://docs.openclaw.ai/cli/qr#options)
*   [Notes](https://docs.openclaw.ai/cli/qr#notes)

Channels and messaging

# qr

# [​](https://docs.openclaw.ai/cli/qr#openclaw-qr)

`openclaw qr`

Generate an iOS pairing QR and setup code from your current Gateway configuration.
## [​](https://docs.openclaw.ai/cli/qr#usage)

Usage

```
openclaw qr
openclaw qr --setup-code-only
openclaw qr --json
openclaw qr --remote
openclaw qr --url wss://gateway.example/ws
```

## [​](https://docs.openclaw.ai/cli/qr#options)

Options

*   `--remote`: use `gateway.remote.url` plus remote token/password from config
*   `--url <url>`: override gateway URL used in payload
*   `--public-url <url>`: override public URL used in payload
*   `--token <token>`: override which gateway token the bootstrap flow authenticates against
*   `--password <password>`: override which gateway password the bootstrap flow authenticates against
*   `--setup-code-only`: print only setup code
*   `--no-ascii`: skip ASCII QR rendering
*   `--json`: emit JSON (`setupCode`, `gatewayUrl`, `auth`, `urlSource`)

## [​](https://docs.openclaw.ai/cli/qr#notes)

Notes

*   `--token` and `--password` are mutually exclusive.
*   The setup code itself now carries an opaque short-lived `bootstrapToken`, not the shared gateway token/password.
*   With `--remote`, if effectively active remote credentials are configured as SecretRefs and you do not pass `--token` or `--password`, the command resolves them from the active gateway snapshot. If gateway is unavailable, the command fails fast.
*   Without `--remote`, local gateway auth SecretRefs are resolved when no CLI auth override is passed:
    *   `gateway.auth.token` resolves when token auth can win (explicit `gateway.auth.mode="token"` or inferred mode where no password source wins).
    *   `gateway.auth.password` resolves when password auth can win (explicit `gateway.auth.mode="password"` or inferred mode with no winning token from auth/env).

*   If both `gateway.auth.token` and `gateway.auth.password` are configured (including SecretRefs) and `gateway.auth.mode` is unset, setup-code resolution fails until mode is set explicitly.
*   Gateway version skew note: this command path requires a gateway that supports `secrets.resolve`; older gateways return an unknown-method error.
*   After scanning, approve device pairing with:
    *   `openclaw devices list`
    *   `openclaw devices approve <requestId>`

[pairing](https://docs.openclaw.ai/cli/pairing)[voicecall](https://docs.openclaw.ai/cli/voicecall)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
