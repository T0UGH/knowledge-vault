# concepts/typing-indicators

Source: https://docs.openclaw.ai/concepts/typing-indicators

Title: Typing Indicators - OpenClaw

URL Source: https://docs.openclaw.ai/concepts/typing-indicators

Markdown Content:
# Typing Indicators - OpenClaw

[Skip to main content](https://docs.openclaw.ai/concepts/typing-indicators#content-area)

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

Concept internals

Typing Indicators

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
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

*   [Typing indicators](https://docs.openclaw.ai/concepts/typing-indicators#typing-indicators)
*   [Defaults](https://docs.openclaw.ai/concepts/typing-indicators#defaults)
*   [Modes](https://docs.openclaw.ai/concepts/typing-indicators#modes)
*   [Configuration](https://docs.openclaw.ai/concepts/typing-indicators#configuration)
*   [Notes](https://docs.openclaw.ai/concepts/typing-indicators#notes)

Concept internals

# Typing Indicators

# [​](https://docs.openclaw.ai/concepts/typing-indicators#typing-indicators)

Typing indicators

Typing indicators are sent to the chat channel while a run is active. Use `agents.defaults.typingMode` to control **when** typing starts and `typingIntervalSeconds` to control **how often** it refreshes.
## [​](https://docs.openclaw.ai/concepts/typing-indicators#defaults)

Defaults

When `agents.defaults.typingMode` is **unset**, OpenClaw keeps the legacy behavior:
*   **Direct chats**: typing starts immediately once the model loop begins.
*   **Group chats with a mention**: typing starts immediately.
*   **Group chats without a mention**: typing starts only when message text begins streaming.
*   **Heartbeat runs**: typing is disabled.

## [​](https://docs.openclaw.ai/concepts/typing-indicators#modes)

Modes

Set `agents.defaults.typingMode` to one of:
*   `never` — no typing indicator, ever.
*   `instant` — start typing **as soon as the model loop begins**, even if the run later returns only the silent reply token.
*   `thinking` — start typing on the **first reasoning delta** (requires `reasoningLevel: "stream"` for the run).
*   `message` — start typing on the **first non-silent text delta** (ignores the `NO_REPLY` silent token).

Order of “how early it fires”: `never` → `message` → `thinking` → `instant`
## [​](https://docs.openclaw.ai/concepts/typing-indicators#configuration)

Configuration

```
{
  agent: {
    typingMode: "thinking",
    typingIntervalSeconds: 6,
  },
}
```

You can override mode or cadence per session:

```
{
  session: {
    typingMode: "message",
    typingIntervalSeconds: 4,
  },
}
```

## [​](https://docs.openclaw.ai/concepts/typing-indicators#notes)

Notes

*   `message` mode won’t show typing for silent-only replies (e.g. the `NO_REPLY` token used to suppress output).
*   `thinking` only fires if the run streams reasoning (`reasoningLevel: "stream"`). If the model doesn’t emit reasoning deltas, typing won’t start.
*   Heartbeats never show typing, regardless of mode.
*   `typingIntervalSeconds` controls the **refresh cadence**, not the start time. The default is 6 seconds.

[Markdown Formatting](https://docs.openclaw.ai/concepts/markdown-formatting)[Usage Tracking](https://docs.openclaw.ai/concepts/usage-tracking)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
