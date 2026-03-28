# cli/onboard

Source: https://docs.openclaw.ai/cli/onboard

Title: onboard - OpenClaw

URL Source: https://docs.openclaw.ai/cli/onboard

Markdown Content:
# onboard - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/onboard#content-area)

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

onboard

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

*   [openclaw onboard](https://docs.openclaw.ai/cli/onboard#openclaw-onboard)
*   [Related guides](https://docs.openclaw.ai/cli/onboard#related-guides)
*   [Examples](https://docs.openclaw.ai/cli/onboard#examples)
*   [Common follow-up commands](https://docs.openclaw.ai/cli/onboard#common-follow-up-commands)

Gateway and service

# onboard

# [​](https://docs.openclaw.ai/cli/onboard#openclaw-onboard)

`openclaw onboard`

Interactive onboarding for local or remote Gateway setup.
## [​](https://docs.openclaw.ai/cli/onboard#related-guides)

Related guides

*   CLI onboarding hub: [Onboarding (CLI)](https://docs.openclaw.ai/start/wizard)
*   Onboarding overview: [Onboarding Overview](https://docs.openclaw.ai/start/onboarding-overview)
*   CLI onboarding reference: [CLI Setup Reference](https://docs.openclaw.ai/start/wizard-cli-reference)
*   CLI automation: [CLI Automation](https://docs.openclaw.ai/start/wizard-cli-automation)
*   macOS onboarding: [Onboarding (macOS App)](https://docs.openclaw.ai/start/onboarding)

## [​](https://docs.openclaw.ai/cli/onboard#examples)

Examples

```
openclaw onboard
openclaw onboard --flow quickstart
openclaw onboard --flow manual
openclaw onboard --mode remote --remote-url wss://gateway-host:18789
```

For plaintext private-network `ws://` targets (trusted networks only), set `OPENCLAW_ALLOW_INSECURE_PRIVATE_WS=1` in the onboarding process environment.Non-interactive custom provider:

```
openclaw onboard --non-interactive \
  --auth-choice custom-api-key \
  --custom-base-url "https://llm.example.com/v1" \
  --custom-model-id "foo-large" \
  --custom-api-key "$CUSTOM_API_KEY" \
  --secret-input-mode plaintext \
  --custom-compatibility openai
```

`--custom-api-key` is optional in non-interactive mode. If omitted, onboarding checks `CUSTOM_API_KEY`.Non-interactive Ollama:

```
openclaw onboard --non-interactive \
  --auth-choice ollama \
  --custom-base-url "http://ollama-host:11434" \
  --custom-model-id "qwen3.5:27b" \
  --accept-risk
```

`--custom-base-url` defaults to `http://127.0.0.1:11434`. `--custom-model-id` is optional; if omitted, onboarding uses Ollama’s suggested defaults. Cloud model IDs such as `kimi-k2.5:cloud` also work here.Store provider keys as refs instead of plaintext:

```
openclaw onboard --non-interactive \
  --auth-choice openai-api-key \
  --secret-input-mode ref \
  --accept-risk
```

With `--secret-input-mode ref`, onboarding writes env-backed refs instead of plaintext key values. For auth-profile backed providers this writes `keyRef` entries; for custom providers this writes `models.providers.<id>.apiKey` as an env ref (for example `{ source: "env", provider: "default", id: "CUSTOM_API_KEY" }`).Non-interactive `ref` mode contract:
*   Set the provider env var in the onboarding process environment (for example `OPENAI_API_KEY`).
*   Do not pass inline key flags (for example `--openai-api-key`) unless that env var is also set.
*   If an inline key flag is passed without the required env var, onboarding fails fast with guidance.

Gateway token options in non-interactive mode:
*   `--gateway-auth token --gateway-token <token>` stores a plaintext token.
*   `--gateway-auth token --gateway-token-ref-env <name>` stores `gateway.auth.token` as an env SecretRef.
*   `--gateway-token` and `--gateway-token-ref-env` are mutually exclusive.
*   `--gateway-token-ref-env` requires a non-empty env var in the onboarding process environment.
*   With `--install-daemon`, when token auth requires a token, SecretRef-managed gateway tokens are validated but not persisted as resolved plaintext in supervisor service environment metadata.
*   With `--install-daemon`, if token mode requires a token and the configured token SecretRef is unresolved, onboarding fails closed with remediation guidance.
*   With `--install-daemon`, if both `gateway.auth.token` and `gateway.auth.password` are configured and `gateway.auth.mode` is unset, onboarding blocks install until mode is set explicitly.

Example:

```
export OPENCLAW_GATEWAY_TOKEN="your-token"
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice skip \
  --gateway-auth token \
  --gateway-token-ref-env OPENCLAW_GATEWAY_TOKEN \
  --accept-risk
```

Non-interactive local gateway health:
*   Unless you pass `--skip-health`, onboarding waits for a reachable local gateway before it exits successfully.
*   `--install-daemon` starts the managed gateway install path first. Without it, you must already have a local gateway running, for example `openclaw gateway run`.
*   If you only want config/workspace/bootstrap writes in automation, use `--skip-health`.
*   On native Windows, `--install-daemon` tries Scheduled Tasks first and falls back to a per-user Startup-folder login item if task creation is denied.

Interactive onboarding behavior with reference mode:
*   Choose **Use secret reference** when prompted.
*   Then choose either:
    *   Environment variable
    *   Configured secret provider (`file` or `exec`)

*   Onboarding performs a fast preflight validation before saving the ref.
    *   If validation fails, onboarding shows the error and lets you retry.

Non-interactive Z.AI endpoint choices:Note: `--auth-choice zai-api-key` now auto-detects the best Z.AI endpoint for your key (prefers the general API with `zai/glm-5`). If you specifically want the GLM Coding Plan endpoints, pick `zai-coding-global` or `zai-coding-cn`.

```
# Promptless endpoint selection
openclaw onboard --non-interactive \
  --auth-choice zai-coding-global \
  --zai-api-key "$ZAI_API_KEY"

# Other Z.AI endpoint choices:
# --auth-choice zai-coding-cn
# --auth-choice zai-global
# --auth-choice zai-cn
```

Non-interactive Mistral example:

```
openclaw onboard --non-interactive \
  --auth-choice mistral-api-key \
  --mistral-api-key "$MISTRAL_API_KEY"
```

Flow notes:
*   `quickstart`: minimal prompts, auto-generates a gateway token.
*   `manual`: full prompts for port/bind/auth (alias of `advanced`).
*   Local onboarding DM scope behavior: [CLI Setup Reference](https://docs.openclaw.ai/start/wizard-cli-reference#outputs-and-internals).
*   Fastest first chat: `openclaw dashboard` (Control UI, no channel setup).
*   Custom Provider: connect any OpenAI or Anthropic compatible endpoint, including hosted providers not listed. Use Unknown to auto-detect.

## [​](https://docs.openclaw.ai/cli/onboard#common-follow-up-commands)

Common follow-up commands

```
openclaw configure
openclaw agents add <name>
```

`--json` does not imply non-interactive mode. Use `--non-interactive` for scripts.

[logs](https://docs.openclaw.ai/cli/logs)[reset](https://docs.openclaw.ai/cli/reset)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
