# providers/openai

Source: https://docs.openclaw.ai/providers/openai

Title: OpenAI - OpenClaw

URL Source: https://docs.openclaw.ai/providers/openai

Markdown Content:
# OpenAI - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/openai#content-area)

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

OpenAI

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

*   [OpenAI](https://docs.openclaw.ai/providers/openai#openai)
*   [Option A: OpenAI API key (OpenAI Platform)](https://docs.openclaw.ai/providers/openai#option-a-openai-api-key-openai-platform)
*   [CLI setup](https://docs.openclaw.ai/providers/openai#cli-setup)
*   [Config snippet](https://docs.openclaw.ai/providers/openai#config-snippet)
*   [Option B: OpenAI Code (Codex) subscription](https://docs.openclaw.ai/providers/openai#option-b-openai-code-codex-subscription)
*   [CLI setup (Codex OAuth)](https://docs.openclaw.ai/providers/openai#cli-setup-codex-oauth)
*   [Config snippet (Codex subscription)](https://docs.openclaw.ai/providers/openai#config-snippet-codex-subscription)
*   [Transport default](https://docs.openclaw.ai/providers/openai#transport-default)
*   [OpenAI WebSocket warm-up](https://docs.openclaw.ai/providers/openai#openai-websocket-warm-up)
*   [Disable warm-up](https://docs.openclaw.ai/providers/openai#disable-warm-up)
*   [Enable warm-up explicitly](https://docs.openclaw.ai/providers/openai#enable-warm-up-explicitly)
*   [OpenAI priority processing](https://docs.openclaw.ai/providers/openai#openai-priority-processing)
*   [OpenAI fast mode](https://docs.openclaw.ai/providers/openai#openai-fast-mode)
*   [OpenAI Responses server-side compaction](https://docs.openclaw.ai/providers/openai#openai-responses-server-side-compaction)
*   [Enable server-side compaction explicitly](https://docs.openclaw.ai/providers/openai#enable-server-side-compaction-explicitly)
*   [Enable with a custom threshold](https://docs.openclaw.ai/providers/openai#enable-with-a-custom-threshold)
*   [Disable server-side compaction](https://docs.openclaw.ai/providers/openai#disable-server-side-compaction)
*   [Notes](https://docs.openclaw.ai/providers/openai#notes)

Providers

# OpenAI

# [​](https://docs.openclaw.ai/providers/openai#openai)

OpenAI

OpenAI provides developer APIs for GPT models. Codex supports **ChatGPT sign-in** for subscription access or **API key** sign-in for usage-based access. Codex cloud requires ChatGPT sign-in. OpenAI explicitly supports subscription OAuth usage in external tools/workflows like OpenClaw.
## [​](https://docs.openclaw.ai/providers/openai#option-a-openai-api-key-openai-platform)

Option A: OpenAI API key (OpenAI Platform)

**Best for:** direct API access and usage-based billing. Get your API key from the OpenAI dashboard.
### [​](https://docs.openclaw.ai/providers/openai#cli-setup)

CLI setup

```
openclaw onboard --auth-choice openai-api-key
# or non-interactive
openclaw onboard --openai-api-key "$OPENAI_API_KEY"
```

### [​](https://docs.openclaw.ai/providers/openai#config-snippet)

Config snippet

```
{
  env: { OPENAI_API_KEY: "sk-..." },
  agents: { defaults: { model: { primary: "openai/gpt-5.4" } } },
}
```

OpenAI’s current API model docs list `gpt-5.4` and `gpt-5.4-pro` for direct OpenAI API usage. OpenClaw forwards both through the `openai/*` Responses path. OpenClaw intentionally suppresses the stale `openai/gpt-5.3-codex-spark` row, because direct OpenAI API calls reject it in live traffic.OpenClaw does **not** expose `openai/gpt-5.3-codex-spark` on the direct OpenAI API path. `pi-ai` still ships a built-in row for that model, but live OpenAI API requests currently reject it. Spark is treated as Codex-only in OpenClaw.
## [​](https://docs.openclaw.ai/providers/openai#option-b-openai-code-codex-subscription)

Option B: OpenAI Code (Codex) subscription

**Best for:** using ChatGPT/Codex subscription access instead of an API key. Codex cloud requires ChatGPT sign-in, while the Codex CLI supports ChatGPT or API key sign-in.
### [​](https://docs.openclaw.ai/providers/openai#cli-setup-codex-oauth)

CLI setup (Codex OAuth)

```
# Run Codex OAuth in the wizard
openclaw onboard --auth-choice openai-codex

# Or run OAuth directly
openclaw models auth login --provider openai-codex
```

### [​](https://docs.openclaw.ai/providers/openai#config-snippet-codex-subscription)

Config snippet (Codex subscription)

```
{
  agents: { defaults: { model: { primary: "openai-codex/gpt-5.4" } } },
}
```

OpenAI’s current Codex docs list `gpt-5.4` as the current Codex model. OpenClaw maps that to `openai-codex/gpt-5.4` for ChatGPT/Codex OAuth usage.If your Codex account is entitled to Codex Spark, OpenClaw also supports:
*   `openai-codex/gpt-5.3-codex-spark`

OpenClaw treats Codex Spark as Codex-only. It does not expose a direct `openai/gpt-5.3-codex-spark` API-key path.OpenClaw also preserves `openai-codex/gpt-5.3-codex-spark` when `pi-ai` discovers it. Treat it as entitlement-dependent and experimental: Codex Spark is separate from GPT-5.4 `/fast`, and availability depends on the signed-in Codex / ChatGPT account.
### [​](https://docs.openclaw.ai/providers/openai#transport-default)

Transport default

OpenClaw uses `pi-ai` for model streaming. For both `openai/*` and `openai-codex/*`, default transport is `"auto"` (WebSocket-first, then SSE fallback).You can set `agents.defaults.models.<provider/model>.params.transport`:
*   `"sse"`: force SSE
*   `"websocket"`: force WebSocket
*   `"auto"`: try WebSocket, then fall back to SSE

For `openai/*` (Responses API), OpenClaw also enables WebSocket warm-up by default (`openaiWsWarmup: true`) when WebSocket transport is used.Related OpenAI docs:
*   [Realtime API with WebSocket](https://platform.openai.com/docs/guides/realtime-websocket)
*   [Streaming API responses (SSE)](https://platform.openai.com/docs/guides/streaming-responses)

```
{
  agents: {
    defaults: {
      model: { primary: "openai-codex/gpt-5.4" },
      models: {
        "openai-codex/gpt-5.4": {
          params: {
            transport: "auto",
          },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/openai#openai-websocket-warm-up)

OpenAI WebSocket warm-up

OpenAI docs describe warm-up as optional. OpenClaw enables it by default for `openai/*` to reduce first-turn latency when using WebSocket transport.
### [​](https://docs.openclaw.ai/providers/openai#disable-warm-up)

Disable warm-up

```
{
  agents: {
    defaults: {
      models: {
        "openai/gpt-5.4": {
          params: {
            openaiWsWarmup: false,
          },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/openai#enable-warm-up-explicitly)

Enable warm-up explicitly

```
{
  agents: {
    defaults: {
      models: {
        "openai/gpt-5.4": {
          params: {
            openaiWsWarmup: true,
          },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/openai#openai-priority-processing)

OpenAI priority processing

OpenAI’s API exposes priority processing via `service_tier=priority`. In OpenClaw, set `agents.defaults.models["openai/<model>"].params.serviceTier` to pass that field through on direct `openai/*` Responses requests.

```
{
  agents: {
    defaults: {
      models: {
        "openai/gpt-5.4": {
          params: {
            serviceTier: "priority",
          },
        },
      },
    },
  },
}
```

Supported values are `auto`, `default`, `flex`, and `priority`.
### [​](https://docs.openclaw.ai/providers/openai#openai-fast-mode)

OpenAI fast mode

OpenClaw exposes a shared fast-mode toggle for both `openai/*` and `openai-codex/*` sessions:
*   Chat/UI: `/fast status|on|off`
*   Config: `agents.defaults.models["<provider>/<model>"].params.fastMode`

When fast mode is enabled, OpenClaw applies a low-latency OpenAI profile:
*   `reasoning.effort = "low"` when the payload does not already specify reasoning
*   `text.verbosity = "low"` when the payload does not already specify verbosity
*   `service_tier = "priority"` for direct `openai/*` Responses calls to `api.openai.com`

Example:

```
{
  agents: {
    defaults: {
      models: {
        "openai/gpt-5.4": {
          params: {
            fastMode: true,
          },
        },
        "openai-codex/gpt-5.4": {
          params: {
            fastMode: true,
          },
        },
      },
    },
  },
}
```

Session overrides win over config. Clearing the session override in the Sessions UI returns the session to the configured default.
### [​](https://docs.openclaw.ai/providers/openai#openai-responses-server-side-compaction)

OpenAI Responses server-side compaction

For direct OpenAI Responses models (`openai/*` using `api: "openai-responses"` with `baseUrl` on `api.openai.com`), OpenClaw now auto-enables OpenAI server-side compaction payload hints:
*   Forces `store: true` (unless model compat sets `supportsStore: false`)
*   Injects `context_management: [{ type: "compaction", compact_threshold: ... }]`

By default, `compact_threshold` is `70%` of model `contextWindow` (or `80000` when unavailable).
### [​](https://docs.openclaw.ai/providers/openai#enable-server-side-compaction-explicitly)

Enable server-side compaction explicitly

Use this when you want to force `context_management` injection on compatible Responses models (for example Azure OpenAI Responses):

```
{
  agents: {
    defaults: {
      models: {
        "azure-openai-responses/gpt-5.4": {
          params: {
            responsesServerCompaction: true,
          },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/openai#enable-with-a-custom-threshold)

Enable with a custom threshold

```
{
  agents: {
    defaults: {
      models: {
        "openai/gpt-5.4": {
          params: {
            responsesServerCompaction: true,
            responsesCompactThreshold: 120000,
          },
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/openai#disable-server-side-compaction)

Disable server-side compaction

```
{
  agents: {
    defaults: {
      models: {
        "openai/gpt-5.4": {
          params: {
            responsesServerCompaction: false,
          },
        },
      },
    },
  },
}
```

`responsesServerCompaction` only controls `context_management` injection. Direct OpenAI Responses models still force `store: true` unless compat sets `supportsStore: false`.
## [​](https://docs.openclaw.ai/providers/openai#notes)

Notes

*   Model refs always use `provider/model` (see [/concepts/models](https://docs.openclaw.ai/concepts/models)).
*   Auth details + reuse rules are in [/concepts/oauth](https://docs.openclaw.ai/concepts/oauth).

[Ollama](https://docs.openclaw.ai/providers/ollama)[OpenCode](https://docs.openclaw.ai/providers/opencode)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
