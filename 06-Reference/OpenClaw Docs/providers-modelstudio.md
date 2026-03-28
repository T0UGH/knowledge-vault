# providers/modelstudio

Source: https://docs.openclaw.ai/providers/modelstudio

Title: Qwen / Model Studio - OpenClaw

URL Source: https://docs.openclaw.ai/providers/modelstudio

Markdown Content:
# Qwen / Model Studio - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/modelstudio#content-area)

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

Qwen / Model Studio

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

*   [Qwen / Model Studio (Alibaba Cloud)](https://docs.openclaw.ai/providers/modelstudio#qwen-%2F-model-studio-alibaba-cloud)
*   [Quick start](https://docs.openclaw.ai/providers/modelstudio#quick-start)
*   [Standard (pay-as-you-go)](https://docs.openclaw.ai/providers/modelstudio#standard-pay-as-you-go)
*   [Coding Plan (subscription)](https://docs.openclaw.ai/providers/modelstudio#coding-plan-subscription)
*   [Plan types and endpoints](https://docs.openclaw.ai/providers/modelstudio#plan-types-and-endpoints)
*   [Get your API key](https://docs.openclaw.ai/providers/modelstudio#get-your-api-key)
*   [Available models](https://docs.openclaw.ai/providers/modelstudio#available-models)
*   [Environment note](https://docs.openclaw.ai/providers/modelstudio#environment-note)

Providers

# Qwen / Model Studio

# [​](https://docs.openclaw.ai/providers/modelstudio#qwen-/-model-studio-alibaba-cloud)

Qwen / Model Studio (Alibaba Cloud)

The Model Studio provider gives access to Alibaba Cloud models including Qwen and third-party models hosted on the platform. Two billing plans are supported: **Standard** (pay-as-you-go) and **Coding Plan** (subscription).
*   Provider: `modelstudio`
*   Auth: `MODELSTUDIO_API_KEY`
*   API: OpenAI-compatible

## [​](https://docs.openclaw.ai/providers/modelstudio#quick-start)

Quick start

### [​](https://docs.openclaw.ai/providers/modelstudio#standard-pay-as-you-go)

Standard (pay-as-you-go)

```
# China endpoint
openclaw onboard --auth-choice modelstudio-standard-api-key-cn

# Global/Intl endpoint
openclaw onboard --auth-choice modelstudio-standard-api-key
```

### [​](https://docs.openclaw.ai/providers/modelstudio#coding-plan-subscription)

Coding Plan (subscription)

```
# China endpoint
openclaw onboard --auth-choice modelstudio-api-key-cn

# Global/Intl endpoint
openclaw onboard --auth-choice modelstudio-api-key
```

After onboarding, set a default model:

```
{
  agents: {
    defaults: {
      model: { primary: "modelstudio/qwen3.5-plus" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/modelstudio#plan-types-and-endpoints)

Plan types and endpoints

| Plan | Region | Auth choice | Endpoint |
| --- | --- | --- | --- |
| Standard (pay-as-you-go) | China | `modelstudio-standard-api-key-cn` | `dashscope.aliyuncs.com/compatible-mode/v1` |
| Standard (pay-as-you-go) | Global | `modelstudio-standard-api-key` | `dashscope-intl.aliyuncs.com/compatible-mode/v1` |
| Coding Plan (subscription) | China | `modelstudio-api-key-cn` | `coding.dashscope.aliyuncs.com/v1` |
| Coding Plan (subscription) | Global | `modelstudio-api-key` | `coding-intl.dashscope.aliyuncs.com/v1` |

The provider auto-selects the endpoint based on your auth choice. You can override with a custom `baseUrl` in config.
## [​](https://docs.openclaw.ai/providers/modelstudio#get-your-api-key)

Get your API key

*   **China**: [bailian.console.aliyun.com](https://bailian.console.aliyun.com/)
*   **Global/Intl**: [modelstudio.console.alibabacloud.com](https://modelstudio.console.alibabacloud.com/)

## [​](https://docs.openclaw.ai/providers/modelstudio#available-models)

Available models

*   **qwen3.5-plus** (default) — Qwen 3.5 Plus
*   **qwen3-coder-plus**, **qwen3-coder-next** — Qwen coding models
*   **GLM-5** — GLM models via Alibaba
*   **Kimi K2.5** — Moonshot AI via Alibaba
*   **MiniMax-M2.5** — MiniMax via Alibaba

Some models (qwen3.5-plus, kimi-k2.5) support image input. Context windows range from 200K to 1M tokens.
## [​](https://docs.openclaw.ai/providers/modelstudio#environment-note)

Environment note

If the Gateway runs as a daemon (launchd/systemd), make sure `MODELSTUDIO_API_KEY` is available to that process (for example, in `~/.openclaw/.env` or via `env.shellEnv`).

[Qianfan](https://docs.openclaw.ai/providers/qianfan)[Qwen](https://docs.openclaw.ai/providers/qwen)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
