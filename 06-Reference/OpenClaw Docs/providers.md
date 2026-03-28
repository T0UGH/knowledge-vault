# providers

Source: https://docs.openclaw.ai/providers

Title: Provider Directory - OpenClaw

URL Source: https://docs.openclaw.ai/providers

Markdown Content:
# Provider Directory - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers#content-area)

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

Overview

Provider Directory

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

*   [Model Providers](https://docs.openclaw.ai/providers#model-providers)
*   [Quick start](https://docs.openclaw.ai/providers#quick-start)
*   [Provider docs](https://docs.openclaw.ai/providers#provider-docs)
*   [Transcription providers](https://docs.openclaw.ai/providers#transcription-providers)
*   [Community tools](https://docs.openclaw.ai/providers#community-tools)

Overview

# Provider Directory

# [​](https://docs.openclaw.ai/providers#model-providers)

Model Providers

OpenClaw can use many LLM providers. Pick a provider, authenticate, then set the default model as `provider/model`.Looking for chat channel docs (WhatsApp/Telegram/Discord/Slack/Mattermost (plugin)/etc.)? See [Channels](https://docs.openclaw.ai/channels).
## [​](https://docs.openclaw.ai/providers#quick-start)

Quick start

1.   Authenticate with the provider (usually via `openclaw onboard`).
2.   Set the default model:

```
{
  agents: { defaults: { model: { primary: "anthropic/claude-opus-4-6" } } },
}
```

## [​](https://docs.openclaw.ai/providers#provider-docs)

Provider docs

*   [Amazon Bedrock](https://docs.openclaw.ai/providers/bedrock)
*   [Anthropic (API + Claude Code CLI)](https://docs.openclaw.ai/providers/anthropic)
*   [Cloudflare AI Gateway](https://docs.openclaw.ai/providers/cloudflare-ai-gateway)
*   [DeepSeek](https://docs.openclaw.ai/providers/deepseek)
*   [GitHub Copilot](https://docs.openclaw.ai/providers/github-copilot)
*   [GLM models](https://docs.openclaw.ai/providers/glm)
*   [Google (Gemini)](https://docs.openclaw.ai/providers/google)
*   [Groq (LPU inference)](https://docs.openclaw.ai/providers/groq)
*   [Hugging Face (Inference)](https://docs.openclaw.ai/providers/huggingface)
*   [Kilocode](https://docs.openclaw.ai/providers/kilocode)
*   [LiteLLM (unified gateway)](https://docs.openclaw.ai/providers/litellm)
*   [MiniMax](https://docs.openclaw.ai/providers/minimax)
*   [Mistral](https://docs.openclaw.ai/providers/mistral)
*   [Moonshot AI (Kimi + Kimi Coding)](https://docs.openclaw.ai/providers/moonshot)
*   [NVIDIA](https://docs.openclaw.ai/providers/nvidia)
*   [Ollama (cloud + local models)](https://docs.openclaw.ai/providers/ollama)
*   [OpenAI (API + Codex)](https://docs.openclaw.ai/providers/openai)
*   [OpenCode](https://docs.openclaw.ai/providers/opencode)
*   [OpenCode Go](https://docs.openclaw.ai/providers/opencode-go)
*   [OpenRouter](https://docs.openclaw.ai/providers/openrouter)
*   [Perplexity (web search)](https://docs.openclaw.ai/providers/perplexity-provider)
*   [Qianfan](https://docs.openclaw.ai/providers/qianfan)
*   [Qwen / Model Studio (Alibaba Cloud)](https://docs.openclaw.ai/providers/qwen_modelstudio)
*   [SGLang (local models)](https://docs.openclaw.ai/providers/sglang)
*   [Synthetic](https://docs.openclaw.ai/providers/synthetic)
*   [Together AI](https://docs.openclaw.ai/providers/together)
*   [Venice (Venice AI, privacy-focused)](https://docs.openclaw.ai/providers/venice)
*   [Vercel AI Gateway](https://docs.openclaw.ai/providers/vercel-ai-gateway)
*   [vLLM (local models)](https://docs.openclaw.ai/providers/vllm)
*   [Volcengine (Doubao)](https://docs.openclaw.ai/providers/volcengine)
*   [xAI](https://docs.openclaw.ai/providers/xai)
*   [Xiaomi](https://docs.openclaw.ai/providers/xiaomi)
*   [Z.AI](https://docs.openclaw.ai/providers/zai)

## [​](https://docs.openclaw.ai/providers#transcription-providers)

Transcription providers

*   [Deepgram (audio transcription)](https://docs.openclaw.ai/providers/deepgram)

## [​](https://docs.openclaw.ai/providers#community-tools)

Community tools

*   [Claude Max API Proxy](https://docs.openclaw.ai/providers/claude-max-api-proxy) - Community proxy for Claude subscription credentials (verify Anthropic policy/terms before use)

For the full provider catalog (xAI, Groq, Mistral, etc.) and advanced configuration, see [Model providers](https://docs.openclaw.ai/concepts/model-providers).

[Model Provider Quickstart](https://docs.openclaw.ai/providers/models)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
