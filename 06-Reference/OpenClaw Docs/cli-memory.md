# cli/memory

Source: https://docs.openclaw.ai/cli/memory

Title: memory - OpenClaw

URL Source: https://docs.openclaw.ai/cli/memory

Markdown Content:
# memory - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/memory#content-area)

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

Agents and sessions

memory

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
    *   [agent](https://docs.openclaw.ai/cli/agent)
    *   [agents](https://docs.openclaw.ai/cli/agents)
    *   [hooks](https://docs.openclaw.ai/cli/hooks)
    *   [memory](https://docs.openclaw.ai/cli/memory)
    *   [message](https://docs.openclaw.ai/cli/message)
    *   [models](https://docs.openclaw.ai/cli/models)
    *   [sessions](https://docs.openclaw.ai/cli/sessions)
    *   [system](https://docs.openclaw.ai/cli/system)

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

*   [openclaw memory](https://docs.openclaw.ai/cli/memory#openclaw-memory)
*   [Examples](https://docs.openclaw.ai/cli/memory#examples)
*   [Options](https://docs.openclaw.ai/cli/memory#options)

Agents and sessions

# memory

# [​](https://docs.openclaw.ai/cli/memory#openclaw-memory)

`openclaw memory`

Manage semantic memory indexing and search. Provided by the active memory plugin (default: `memory-core`; set `plugins.slots.memory = "none"` to disable).Related:
*   Memory concept: [Memory](https://docs.openclaw.ai/concepts/memory)
*   Plugins: [Plugins](https://docs.openclaw.ai/tools/plugin)

## [​](https://docs.openclaw.ai/cli/memory#examples)

Examples

```
openclaw memory status
openclaw memory status --deep
openclaw memory index --force
openclaw memory search "meeting notes"
openclaw memory search --query "deployment" --max-results 20
openclaw memory status --json
openclaw memory status --deep --index
openclaw memory status --deep --index --verbose
openclaw memory status --agent main
openclaw memory index --agent main --verbose
```

## [​](https://docs.openclaw.ai/cli/memory#options)

Options

`memory status` and `memory index`:
*   `--agent <id>`: scope to a single agent. Without it, these commands run for each configured agent; if no agent list is configured, they fall back to the default agent.
*   `--verbose`: emit detailed logs during probes and indexing.

`memory status`:
*   `--deep`: probe vector + embedding availability.
*   `--index`: run a reindex if the store is dirty (implies `--deep`).
*   `--json`: print JSON output.

`memory index`:
*   `--force`: force a full reindex.

`memory search`:
*   Query input: pass either positional `[query]` or `--query <text>`.
*   If both are provided, `--query` wins.
*   If neither is provided, the command exits with an error.
*   `--agent <id>`: scope to a single agent (default: the default agent).
*   `--max-results <n>`: limit the number of results returned.
*   `--min-score <n>`: filter out low-score matches.
*   `--json`: print JSON results.

Notes:
*   `memory index --verbose` prints per-phase details (provider, model, sources, batch activity).
*   `memory status` includes any extra paths configured via `memorySearch.extraPaths`.
*   If effectively active memory remote API key fields are configured as SecretRefs, the command resolves those values from the active gateway snapshot. If gateway is unavailable, the command fails fast.
*   Gateway version skew note: this command path requires a gateway that supports `secrets.resolve`; older gateways return an unknown-method error.

[hooks](https://docs.openclaw.ai/cli/hooks)[message](https://docs.openclaw.ai/cli/message)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
