# cli/models

Source: https://docs.openclaw.ai/cli/models

Title: models - OpenClaw

URL Source: https://docs.openclaw.ai/cli/models

Markdown Content:
# models - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/models#content-area)

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

models

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

*   [openclaw models](https://docs.openclaw.ai/cli/models#openclaw-models)
*   [Common commands](https://docs.openclaw.ai/cli/models#common-commands)
*   [models status](https://docs.openclaw.ai/cli/models#models-status)
*   [Aliases + fallbacks](https://docs.openclaw.ai/cli/models#aliases-%2B-fallbacks)
*   [Auth profiles](https://docs.openclaw.ai/cli/models#auth-profiles)

Agents and sessions

# models

# [​](https://docs.openclaw.ai/cli/models#openclaw-models)

`openclaw models`

Model discovery, scanning, and configuration (default model, fallbacks, auth profiles).Related:
*   Providers + models: [Models](https://docs.openclaw.ai/providers/models)
*   Provider auth setup: [Getting started](https://docs.openclaw.ai/start/getting-started)

## [​](https://docs.openclaw.ai/cli/models#common-commands)

Common commands

```
openclaw models status
openclaw models list
openclaw models set <model-or-alias>
openclaw models scan
```

`openclaw models status` shows the resolved default/fallbacks plus an auth overview. When provider usage snapshots are available, the OAuth/token status section includes provider usage headers. Add `--probe` to run live auth probes against each configured provider profile. Probes are real requests (may consume tokens and trigger rate limits). Use `--agent <id>` to inspect a configured agent’s model/auth state. When omitted, the command uses `OPENCLAW_AGENT_DIR`/`PI_CODING_AGENT_DIR` if set, otherwise the configured default agent.Notes:
*   `models set <model-or-alias>` accepts `provider/model` or an alias.
*   Model refs are parsed by splitting on the **first**`/`. If the model ID includes `/` (OpenRouter-style), include the provider prefix (example: `openrouter/moonshotai/kimi-k2`).
*   If you omit the provider, OpenClaw treats the input as an alias or a model for the **default provider** (only works when there is no `/` in the model ID).
*   `models status` may show `marker(<value>)` in auth output for non-secret placeholders (for example `OPENAI_API_KEY`, `secretref-managed`, `minimax-oauth`, `oauth:chutes`, `ollama-local`) instead of masking them as secrets.

### [​](https://docs.openclaw.ai/cli/models#models-status)

`models status`

Options:
*   `--json`
*   `--plain`
*   `--check` (exit 1=expired/missing, 2=expiring)
*   `--probe` (live probe of configured auth profiles)
*   `--probe-provider <name>` (probe one provider)
*   `--probe-profile <id>` (repeat or comma-separated profile ids)
*   `--probe-timeout <ms>`
*   `--probe-concurrency <n>`
*   `--probe-max-tokens <n>`
*   `--agent <id>` (configured agent id; overrides `OPENCLAW_AGENT_DIR`/`PI_CODING_AGENT_DIR`)

## [​](https://docs.openclaw.ai/cli/models#aliases-+-fallbacks)

Aliases + fallbacks

```
openclaw models aliases list
openclaw models fallbacks list
```

## [​](https://docs.openclaw.ai/cli/models#auth-profiles)

Auth profiles

```
openclaw models auth add
openclaw models auth login --provider <id>
openclaw models auth setup-token
openclaw models auth paste-token
```

`models auth login` runs a provider plugin’s auth flow (OAuth/API key). Use `openclaw plugins list` to see which providers are installed.Examples:

```
openclaw models auth login --provider anthropic --method cli --set-default
openclaw models auth login --provider openai-codex --set-default
```

Notes:
*   `login --provider anthropic --method cli --set-default` reuses a local Claude CLI login and rewrites the main Anthropic default-model path to `claude-cli/...`.
*   `setup-token` prompts for a setup-token value (generate it with `claude setup-token` on any machine).
*   `paste-token` accepts a token string generated elsewhere or from automation.
*   Anthropic policy note: setup-token support is technical compatibility. Anthropic has blocked some subscription usage outside Claude Code in the past, so verify current terms before using it broadly.

[message](https://docs.openclaw.ai/cli/message)[sessions](https://docs.openclaw.ai/cli/sessions)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
