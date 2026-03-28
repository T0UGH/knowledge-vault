# providers/opencode

Source: https://docs.openclaw.ai/providers/opencode

Title: OpenCode - OpenClaw

URL Source: https://docs.openclaw.ai/providers/opencode

Markdown Content:
# OpenCode - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/opencode#content-area)

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

OpenCode

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

*   [OpenCode](https://docs.openclaw.ai/providers/opencode#opencode)
*   [CLI setup](https://docs.openclaw.ai/providers/opencode#cli-setup)
*   [Zen catalog](https://docs.openclaw.ai/providers/opencode#zen-catalog)
*   [Go catalog](https://docs.openclaw.ai/providers/opencode#go-catalog)
*   [Config snippet](https://docs.openclaw.ai/providers/opencode#config-snippet)
*   [Catalogs](https://docs.openclaw.ai/providers/opencode#catalogs)
*   [Zen](https://docs.openclaw.ai/providers/opencode#zen)
*   [Go](https://docs.openclaw.ai/providers/opencode#go)
*   [Notes](https://docs.openclaw.ai/providers/opencode#notes)

Providers

# OpenCode

# [​](https://docs.openclaw.ai/providers/opencode#opencode)

OpenCode

OpenCode exposes two hosted catalogs in OpenClaw:
*   `opencode/...` for the **Zen** catalog
*   `opencode-go/...` for the **Go** catalog

Both catalogs use the same OpenCode API key. OpenClaw keeps the runtime provider ids split so upstream per-model routing stays correct, but onboarding and docs treat them as one OpenCode setup.
## [​](https://docs.openclaw.ai/providers/opencode#cli-setup)

CLI setup

### [​](https://docs.openclaw.ai/providers/opencode#zen-catalog)

Zen catalog

```
openclaw onboard --auth-choice opencode-zen
openclaw onboard --opencode-zen-api-key "$OPENCODE_API_KEY"
```

### [​](https://docs.openclaw.ai/providers/opencode#go-catalog)

Go catalog

```
openclaw onboard --auth-choice opencode-go
openclaw onboard --opencode-go-api-key "$OPENCODE_API_KEY"
```

## [​](https://docs.openclaw.ai/providers/opencode#config-snippet)

Config snippet

```
{
  env: { OPENCODE_API_KEY: "sk-..." },
  agents: { defaults: { model: { primary: "opencode/claude-opus-4-6" } } },
}
```

## [​](https://docs.openclaw.ai/providers/opencode#catalogs)

Catalogs

### [​](https://docs.openclaw.ai/providers/opencode#zen)

Zen

*   Runtime provider: `opencode`
*   Example models: `opencode/claude-opus-4-6`, `opencode/gpt-5.2`, `opencode/gemini-3-pro`
*   Best when you want the curated OpenCode multi-model proxy

### [​](https://docs.openclaw.ai/providers/opencode#go)

Go

*   Runtime provider: `opencode-go`
*   Example models: `opencode-go/kimi-k2.5`, `opencode-go/glm-5`, `opencode-go/minimax-m2.5`
*   Best when you want the OpenCode-hosted Kimi/GLM/MiniMax lineup

## [​](https://docs.openclaw.ai/providers/opencode#notes)

Notes

*   `OPENCODE_ZEN_API_KEY` is also supported.
*   Entering one OpenCode key during setup stores credentials for both runtime providers.
*   You sign in to OpenCode, add billing details, and copy your API key.
*   Billing and catalog availability are managed from the OpenCode dashboard.

[OpenAI](https://docs.openclaw.ai/providers/openai)[OpenCode Go](https://docs.openclaw.ai/providers/opencode-go)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
