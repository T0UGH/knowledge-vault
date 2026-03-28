# providers/sglang

Source: https://docs.openclaw.ai/providers/sglang

Title: SGLang - OpenClaw

URL Source: https://docs.openclaw.ai/providers/sglang

Markdown Content:
# SGLang - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/sglang#content-area)

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

SGLang

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

*   [SGLang](https://docs.openclaw.ai/providers/sglang#sglang)
*   [Quick start](https://docs.openclaw.ai/providers/sglang#quick-start)
*   [Model discovery (implicit provider)](https://docs.openclaw.ai/providers/sglang#model-discovery-implicit-provider)
*   [Explicit configuration (manual models)](https://docs.openclaw.ai/providers/sglang#explicit-configuration-manual-models)
*   [Troubleshooting](https://docs.openclaw.ai/providers/sglang#troubleshooting)

Providers

# SGLang

# [​](https://docs.openclaw.ai/providers/sglang#sglang)

SGLang

SGLang can serve open-source models via an **OpenAI-compatible** HTTP API. OpenClaw can connect to SGLang using the `openai-completions` API.OpenClaw can also **auto-discover** available models from SGLang when you opt in with `SGLANG_API_KEY` (any value works if your server does not enforce auth) and you do not define an explicit `models.providers.sglang` entry.
## [​](https://docs.openclaw.ai/providers/sglang#quick-start)

Quick start

1.   Start SGLang with an OpenAI-compatible server.

Your base URL should expose `/v1` endpoints (for example `/v1/models`, `/v1/chat/completions`). SGLang commonly runs on:
*   `http://127.0.0.1:30000/v1`

1.   Opt in (any value works if no auth is configured):

```
export SGLANG_API_KEY="sglang-local"
```

1.   Run onboarding and choose `SGLang`, or set a model directly:

```
openclaw onboard
```

```
{
  agents: {
    defaults: {
      model: { primary: "sglang/your-model-id" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/sglang#model-discovery-implicit-provider)

Model discovery (implicit provider)

When `SGLANG_API_KEY` is set (or an auth profile exists) and you **do not** define `models.providers.sglang`, OpenClaw will query:
*   `GET http://127.0.0.1:30000/v1/models`

and convert the returned IDs into model entries.If you set `models.providers.sglang` explicitly, auto-discovery is skipped and you must define models manually.
## [​](https://docs.openclaw.ai/providers/sglang#explicit-configuration-manual-models)

Explicit configuration (manual models)

Use explicit config when:
*   SGLang runs on a different host/port.
*   You want to pin `contextWindow`/`maxTokens` values.
*   Your server requires a real API key (or you want to control headers).

```
{
  models: {
    providers: {
      sglang: {
        baseUrl: "http://127.0.0.1:30000/v1",
        apiKey: "${SGLANG_API_KEY}",
        api: "openai-completions",
        models: [
          {
            id: "your-model-id",
            name: "Local SGLang Model",
            reasoning: false,
            input: ["text"],
            cost: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
            contextWindow: 128000,
            maxTokens: 8192,
          },
        ],
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/sglang#troubleshooting)

Troubleshooting

*   Check the server is reachable:

```
curl http://127.0.0.1:30000/v1/models
```

*   If requests fail with auth errors, set a real `SGLANG_API_KEY` that matches your server configuration, or configure the provider explicitly under `models.providers.sglang`.

[Qwen](https://docs.openclaw.ai/providers/qwen)[Synthetic](https://docs.openclaw.ai/providers/synthetic)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
