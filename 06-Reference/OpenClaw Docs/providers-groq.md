# providers/groq

Source: https://docs.openclaw.ai/providers/groq

Title: Groq - OpenClaw

URL Source: https://docs.openclaw.ai/providers/groq

Markdown Content:
# Groq - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/groq#content-area)

[OpenClaw home page![Image 1: dark logo](https://mintcdn.com/clawdhub/dpADRo8IUoiDztzJ/assets/pixel-lobster.svg?fit=max&auto=format&n=dpADRo8IUoiDztzJ&q=85&s=8fdf719fb6d3eaad7c65231385bf28e5)![Image 2: dark logo](https://mintcdn.com/clawdhub/dpADRo8IUoiDztzJ/assets/pixel-lobster.svg?fit=max&auto=format&n=dpADRo8IUoiDztzJ&q=85&s=8fdf719fb6d3eaad7c65231385bf28e5)](https://docs.openclaw.ai/)

![Image 3: US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

Ctrl K

*   [GitHub](https://github.com/openclaw/openclaw)
*   [Releases](https://github.com/openclaw/openclaw/releases)
*   [Discord](https://discord.com/invite/clawd)

Search...

Navigation

Providers

Groq

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

*   [Groq](https://docs.openclaw.ai/providers/groq#groq)
*   [Quick start](https://docs.openclaw.ai/providers/groq#quick-start)
*   [Config file example](https://docs.openclaw.ai/providers/groq#config-file-example)
*   [Audio transcription](https://docs.openclaw.ai/providers/groq#audio-transcription)
*   [Environment note](https://docs.openclaw.ai/providers/groq#environment-note)
*   [Available models](https://docs.openclaw.ai/providers/groq#available-models)
*   [Links](https://docs.openclaw.ai/providers/groq#links)

Providers

# Groq

# [​](https://docs.openclaw.ai/providers/groq#groq)

Groq

[Groq](https://groq.com/) provides ultra-fast inference on open-source models (Llama, Gemma, Mistral, and more) using custom LPU hardware. OpenClaw connects to Groq through its OpenAI-compatible API.
*   Provider: `groq`
*   Auth: `GROQ_API_KEY`
*   API: OpenAI-compatible

## [​](https://docs.openclaw.ai/providers/groq#quick-start)

Quick start

1.   Get an API key from [console.groq.com/keys](https://console.groq.com/keys).
2.   Set the API key:

```
export GROQ_API_KEY="gsk_..."
```

1.   Set a default model:

```
{
  agents: {
    defaults: {
      model: { primary: "groq/llama-3.3-70b-versatile" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/groq#config-file-example)

Config file example

```
{
  env: { GROQ_API_KEY: "gsk_..." },
  agents: {
    defaults: {
      model: { primary: "groq/llama-3.3-70b-versatile" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/groq#audio-transcription)

Audio transcription

Groq also provides fast Whisper-based audio transcription. When configured as a media-understanding provider, OpenClaw uses Groq’s `whisper-large-v3-turbo` model to transcribe voice messages.

```
{
  media: {
    understanding: {
      audio: {
        models: [{ provider: "groq" }],
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/groq#environment-note)

Environment note

If the Gateway runs as a daemon (launchd/systemd), make sure `GROQ_API_KEY` is available to that process (for example, in `~/.openclaw/.env` or via `env.shellEnv`).
## [​](https://docs.openclaw.ai/providers/groq#available-models)

Available models

Groq’s model catalog changes frequently. Run `openclaw models list | grep groq` to see currently available models, or check [console.groq.com/docs/models](https://console.groq.com/docs/models).Popular choices include:
*   **Llama 3.3 70B Versatile** - general-purpose, large context
*   **Llama 3.1 8B Instant** - fast, lightweight
*   **Gemma 2 9B** - compact, efficient
*   **Mixtral 8x7B** - MoE architecture, strong reasoning

## [​](https://docs.openclaw.ai/providers/groq#links)

Links

*   [Groq Console](https://console.groq.com/)
*   [API Documentation](https://console.groq.com/docs)
*   [Model List](https://console.groq.com/docs/models)
*   [Pricing](https://groq.com/pricing)

[Google (Gemini)](https://docs.openclaw.ai/providers/google)[Hugging Face (Inference)](https://docs.openclaw.ai/providers/huggingface)

Ctrl+I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
