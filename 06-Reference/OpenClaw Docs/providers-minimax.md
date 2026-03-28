# providers/minimax

Source: https://docs.openclaw.ai/providers/minimax

Title: MiniMax - OpenClaw

URL Source: https://docs.openclaw.ai/providers/minimax

Markdown Content:
# MiniMax - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/minimax#content-area)

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

MiniMax

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

*   [MiniMax](https://docs.openclaw.ai/providers/minimax#minimax)
*   [Model lineup](https://docs.openclaw.ai/providers/minimax#model-lineup)
*   [Choose a setup](https://docs.openclaw.ai/providers/minimax#choose-a-setup)
*   [MiniMax OAuth (Coding Plan) - recommended](https://docs.openclaw.ai/providers/minimax#minimax-oauth-coding-plan-recommended)
*   [MiniMax M2.7 (API key)](https://docs.openclaw.ai/providers/minimax#minimax-m2-7-api-key)
*   [MiniMax M2.7 as fallback (example)](https://docs.openclaw.ai/providers/minimax#minimax-m2-7-as-fallback-example)
*   [Configure via openclaw configure](https://docs.openclaw.ai/providers/minimax#configure-via-openclaw-configure)
*   [Configuration options](https://docs.openclaw.ai/providers/minimax#configuration-options)
*   [Notes](https://docs.openclaw.ai/providers/minimax#notes)
*   [Troubleshooting](https://docs.openclaw.ai/providers/minimax#troubleshooting)
*   [”Unknown model: minimax/MiniMax-M2.7”](https://docs.openclaw.ai/providers/minimax#%E2%80%9Dunknown-model-minimax%2Fminimax-m2-7%E2%80%9D)

Providers

# MiniMax

# [​](https://docs.openclaw.ai/providers/minimax#minimax)

MiniMax

OpenClaw’s MiniMax provider defaults to **MiniMax M2.7**.
## [​](https://docs.openclaw.ai/providers/minimax#model-lineup)

Model lineup

*   `MiniMax-M2.7`: default hosted text model.
*   `MiniMax-M2.7-highspeed`: faster M2.7 text tier.

## [​](https://docs.openclaw.ai/providers/minimax#choose-a-setup)

Choose a setup

### [​](https://docs.openclaw.ai/providers/minimax#minimax-oauth-coding-plan-recommended)

MiniMax OAuth (Coding Plan) - recommended

**Best for:** quick setup with MiniMax Coding Plan via OAuth, no API key required.Enable the bundled OAuth plugin and authenticate:

```
openclaw plugins enable minimax  # skip if already loaded.
openclaw gateway restart  # restart if gateway is already running
openclaw onboard --auth-choice minimax-portal
```

You will be prompted to select an endpoint:
*   **Global** - International users (`api.minimax.io`)
*   **CN** - Users in China (`api.minimaxi.com`)

See [MiniMax plugin README](https://github.com/openclaw/openclaw/tree/main/extensions/minimax) for details.
### [​](https://docs.openclaw.ai/providers/minimax#minimax-m2-7-api-key)

MiniMax M2.7 (API key)

**Best for:** hosted MiniMax with Anthropic-compatible API.Configure via CLI:
*   Run `openclaw configure`
*   Select **Model/auth**
*   Choose a **MiniMax** auth option

```
{
  env: { MINIMAX_API_KEY: "sk-..." },
  agents: { defaults: { model: { primary: "minimax/MiniMax-M2.7" } } },
  models: {
    mode: "merge",
    providers: {
      minimax: {
        baseUrl: "https://api.minimax.io/anthropic",
        apiKey: "${MINIMAX_API_KEY}",
        api: "anthropic-messages",
        models: [
          {
            id: "MiniMax-M2.7",
            name: "MiniMax M2.7",
            reasoning: true,
            input: ["text"],
            cost: { input: 0.3, output: 1.2, cacheRead: 0.03, cacheWrite: 0.12 },
            contextWindow: 200000,
            maxTokens: 8192,
          },
          {
            id: "MiniMax-M2.7-highspeed",
            name: "MiniMax M2.7 Highspeed",
            reasoning: true,
            input: ["text"],
            cost: { input: 0.3, output: 1.2, cacheRead: 0.03, cacheWrite: 0.12 },
            contextWindow: 200000,
            maxTokens: 8192,
          },
        ],
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/providers/minimax#minimax-m2-7-as-fallback-example)

MiniMax M2.7 as fallback (example)

**Best for:** keep your strongest latest-generation model as primary, fail over to MiniMax M2.7. Example below uses Opus as a concrete primary; swap to your preferred latest-gen primary model.

```
{
  env: { MINIMAX_API_KEY: "sk-..." },
  agents: {
    defaults: {
      models: {
        "anthropic/claude-opus-4-6": { alias: "primary" },
        "minimax/MiniMax-M2.7": { alias: "minimax" },
      },
      model: {
        primary: "anthropic/claude-opus-4-6",
        fallbacks: ["minimax/MiniMax-M2.7"],
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/minimax#configure-via-openclaw-configure)

Configure via `openclaw configure`

Use the interactive config wizard to set MiniMax without editing JSON:
1.   Run `openclaw configure`.
2.   Select **Model/auth**.
3.   Choose a **MiniMax** auth option.
4.   Pick your default model when prompted.

## [​](https://docs.openclaw.ai/providers/minimax#configuration-options)

Configuration options

*   `models.providers.minimax.baseUrl`: prefer `https://api.minimax.io/anthropic` (Anthropic-compatible); `https://api.minimax.io/v1` is optional for OpenAI-compatible payloads.
*   `models.providers.minimax.api`: prefer `anthropic-messages`; `openai-completions` is optional for OpenAI-compatible payloads.
*   `models.providers.minimax.apiKey`: MiniMax API key (`MINIMAX_API_KEY`).
*   `models.providers.minimax.models`: define `id`, `name`, `reasoning`, `contextWindow`, `maxTokens`, `cost`.
*   `agents.defaults.models`: alias models you want in the allowlist.
*   `models.mode`: keep `merge` if you want to add MiniMax alongside built-ins.

## [​](https://docs.openclaw.ai/providers/minimax#notes)

Notes

*   Model refs are `minimax/<model>`.
*   Default text model: `MiniMax-M2.7`.
*   Alternate text model: `MiniMax-M2.7-highspeed`.
*   Coding Plan usage API: `https://api.minimaxi.com/v1/api/openplatform/coding_plan/remains` (requires a coding plan key).
*   Update pricing values in `models.json` if you need exact cost tracking.
*   Referral link for MiniMax Coding Plan (10% off): [https://platform.minimax.io/subscribe/coding-plan?code=DbXJTRClnb&source=link](https://platform.minimax.io/subscribe/coding-plan?code=DbXJTRClnb&source=link)
*   See [/concepts/model-providers](https://docs.openclaw.ai/concepts/model-providers) for provider rules.
*   Use `openclaw models list` and `openclaw models set minimax/MiniMax-M2.7` to switch.

## [​](https://docs.openclaw.ai/providers/minimax#troubleshooting)

Troubleshooting

### [​](https://docs.openclaw.ai/providers/minimax#%E2%80%9Dunknown-model-minimax/minimax-m2-7%E2%80%9D)

”Unknown model: minimax/MiniMax-M2.7”

This usually means the **MiniMax provider isn’t configured** (no provider entry and no MiniMax auth profile/env key found). A fix for this detection is in **2026.1.12**. Fix by:
*   Upgrading to **2026.1.12** (or run from source `main`), then restarting the gateway.
*   Running `openclaw configure` and selecting a **MiniMax** auth option, or
*   Adding the `models.providers.minimax` block manually, or
*   Setting `MINIMAX_API_KEY` (or a MiniMax auth profile) so the provider can be injected.

Make sure the model id is **case‑sensitive**:
*   `minimax/MiniMax-M2.7`
*   `minimax/MiniMax-M2.7-highspeed`

Then recheck with:

```
openclaw models list
```

[LiteLLM](https://docs.openclaw.ai/providers/litellm)[Mistral](https://docs.openclaw.ai/providers/mistral)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
