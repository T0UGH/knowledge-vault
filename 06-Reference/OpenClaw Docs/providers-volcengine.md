# providers/volcengine

Source: https://docs.openclaw.ai/providers/volcengine

Title: Volcengine (Doubao) - OpenClaw

URL Source: https://docs.openclaw.ai/providers/volcengine

Markdown Content:
# Volcengine (Doubao) - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/volcengine#content-area)

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

Volcengine (Doubao)

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

*   [Volcengine (Doubao)](https://docs.openclaw.ai/providers/volcengine#volcengine-doubao)
*   [Quick start](https://docs.openclaw.ai/providers/volcengine#quick-start)
*   [Non-interactive example](https://docs.openclaw.ai/providers/volcengine#non-interactive-example)
*   [Providers and endpoints](https://docs.openclaw.ai/providers/volcengine#providers-and-endpoints)
*   [Available models](https://docs.openclaw.ai/providers/volcengine#available-models)
*   [Environment note](https://docs.openclaw.ai/providers/volcengine#environment-note)

Providers

# Volcengine (Doubao)

# [​](https://docs.openclaw.ai/providers/volcengine#volcengine-doubao)

Volcengine (Doubao)

The Volcengine provider gives access to Doubao models and third-party models hosted on Volcano Engine, with separate endpoints for general and coding workloads.
*   Providers: `volcengine` (general) + `volcengine-plan` (coding)
*   Auth: `VOLCANO_ENGINE_API_KEY`
*   API: OpenAI-compatible

## [​](https://docs.openclaw.ai/providers/volcengine#quick-start)

Quick start

1.   Set the API key:

```
openclaw onboard --auth-choice volcengine-api-key
```

1.   Set a default model:

```
{
  agents: {
    defaults: {
      model: { primary: "volcengine-plan/ark-code-latest" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/volcengine#non-interactive-example)

Non-interactive example

```
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice volcengine-api-key \
  --volcengine-api-key "$VOLCANO_ENGINE_API_KEY"
```

## [​](https://docs.openclaw.ai/providers/volcengine#providers-and-endpoints)

Providers and endpoints

| Provider | Endpoint | Use case |
| --- | --- | --- |
| `volcengine` | `ark.cn-beijing.volces.com/api/v3` | General models |
| `volcengine-plan` | `ark.cn-beijing.volces.com/api/coding/v3` | Coding models |

Both providers are configured from a single API key. Setup registers both automatically.
## [​](https://docs.openclaw.ai/providers/volcengine#available-models)

Available models

*   **doubao-seed-1-8** - Doubao Seed 1.8 (general, default)
*   **doubao-seed-code-preview** - Doubao coding model
*   **ark-code-latest** - Coding plan default
*   **Kimi K2.5** - Moonshot AI via Volcano Engine
*   **GLM-4.7** - GLM via Volcano Engine
*   **DeepSeek V3.2** - DeepSeek via Volcano Engine

Most models support text + image input. Context windows range from 128K to 256K tokens.
## [​](https://docs.openclaw.ai/providers/volcengine#environment-note)

Environment note

If the Gateway runs as a daemon (launchd/systemd), make sure `VOLCANO_ENGINE_API_KEY` is available to that process (for example, in `~/.openclaw/.env` or via `env.shellEnv`).

[vLLM](https://docs.openclaw.ai/providers/vllm)[xAI](https://docs.openclaw.ai/providers/xai)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
