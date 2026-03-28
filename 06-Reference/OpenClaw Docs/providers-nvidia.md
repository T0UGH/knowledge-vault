# providers/nvidia

Source: https://docs.openclaw.ai/providers/nvidia

Title: NVIDIA - OpenClaw

URL Source: https://docs.openclaw.ai/providers/nvidia

Markdown Content:
# NVIDIA - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/nvidia#content-area)

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

NVIDIA

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

*   [NVIDIA](https://docs.openclaw.ai/providers/nvidia#nvidia)
*   [CLI setup](https://docs.openclaw.ai/providers/nvidia#cli-setup)
*   [Config snippet](https://docs.openclaw.ai/providers/nvidia#config-snippet)
*   [Model IDs](https://docs.openclaw.ai/providers/nvidia#model-ids)
*   [Notes](https://docs.openclaw.ai/providers/nvidia#notes)

Providers

# NVIDIA

# [​](https://docs.openclaw.ai/providers/nvidia#nvidia)

NVIDIA

NVIDIA provides an OpenAI-compatible API at `https://integrate.api.nvidia.com/v1` for Nemotron and NeMo models. Authenticate with an API key from [NVIDIA NGC](https://catalog.ngc.nvidia.com/).
## [​](https://docs.openclaw.ai/providers/nvidia#cli-setup)

CLI setup

Export the key once, then run onboarding and set an NVIDIA model:

```
export NVIDIA_API_KEY="nvapi-..."
openclaw onboard --auth-choice skip
openclaw models set nvidia/nvidia/llama-3.1-nemotron-70b-instruct
```

If you still pass `--token`, remember it lands in shell history and `ps` output; prefer the env var when possible.
## [​](https://docs.openclaw.ai/providers/nvidia#config-snippet)

Config snippet

```
{
  env: { NVIDIA_API_KEY: "nvapi-..." },
  models: {
    providers: {
      nvidia: {
        baseUrl: "https://integrate.api.nvidia.com/v1",
        api: "openai-completions",
      },
    },
  },
  agents: {
    defaults: {
      model: { primary: "nvidia/nvidia/llama-3.1-nemotron-70b-instruct" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/nvidia#model-ids)

Model IDs

*   `nvidia/llama-3.1-nemotron-70b-instruct` (default)
*   `meta/llama-3.3-70b-instruct`
*   `nvidia/mistral-nemo-minitron-8b-8k-instruct`

## [​](https://docs.openclaw.ai/providers/nvidia#notes)

Notes

*   OpenAI-compatible `/v1` endpoint; use an API key from NVIDIA NGC.
*   Provider auto-enables when `NVIDIA_API_KEY` is set; uses static defaults (131,072-token context window, 4,096 max tokens).

[Moonshot AI](https://docs.openclaw.ai/providers/moonshot)[Ollama](https://docs.openclaw.ai/providers/ollama)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
