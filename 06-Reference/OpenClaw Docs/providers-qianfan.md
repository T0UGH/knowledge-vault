# providers/qianfan

Source: https://docs.openclaw.ai/providers/qianfan

Title: Qianfan - OpenClaw

URL Source: https://docs.openclaw.ai/providers/qianfan

Markdown Content:
# Qianfan - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/qianfan#content-area)

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

Qianfan

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

*   [Qianfan Provider Guide](https://docs.openclaw.ai/providers/qianfan#qianfan-provider-guide)
*   [Prerequisites](https://docs.openclaw.ai/providers/qianfan#prerequisites)
*   [Getting Your API Key](https://docs.openclaw.ai/providers/qianfan#getting-your-api-key)
*   [CLI setup](https://docs.openclaw.ai/providers/qianfan#cli-setup)
*   [Related Documentation](https://docs.openclaw.ai/providers/qianfan#related-documentation)

Providers

# Qianfan

# [​](https://docs.openclaw.ai/providers/qianfan#qianfan-provider-guide)

Qianfan Provider Guide

Qianfan is Baidu’s MaaS platform, provides a **unified API** that routes requests to many models behind a single endpoint and API key. It is OpenAI-compatible, so most OpenAI SDKs work by switching the base URL.
## [​](https://docs.openclaw.ai/providers/qianfan#prerequisites)

Prerequisites

1.   A Baidu Cloud account with Qianfan API access
2.   An API key from the Qianfan console
3.   OpenClaw installed on your system

## [​](https://docs.openclaw.ai/providers/qianfan#getting-your-api-key)

Getting Your API Key

1.   Visit the [Qianfan Console](https://console.bce.baidu.com/qianfan/ais/console/apiKey)
2.   Create a new application or select an existing one
3.   Generate an API key (format: `bce-v3/ALTAK-...`)
4.   Copy the API key for use with OpenClaw

## [​](https://docs.openclaw.ai/providers/qianfan#cli-setup)

CLI setup

```
openclaw onboard --auth-choice qianfan-api-key
```

## [​](https://docs.openclaw.ai/providers/qianfan#related-documentation)

Related Documentation

*   [OpenClaw Configuration](https://docs.openclaw.ai/gateway/configuration)
*   [Model Providers](https://docs.openclaw.ai/concepts/model-providers)
*   [Agent Setup](https://docs.openclaw.ai/concepts/agent)
*   [Qianfan API Documentation](https://cloud.baidu.com/doc/qianfan-api/s/3m7of64lb)

[Perplexity (Provider)](https://docs.openclaw.ai/providers/perplexity-provider)[Qwen / Model Studio](https://docs.openclaw.ai/providers/qwen_modelstudio)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
