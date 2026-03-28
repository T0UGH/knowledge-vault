# cli/voicecall

Source: https://docs.openclaw.ai/cli/voicecall

Title: voicecall - OpenClaw

URL Source: https://docs.openclaw.ai/cli/voicecall

Markdown Content:
# voicecall - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/voicecall#content-area)

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

voicecall

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

*   [openclaw voicecall](https://docs.openclaw.ai/cli/voicecall#openclaw-voicecall)
*   [Common commands](https://docs.openclaw.ai/cli/voicecall#common-commands)
*   [Exposing webhooks (Tailscale)](https://docs.openclaw.ai/cli/voicecall#exposing-webhooks-tailscale)

Channels and messaging

# voicecall

# [​](https://docs.openclaw.ai/cli/voicecall#openclaw-voicecall)

`openclaw voicecall`

`voicecall` is a plugin-provided command. It only appears if the voice-call plugin is installed and enabled.Primary doc:
*   Voice-call plugin: [Voice Call](https://docs.openclaw.ai/plugins/voice-call)

## [​](https://docs.openclaw.ai/cli/voicecall#common-commands)

Common commands

```
openclaw voicecall status --call-id <id>
openclaw voicecall call --to "+15555550123" --message "Hello" --mode notify
openclaw voicecall continue --call-id <id> --message "Any questions?"
openclaw voicecall end --call-id <id>
```

## [​](https://docs.openclaw.ai/cli/voicecall#exposing-webhooks-tailscale)

Exposing webhooks (Tailscale)

```
openclaw voicecall expose --mode serve
openclaw voicecall expose --mode funnel
openclaw voicecall expose --mode off
```

Security note: only expose the webhook endpoint to networks you trust. Prefer Tailscale Serve over Funnel when possible.

[qr](https://docs.openclaw.ai/cli/qr)[approvals](https://docs.openclaw.ai/cli/approvals)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
