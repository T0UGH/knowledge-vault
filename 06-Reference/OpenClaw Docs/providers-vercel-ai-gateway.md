# providers/vercel-ai-gateway

Source: https://docs.openclaw.ai/providers/vercel-ai-gateway

Title: Vercel AI Gateway - OpenClaw

URL Source: https://docs.openclaw.ai/providers/vercel-ai-gateway

Markdown Content:
# Vercel AI Gateway - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/vercel-ai-gateway#content-area)

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

Vercel AI Gateway

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

*   [Vercel AI Gateway](https://docs.openclaw.ai/providers/vercel-ai-gateway#vercel-ai-gateway)
*   [Quick start](https://docs.openclaw.ai/providers/vercel-ai-gateway#quick-start)
*   [Non-interactive example](https://docs.openclaw.ai/providers/vercel-ai-gateway#non-interactive-example)
*   [Environment note](https://docs.openclaw.ai/providers/vercel-ai-gateway#environment-note)
*   [Model ID shorthand](https://docs.openclaw.ai/providers/vercel-ai-gateway#model-id-shorthand)

Providers

# Vercel AI Gateway

# [​](https://docs.openclaw.ai/providers/vercel-ai-gateway#vercel-ai-gateway)

Vercel AI Gateway

The [Vercel AI Gateway](https://vercel.com/ai-gateway) provides a unified API to access hundreds of models through a single endpoint.
*   Provider: `vercel-ai-gateway`
*   Auth: `AI_GATEWAY_API_KEY`
*   API: Anthropic Messages compatible
*   OpenClaw auto-discovers the Gateway `/v1/models` catalog, so `/models vercel-ai-gateway` includes current model refs such as `vercel-ai-gateway/openai/gpt-5.4`.

## [​](https://docs.openclaw.ai/providers/vercel-ai-gateway#quick-start)

Quick start

1.   Set the API key (recommended: store it for the Gateway):

```
openclaw onboard --auth-choice ai-gateway-api-key
```

1.   Set a default model:

```
{
  agents: {
    defaults: {
      model: { primary: "vercel-ai-gateway/anthropic/claude-opus-4.6" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/vercel-ai-gateway#non-interactive-example)

Non-interactive example

```
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice ai-gateway-api-key \
  --ai-gateway-api-key "$AI_GATEWAY_API_KEY"
```

## [​](https://docs.openclaw.ai/providers/vercel-ai-gateway#environment-note)

Environment note

If the Gateway runs as a daemon (launchd/systemd), make sure `AI_GATEWAY_API_KEY` is available to that process (for example, in `~/.openclaw/.env` or via `env.shellEnv`).
## [​](https://docs.openclaw.ai/providers/vercel-ai-gateway#model-id-shorthand)

Model ID shorthand

OpenClaw accepts Vercel Claude shorthand model refs and normalizes them at runtime:
*   `vercel-ai-gateway/claude-opus-4.6` ->`vercel-ai-gateway/anthropic/claude-opus-4.6`
*   `vercel-ai-gateway/opus-4.6` ->`vercel-ai-gateway/anthropic/claude-opus-4-6`

[Venice AI](https://docs.openclaw.ai/providers/venice)[vLLM](https://docs.openclaw.ai/providers/vllm)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
