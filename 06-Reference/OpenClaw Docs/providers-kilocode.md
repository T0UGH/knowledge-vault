# providers/kilocode

Source: https://docs.openclaw.ai/providers/kilocode

Title: Kilo Gateway - OpenClaw

URL Source: https://docs.openclaw.ai/providers/kilocode

Markdown Content:
# Kilo Gateway - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/kilocode#content-area)

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

Kilo Gateway

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

*   [Kilo Gateway](https://docs.openclaw.ai/providers/kilocode#kilo-gateway)
*   [Getting an API key](https://docs.openclaw.ai/providers/kilocode#getting-an-api-key)
*   [CLI setup](https://docs.openclaw.ai/providers/kilocode#cli-setup)
*   [Config snippet](https://docs.openclaw.ai/providers/kilocode#config-snippet)
*   [Default model](https://docs.openclaw.ai/providers/kilocode#default-model)
*   [Available models](https://docs.openclaw.ai/providers/kilocode#available-models)
*   [Notes](https://docs.openclaw.ai/providers/kilocode#notes)

Providers

# Kilo Gateway

# [​](https://docs.openclaw.ai/providers/kilocode#kilo-gateway)

Kilo Gateway

Kilo Gateway provides a **unified API** that routes requests to many models behind a single endpoint and API key. It is OpenAI-compatible, so most OpenAI SDKs work by switching the base URL.
## [​](https://docs.openclaw.ai/providers/kilocode#getting-an-api-key)

Getting an API key

1.   Go to [app.kilo.ai](https://app.kilo.ai/)
2.   Sign in or create an account
3.   Navigate to API Keys and generate a new key

## [​](https://docs.openclaw.ai/providers/kilocode#cli-setup)

CLI setup

```
openclaw onboard --kilocode-api-key <key>
```

Or set the environment variable:

```
export KILOCODE_API_KEY="<your-kilocode-api-key>" # pragma: allowlist secret
```

## [​](https://docs.openclaw.ai/providers/kilocode#config-snippet)

Config snippet

```
{
  env: { KILOCODE_API_KEY: "<your-kilocode-api-key>" }, // pragma: allowlist secret
  agents: {
    defaults: {
      model: { primary: "kilocode/kilo/auto" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/kilocode#default-model)

Default model

The default model is `kilocode/kilo/auto`, a smart routing model that automatically selects the best underlying model based on the task:
*   Planning, debugging, and orchestration tasks route to Claude Opus
*   Code writing and exploration tasks route to Claude Sonnet

## [​](https://docs.openclaw.ai/providers/kilocode#available-models)

Available models

OpenClaw dynamically discovers available models from the Kilo Gateway at startup. Use `/models kilocode` to see the full list of models available with your account.Any model available on the gateway can be used with the `kilocode/` prefix:

```
kilocode/kilo/auto              (default - smart routing)
kilocode/anthropic/claude-sonnet-4
kilocode/openai/gpt-5.2
kilocode/google/gemini-3-pro-preview
...and many more
```

## [​](https://docs.openclaw.ai/providers/kilocode#notes)

Notes

*   Model refs are `kilocode/<model-id>` (e.g., `kilocode/anthropic/claude-sonnet-4`).
*   Default model: `kilocode/kilo/auto`
*   Base URL: `https://api.kilo.ai/api/gateway/`
*   For more model/provider options, see [/concepts/model-providers](https://docs.openclaw.ai/concepts/model-providers).
*   Kilo Gateway uses a Bearer token with your API key under the hood.

[Hugging Face (Inference)](https://docs.openclaw.ai/providers/huggingface)[LiteLLM](https://docs.openclaw.ai/providers/litellm)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
