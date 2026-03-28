# cli/status

Source: https://docs.openclaw.ai/cli/status

Title: status - OpenClaw

URL Source: https://docs.openclaw.ai/cli/status

Markdown Content:
# status - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/status#content-area)

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

Gateway and service

status

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
    *   [backup](https://docs.openclaw.ai/cli/backup)
    *   [daemon](https://docs.openclaw.ai/cli/daemon)
    *   [doctor](https://docs.openclaw.ai/cli/doctor)
    *   [gateway](https://docs.openclaw.ai/cli/gateway)
    *   [health](https://docs.openclaw.ai/cli/health)
    *   [logs](https://docs.openclaw.ai/cli/logs)
    *   [onboard](https://docs.openclaw.ai/cli/onboard)
    *   [reset](https://docs.openclaw.ai/cli/reset)
    *   [secrets](https://docs.openclaw.ai/cli/secrets)
    *   [security](https://docs.openclaw.ai/cli/security)
    *   [setup](https://docs.openclaw.ai/cli/setup)
    *   [status](https://docs.openclaw.ai/cli/status)
    *   [uninstall](https://docs.openclaw.ai/cli/uninstall)
    *   [update](https://docs.openclaw.ai/cli/update)

*   Agents and sessions 
*   Channels and messaging 
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

*   [openclaw status](https://docs.openclaw.ai/cli/status#openclaw-status)

Gateway and service

# status

# [​](https://docs.openclaw.ai/cli/status#openclaw-status)

`openclaw status`

Diagnostics for channels + sessions.

```
openclaw status
openclaw status --all
openclaw status --deep
openclaw status --usage
```

Notes:
*   `--deep` runs live probes (WhatsApp Web + Telegram + Discord + Google Chat + Slack + Signal).
*   Output includes per-agent session stores when multiple agents are configured.
*   Overview includes Gateway + node host service install/runtime status when available.
*   Overview includes update channel + git SHA (for source checkouts).
*   Update info surfaces in the Overview; if an update is available, status prints a hint to run `openclaw update` (see [Updating](https://docs.openclaw.ai/install/updating)).
*   Read-only status surfaces (`status`, `status --json`, `status --all`) resolve supported SecretRefs for their targeted config paths when possible.
*   If a supported channel SecretRef is configured but unavailable in the current command path, status stays read-only and reports degraded output instead of crashing. Human output shows warnings such as “configured token unavailable in this command path”, and JSON output includes `secretDiagnostics`.
*   When command-local SecretRef resolution succeeds, status prefers the resolved snapshot and clears transient “secret unavailable” channel markers from the final output.
*   `status --all` includes a Secrets overview row and a diagnosis section that summarizes secret diagnostics (truncated for readability) without stopping report generation.

[setup](https://docs.openclaw.ai/cli/setup)[uninstall](https://docs.openclaw.ai/cli/uninstall)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
