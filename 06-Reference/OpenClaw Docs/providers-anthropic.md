# providers/anthropic

Source: https://docs.openclaw.ai/providers/anthropic

Title: Anthropic - OpenClaw

URL Source: https://docs.openclaw.ai/providers/anthropic

Markdown Content:
# Anthropic - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/anthropic#content-area)

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

Providers

Anthropic

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Provider Directory](https://docs.openclaw.ai/providers)
*   [Model Provider Quickstart](https://docs.openclaw.ai/providers/models)

##### Concepts and configuration

*   [Models CLI](https://docs.openclaw.ai/concepts/models)
*   [Model Providers](https://docs.openclaw.ai/concepts/model-providers)
*   [Model Failover](https://docs.openclaw.ai/concepts/model-failover)

##### Providers

*   [Anthropic](https://docs.openclaw.ai/providers/anthropic)
*   [Amazon Bedrock](https://docs.openclaw.ai/providers/bedrock)
*   [Claude Max API Proxy](https://docs.openclaw.ai/providers/claude-max-api-proxy)
*   [Cloudflare AI Gateway](https://docs.openclaw.ai/providers/cloudflare-ai-gateway)
*   [Deepgram](https://docs.openclaw.ai/providers/deepgram)
*   [Deepseek](https://docs.openclaw.ai/providers/deepseek)
*   [GitHub Copilot](https://docs.openclaw.ai/providers/github-copilot)
*   [GLM Models](https://docs.openclaw.ai/providers/glm)
*   [Google (Gemini)](https://docs.openclaw.ai/providers/google)
*   [Groq](https://docs.openclaw.ai/providers/groq)
*   [Hugging Face (Inference)](https://docs.openclaw.ai/providers/huggingface)
*   [Kilo Gateway](https://docs.openclaw.ai/providers/kilocode)
*   [LiteLLM](https://docs.openclaw.ai/providers/litellm)
*   [MiniMax](https://docs.openclaw.ai/providers/minimax)
*   [Mistral](https://docs.openclaw.ai/providers/mistral)
*   [Moonshot AI](https://docs.openclaw.ai/providers/moonshot)
*   [NVIDIA](https://docs.openclaw.ai/providers/nvidia)
*   [Ollama](https://docs.openclaw.ai/providers/ollama)
*   [OpenAI](https://docs.openclaw.ai/providers/openai)
*   [OpenCode](https://docs.openclaw.ai/providers/opencode)
*   [OpenCode Go](https://docs.openclaw.ai/providers/opencode-go)
*   [OpenRouter](https://docs.openclaw.ai/providers/openrouter)
*   [Perplexity (Provider)](https://docs.openclaw.ai/providers/perplexity-provider)
*   [Qianfan](https://docs.openclaw.ai/providers/qianfan)
*   [Qwen / Model Studio](https://docs.openclaw.ai/providers/qwen_modelstudio)
*   [Qwen](https://docs.openclaw.ai/providers/qwen)
*   [SGLang](https://docs.openclaw.ai/providers/sglang)
*   [Synthetic](https://docs.openclaw.ai/providers/synthetic)
*   [Together AI](https://docs.openclaw.ai/providers/together)
*   [Venice AI](https://docs.openclaw.ai/providers/venice)
*   [Vercel AI Gateway](https://docs.openclaw.ai/providers/vercel-ai-gateway)
*   [vLLM](https://docs.openclaw.ai/providers/vllm)
*   [Volcengine (Doubao)](https://docs.openclaw.ai/providers/volcengine)
*   [xAI](https://docs.openclaw.ai/providers/xai)
*   [Xiaomi MiMo](https://docs.openclaw.ai/providers/xiaomi)
*   [Z.AI](https://docs.openclaw.ai/providers/zai)

On this page

*   [Anthropic (Claude)](https://docs.openclaw.ai/providers/anthropic#anthropic-claude)
*   [Option A: Anthropic API key](https://docs.openclaw.ai/providers/anthropic#option-a-anthropic-api-key)
*   [CLI setup](https://docs.openclaw.ai/providers/anthropic#cli-setup)
*   [Claude CLI config snippet](https://docs.openclaw.ai/providers/anthropic#claude-cli-config-snippet)
*   [Thinking defaults (Claude 4.6)](https://docs.openclaw.ai/providers/anthropic#thinking-defaults-claude-4-6)
*   [Fast mode (Anthropic API)](https://docs.openclaw.ai/providers/anthropic#fast-mode-anthropic-api)
*   [Prompt caching (Anthropic API)](https://docs.openclaw.ai/providers/anthropic#prompt-caching-anthropic-api)
*   [Configuration](https://docs.openclaw.ai/providers/anthropic#configuration)
*   [Defaults](https://docs.openclaw.ai/providers/anthropic#defaults)
*   [Per-agent cacheRetention overrides](https://docs.openclaw.ai/providers/anthropic#per-agent-cacheretention-overrides)
*   [Bedrock Claude notes](https://docs.openclaw.ai/providers/anthropic#bedrock-claude-notes)
*   [Legacy parameter](https://docs.openclaw.ai/providers/anthropic#legacy-parameter)
*   [1M context window (Anthropic beta)](https://docs.openclaw.ai/providers/anthropic#1m-context-window-anthropic-beta)
*   [Option B: Claude CLI as the message provider](https://docs.openclaw.ai/providers/anthropic#option-b-claude-cli-as-the-message-provider)
*   [Requirements](https://docs.openclaw.ai/providers/anthropic#requirements)
*   [Config snippet](https://docs.openclaw.ai/providers/anthropic#config-snippet)
*   [What you get](https://docs.openclaw.ai/providers/anthropic#what-you-get)
*   [Migrate from Anthropic auth to Claude CLI](https://docs.openclaw.ai/providers/anthropic#migrate-from-anthropic-auth-to-claude-cli)
*   [Important limits](https://docs.openclaw.ai/providers/anthropic#important-limits)
*   [Option C: Claude setup-token](https://docs.openclaw.ai/providers/anthropic#option-c-claude-setup-token)
*   [Where to get a setup-token](https://docs.openclaw.ai/providers/anthropic#where-to-get-a-setup-token)
*   [CLI setup (setup-token)](https://docs.openclaw.ai/providers/anthropic#cli-setup-setup-token)
*   [Config snippet (setup-token)](https://docs.openclaw.ai/providers/anthropic#config-snippet-setup-token)
*   [Notes](https://docs.openclaw.ai/providers/anthropic#notes)
*   [Troubleshooting](https://docs.openclaw.ai/providers/anthropic#troubleshooting)

Providers

# Anthropic

# [​](https://docs.openclaw.ai/providers/anthropic#anthropic-claude)

Anthropic (Claude)

Anthropic builds the **Claude** model family and provides access via an API. In OpenClaw you can authenticate with an API key or a **setup-token**.
## [​](https://docs.openclaw.ai/providers/anthropic#option-a-anthropic-api-key)

Option A: Anthropic API key

**Best for:** standard API access and usage-based billing. Create your API key in the Anthropic Console.
### [​](https://docs.openclaw.ai/providers/anthropic#cli-setup)

CLI setup

```
openclaw onboard
# choose: Anthropic API key

# or non-interactive
openclaw onboard --anthropic-api-key "$ANTHROPIC_API_KEY"
```

### [​](https://docs.openclaw.ai/providers/anthropic#claude-cli-config-snippet)

Claude CLI config snippet

```
{
  env: { ANTHROPIC_API_KEY: "sk-ant-..." },
  agents: { defaults: { model: { primary: "anthropic/claude-opus-4-6" } } },
}
```

## [​](https://docs.openclaw.ai/providers/anthropic#thinking-defaults-claude-4-6)

Thinking defaults (Claude 4.6)

*   Anthropic Claude 4.6 models default to `adaptive` thinking in OpenClaw when no explicit thinking level is set.
*   You can override per-message (`/think:<level>`) or in model params: `agents.defaults.models["anthropic/<model>"].params.thinking`.
*   Related Anthropic docs:
    *   [Adaptive thinking](https://platform.claude.com/docs/en/build-with-claude/adaptive-thinking)
    *   [Extended thinking](https://platform.claude.com/docs/en/build-with-claude/extended-thinking)

## [​](https://docs.openclaw.ai/providers/anthropic#fast-mode-anthropic-api)

Fast mode (Anthropic API)

OpenClaw’s shared `/fast` toggle also supports direct Anthropic API-key traffic.
*   `/fast on` maps to `service_tier: "auto"`
*   `/fast off` maps to `service_tier: "standard_only"`
*   Config default:

```
{
  agents: {
    defaults: {
      models: {
        "anthropic/claude-sonnet-4-6": {
          params: { fastMode: true },
        },
      },
    },
  },
}
```

Important limits:
*   This is **API-key only**. Anthropic setup-token / OAuth auth does not honor OpenClaw fast-mode tier injection.
*   OpenClaw only injects Anthropic service tiers for direct `api.anthropic.com` requests. If you route `anthropic/*` through a proxy or gateway, `/fast` leaves `service_tier` untouched.
*   Anthropic reports the effective tier on the response under `usage.service_tier`. On accounts without Priority Tier capacity, `service_tier: "auto"` may still resolve to `standard`.

## [​](https://docs.openclaw.ai/providers/anthropic#prompt-caching-anthropic-api)

Prompt caching (Anthropic API)

OpenClaw supports Anthropic’s prompt caching feature. This is **API-only**; subscription auth does not honor cache settings.
### [​](https://docs.openclaw.ai/providers/anthropic#configuration)

Configuration

Use the `cacheRetention` parameter in your model config:

| Value | Cache Duration | Description |
| --- | --- | --- |
| `none` | No caching | Disable prompt caching |
| `short` | 5 minutes | Default for API Key auth |
| `long` | 1 hour | Extended cache (requires beta flag) |

```
{
  agents: {
    defaults: {
      models: {
        "anthropic/claude-opus-4-6": {
          params: { cacheRetention: "long" },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/anthropic#defaults)

Defaults

When using Anthropic API Key authentication, OpenClaw automatically applies `cacheRetention: "short"` (5-minute cache) for all Anthropic models. You can override this by explicitly setting `cacheRetention` in your config.
### [​](https://docs.openclaw.ai/providers/anthropic#per-agent-cacheretention-overrides)

Per-agent cacheRetention overrides

Use model-level params as your baseline, then override specific agents via `agents.list[].params`.

```
{
  agents: {
    defaults: {
      model: { primary: "anthropic/claude-opus-4-6" },
      models: {
        "anthropic/claude-opus-4-6": {
          params: { cacheRetention: "long" }, // baseline for most agents
        },
      },
    },
    list: [
      { id: "research", default: true },
      { id: "alerts", params: { cacheRetention: "none" } }, // override for this agent only
    ],
  },
}
```

Config merge order for cache-related params:
1.   `agents.defaults.models["provider/model"].params`
2.   `agents.list[].params` (matching `id`, overrides by key)

This lets one agent keep a long-lived cache while another agent on the same model disables caching to avoid write costs on bursty/low-reuse traffic.
### [​](https://docs.openclaw.ai/providers/anthropic#bedrock-claude-notes)

Bedrock Claude notes

*   Anthropic Claude models on Bedrock (`amazon-bedrock/*anthropic.claude*`) accept `cacheRetention` pass-through when configured.
*   Non-Anthropic Bedrock models are forced to `cacheRetention: "none"` at runtime.
*   Anthropic API-key smart defaults also seed `cacheRetention: "short"` for Claude-on-Bedrock model refs when no explicit value is set.

### [​](https://docs.openclaw.ai/providers/anthropic#legacy-parameter)

Legacy parameter

The older `cacheControlTtl` parameter is still supported for backwards compatibility:
*   `"5m"` maps to `short`
*   `"1h"` maps to `long`

We recommend migrating to the new `cacheRetention` parameter.OpenClaw includes the `extended-cache-ttl-2025-04-11` beta flag for Anthropic API requests; keep it if you override provider headers (see [/gateway/configuration](https://docs.openclaw.ai/gateway/configuration)).
## [​](https://docs.openclaw.ai/providers/anthropic#1m-context-window-anthropic-beta)

1M context window (Anthropic beta)

Anthropic’s 1M context window is beta-gated. In OpenClaw, enable it per model with `params.context1m: true` for supported Opus/Sonnet models.

```
{
  agents: {
    defaults: {
      models: {
        "anthropic/claude-opus-4-6": {
          params: { context1m: true },
        },
      },
    },
  },
}
```

OpenClaw maps this to `anthropic-beta: context-1m-2025-08-07` on Anthropic requests.This only activates when `params.context1m` is explicitly set to `true` for that model.Requirement: Anthropic must allow long-context usage on that credential (typically API key billing, or a subscription account with Extra Usage enabled). Otherwise Anthropic returns: `HTTP 429: rate_limit_error: Extra usage is required for long context requests`.Note: Anthropic currently rejects `context-1m-*` beta requests when using OAuth/subscription tokens (`sk-ant-oat-*`). OpenClaw automatically skips the context1m beta header for OAuth auth and keeps the required OAuth betas.
## [​](https://docs.openclaw.ai/providers/anthropic#option-b-claude-cli-as-the-message-provider)

Option B: Claude CLI as the message provider

**Best for:** a single-user gateway host that already has Claude CLI installed and signed in with a Claude subscription.This path uses the local `claude` binary for model inference instead of calling the Anthropic API directly. OpenClaw treats it as a **CLI backend provider** with model refs like:
*   `claude-cli/claude-sonnet-4-6`
*   `claude-cli/claude-opus-4-6`

How it works:
1.   OpenClaw launches `claude -p --output-format json ...` on the **gateway host**.
2.   The first turn sends `--session-id <uuid>`.
3.   Follow-up turns reuse the stored Claude session via `--resume <sessionId>`.
4.   Your chat messages still go through the normal OpenClaw message pipeline, but the actual model reply is produced by Claude CLI.

### [​](https://docs.openclaw.ai/providers/anthropic#requirements)

Requirements

*   Claude CLI installed on the gateway host and available on PATH, or configured with an absolute command path.
*   Claude CLI already authenticated on that same host:

```
claude auth status
```

*   OpenClaw auto-loads the bundled Anthropic plugin at gateway startup when your config explicitly references `claude-cli/...` or `claude-cli` backend config.

### [​](https://docs.openclaw.ai/providers/anthropic#config-snippet)

Config snippet

```
{
  agents: {
    defaults: {
      model: {
        primary: "claude-cli/claude-sonnet-4-6",
      },
      models: {
        "claude-cli/claude-sonnet-4-6": {},
      },
      sandbox: { mode: "off" },
    },
  },
}
```

If the `claude` binary is not on the gateway host PATH:

```
{
  agents: {
    defaults: {
      cliBackends: {
        "claude-cli": {
          command: "/opt/homebrew/bin/claude",
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/anthropic#what-you-get)

What you get

*   Claude subscription auth reused from the local CLI
*   Normal OpenClaw message/session routing
*   Claude CLI session continuity across turns

### [​](https://docs.openclaw.ai/providers/anthropic#migrate-from-anthropic-auth-to-claude-cli)

Migrate from Anthropic auth to Claude CLI

If you currently use `anthropic/...` with a setup-token or API key and want to switch the same gateway host to Claude CLI:

```
openclaw models auth login --provider anthropic --method cli --set-default
```

Or in onboarding:

```
openclaw onboard --auth-choice anthropic-cli
```

What this does:
*   verifies Claude CLI is already signed in on the gateway host
*   switches the default model to `claude-cli/...`
*   rewrites Anthropic default-model fallbacks like `anthropic/claude-opus-4-6` to `claude-cli/claude-opus-4-6`
*   adds matching `claude-cli/...` entries to `agents.defaults.models`

What it does **not** do:
*   delete your existing Anthropic auth profiles
*   remove every old `anthropic/...` config reference outside the main default model/allowlist path

That makes rollback simple: change the default model back to `anthropic/...` if you need to.
### [​](https://docs.openclaw.ai/providers/anthropic#important-limits)

Important limits

*   This is **not** the Anthropic API provider. It is the local CLI runtime.
*   Tools are disabled on the OpenClaw side for CLI backend runs.
*   Text in, text out. No OpenClaw streaming handoff.
*   Best fit for a personal gateway host, not shared multi-user billing setups.

More details: [/gateway/cli-backends](https://docs.openclaw.ai/gateway/cli-backends)
## [​](https://docs.openclaw.ai/providers/anthropic#option-c-claude-setup-token)

Option C: Claude setup-token

**Best for:** using your Claude subscription.
### [​](https://docs.openclaw.ai/providers/anthropic#where-to-get-a-setup-token)

Where to get a setup-token

Setup-tokens are created by the **Claude Code CLI**, not the Anthropic Console. You can run this on **any machine**:

```
claude setup-token
```

Paste the token into OpenClaw (wizard: **Anthropic token (paste setup-token)**), or run it on the gateway host:

```
openclaw models auth setup-token --provider anthropic
```

If you generated the token on a different machine, paste it:

```
openclaw models auth paste-token --provider anthropic
```

### [​](https://docs.openclaw.ai/providers/anthropic#cli-setup-setup-token)

CLI setup (setup-token)

```
# Paste a setup-token during setup
openclaw onboard --auth-choice setup-token
```

### [​](https://docs.openclaw.ai/providers/anthropic#config-snippet-setup-token)

Config snippet (setup-token)

```
{
  agents: { defaults: { model: { primary: "anthropic/claude-opus-4-6" } } },
}
```

## [​](https://docs.openclaw.ai/providers/anthropic#notes)

Notes

*   Generate the setup-token with `claude setup-token` and paste it, or run `openclaw models auth setup-token` on the gateway host.
*   If you see “OAuth token refresh failed …” on a Claude subscription, re-auth with a setup-token. See [/gateway/troubleshooting](https://docs.openclaw.ai/gateway/troubleshooting).
*   Auth details + reuse rules are in [/concepts/oauth](https://docs.openclaw.ai/concepts/oauth).

## [​](https://docs.openclaw.ai/providers/anthropic#troubleshooting)

Troubleshooting

**401 errors / token suddenly invalid**
*   Claude subscription auth can expire or be revoked. Re-run `claude setup-token` and paste it into the **gateway host**.
*   If the Claude CLI login lives on a different machine, use `openclaw models auth paste-token --provider anthropic` on the gateway host.

**No API key found for provider “anthropic”**
*   Auth is **per agent**. New agents don’t inherit the main agent’s keys.
*   Re-run onboarding for that agent, or paste a setup-token / API key on the gateway host, then verify with `openclaw models status`.

**No credentials found for profile `anthropic:default`**
*   Run `openclaw models status` to see which auth profile is active.
*   Re-run onboarding, or paste a setup-token / API key for that profile.

**No available auth profile (all in cooldown/unavailable)**
*   Check `openclaw models status --json` for `auth.unusableProfiles`.
*   Add another Anthropic profile or wait for cooldown.

More: [/gateway/troubleshooting](https://docs.openclaw.ai/gateway/troubleshooting) and [/help/faq](https://docs.openclaw.ai/help/faq).

[Model Failover](https://docs.openclaw.ai/concepts/model-failover)[Amazon Bedrock](https://docs.openclaw.ai/providers/bedrock)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
