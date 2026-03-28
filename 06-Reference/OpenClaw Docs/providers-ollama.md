# providers/ollama

Source: https://docs.openclaw.ai/providers/ollama

Title: Ollama - OpenClaw

URL Source: https://docs.openclaw.ai/providers/ollama

Markdown Content:
# Ollama - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/ollama#content-area)

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

Ollama

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

*   [Ollama](https://docs.openclaw.ai/providers/ollama#ollama)
*   [Quick start](https://docs.openclaw.ai/providers/ollama#quick-start)
*   [Onboarding (recommended)](https://docs.openclaw.ai/providers/ollama#onboarding-recommended)
*   [Manual setup](https://docs.openclaw.ai/providers/ollama#manual-setup)
*   [Model discovery (implicit provider)](https://docs.openclaw.ai/providers/ollama#model-discovery-implicit-provider)
*   [Configuration](https://docs.openclaw.ai/providers/ollama#configuration)
*   [Basic setup (implicit discovery)](https://docs.openclaw.ai/providers/ollama#basic-setup-implicit-discovery)
*   [Explicit setup (manual models)](https://docs.openclaw.ai/providers/ollama#explicit-setup-manual-models)
*   [Custom base URL (explicit config)](https://docs.openclaw.ai/providers/ollama#custom-base-url-explicit-config)
*   [Model selection](https://docs.openclaw.ai/providers/ollama#model-selection)
*   [Cloud models](https://docs.openclaw.ai/providers/ollama#cloud-models)
*   [Advanced](https://docs.openclaw.ai/providers/ollama#advanced)
*   [Reasoning models](https://docs.openclaw.ai/providers/ollama#reasoning-models)
*   [Model Costs](https://docs.openclaw.ai/providers/ollama#model-costs)
*   [Streaming Configuration](https://docs.openclaw.ai/providers/ollama#streaming-configuration)
*   [Legacy OpenAI-Compatible Mode](https://docs.openclaw.ai/providers/ollama#legacy-openai-compatible-mode)
*   [Context windows](https://docs.openclaw.ai/providers/ollama#context-windows)
*   [Troubleshooting](https://docs.openclaw.ai/providers/ollama#troubleshooting)
*   [Ollama not detected](https://docs.openclaw.ai/providers/ollama#ollama-not-detected)
*   [No models available](https://docs.openclaw.ai/providers/ollama#no-models-available)
*   [Connection refused](https://docs.openclaw.ai/providers/ollama#connection-refused)
*   [See Also](https://docs.openclaw.ai/providers/ollama#see-also)

Providers

# Ollama

# [​](https://docs.openclaw.ai/providers/ollama#ollama)

Ollama

Ollama is a local LLM runtime that makes it easy to run open-source models on your machine. OpenClaw integrates with Ollama’s native API (`/api/chat`), supports streaming and tool calling, and can auto-discover local Ollama models when you opt in with `OLLAMA_API_KEY` (or an auth profile) and do not define an explicit `models.providers.ollama` entry.

**Remote Ollama users**: Do not use the `/v1` OpenAI-compatible URL (`http://host:11434/v1`) with OpenClaw. This breaks tool calling and models may output raw tool JSON as plain text. Use the native Ollama API URL instead: `baseUrl: "http://host:11434"` (no `/v1`).

## [​](https://docs.openclaw.ai/providers/ollama#quick-start)

Quick start

### [​](https://docs.openclaw.ai/providers/ollama#onboarding-recommended)

Onboarding (recommended)

The fastest way to set up Ollama is through onboarding:

```
openclaw onboard
```

Select **Ollama** from the provider list. Onboarding will:
1.   Ask for the Ollama base URL where your instance can be reached (default `http://127.0.0.1:11434`).
2.   Let you choose **Cloud + Local** (cloud models and local models) or **Local** (local models only).
3.   Open a browser sign-in flow if you choose **Cloud + Local** and are not signed in to ollama.com.
4.   Discover available models and suggest defaults.
5.   Auto-pull the selected model if it is not available locally.

Non-interactive mode is also supported:

```
openclaw onboard --non-interactive \
  --auth-choice ollama \
  --accept-risk
```

Optionally specify a custom base URL or model:

```
openclaw onboard --non-interactive \
  --auth-choice ollama \
  --custom-base-url "http://ollama-host:11434" \
  --custom-model-id "qwen3.5:27b" \
  --accept-risk
```

### [​](https://docs.openclaw.ai/providers/ollama#manual-setup)

Manual setup

1.   Install Ollama: [https://ollama.com/download](https://ollama.com/download)
2.   Pull a local model if you want local inference:

```
ollama pull glm-4.7-flash
# or
ollama pull gpt-oss:20b
# or
ollama pull llama3.3
```

1.   If you want cloud models too, sign in:

```
ollama signin
```

1.   Run onboarding and choose `Ollama`:

```
openclaw onboard
```

*   `Local`: local models only
*   `Cloud + Local`: local models plus cloud models
*   Cloud models such as `kimi-k2.5:cloud`, `minimax-m2.5:cloud`, and `glm-5:cloud` do **not** require a local `ollama pull`

OpenClaw currently suggests:
*   local default: `glm-4.7-flash`
*   cloud defaults: `kimi-k2.5:cloud`, `minimax-m2.5:cloud`, `glm-5:cloud`

1.   If you prefer manual setup, enable Ollama for OpenClaw directly (any value works; Ollama doesn’t require a real key):

```
# Set environment variable
export OLLAMA_API_KEY="ollama-local"

# Or configure in your config file
openclaw config set models.providers.ollama.apiKey "ollama-local"
```

1.   Inspect or switch models:

```
openclaw models list
openclaw models set ollama/glm-4.7-flash
```

1.   Or set the default in config:

```
{
  agents: {
    defaults: {
      model: { primary: "ollama/glm-4.7-flash" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/ollama#model-discovery-implicit-provider)

Model discovery (implicit provider)

When you set `OLLAMA_API_KEY` (or an auth profile) and **do not** define `models.providers.ollama`, OpenClaw discovers models from the local Ollama instance at `http://127.0.0.1:11434`:
*   Queries `/api/tags`
*   Uses best-effort `/api/show` lookups to read `contextWindow` when available
*   Marks `reasoning` with a model-name heuristic (`r1`, `reasoning`, `think`)
*   Sets `maxTokens` to the default Ollama max-token cap used by OpenClaw
*   Sets all costs to `0`

This avoids manual model entries while keeping the catalog aligned with the local Ollama instance.To see what models are available:

```
ollama list
openclaw models list
```

To add a new model, simply pull it with Ollama:

```
ollama pull mistral
```

The new model will be automatically discovered and available to use.If you set `models.providers.ollama` explicitly, auto-discovery is skipped and you must define models manually (see below).
## [​](https://docs.openclaw.ai/providers/ollama#configuration)

Configuration

### [​](https://docs.openclaw.ai/providers/ollama#basic-setup-implicit-discovery)

Basic setup (implicit discovery)

The simplest way to enable Ollama is via environment variable:

```
export OLLAMA_API_KEY="ollama-local"
```

### [​](https://docs.openclaw.ai/providers/ollama#explicit-setup-manual-models)

Explicit setup (manual models)

Use explicit config when:
*   Ollama runs on another host/port.
*   You want to force specific context windows or model lists.
*   You want fully manual model definitions.

```
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://ollama-host:11434",
        apiKey: "ollama-local",
        api: "ollama",
        models: [
          {
            id: "gpt-oss:20b",
            name: "GPT-OSS 20B",
            reasoning: false,
            input: ["text"],
            cost: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
            contextWindow: 8192,
            maxTokens: 8192 * 10
          }
        ]
      }
    }
  }
}
```

If `OLLAMA_API_KEY` is set, you can omit `apiKey` in the provider entry and OpenClaw will fill it for availability checks.
### [​](https://docs.openclaw.ai/providers/ollama#custom-base-url-explicit-config)

Custom base URL (explicit config)

If Ollama is running on a different host or port (explicit config disables auto-discovery, so define models manually):

```
{
  models: {
    providers: {
      ollama: {
        apiKey: "ollama-local",
        baseUrl: "http://ollama-host:11434", // No /v1 - use native Ollama API URL
        api: "ollama", // Set explicitly to guarantee native tool-calling behavior
      },
    },
  },
}
```

Do not add `/v1` to the URL. The `/v1` path uses OpenAI-compatible mode, where tool calling is not reliable. Use the base Ollama URL without a path suffix.

### [​](https://docs.openclaw.ai/providers/ollama#model-selection)

Model selection

Once configured, all your Ollama models are available:

```
{
  agents: {
    defaults: {
      model: {
        primary: "ollama/gpt-oss:20b",
        fallbacks: ["ollama/llama3.3", "ollama/qwen2.5-coder:32b"],
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/ollama#cloud-models)

Cloud models

Cloud models let you run cloud-hosted models (for example `kimi-k2.5:cloud`, `minimax-m2.5:cloud`, `glm-5:cloud`) alongside your local models.To use cloud models, select **Cloud + Local** mode during setup. The wizard checks whether you are signed in and opens a browser sign-in flow when needed. If authentication cannot be verified, the wizard falls back to local model defaults.You can also sign in directly at [ollama.com/signin](https://ollama.com/signin).
## [​](https://docs.openclaw.ai/providers/ollama#advanced)

Advanced

### [​](https://docs.openclaw.ai/providers/ollama#reasoning-models)

Reasoning models

OpenClaw treats models with names such as `deepseek-r1`, `reasoning`, or `think` as reasoning-capable by default:

```
ollama pull deepseek-r1:32b
```

### [​](https://docs.openclaw.ai/providers/ollama#model-costs)

Model Costs

Ollama is free and runs locally, so all model costs are set to $0.
### [​](https://docs.openclaw.ai/providers/ollama#streaming-configuration)

Streaming Configuration

OpenClaw’s Ollama integration uses the **native Ollama API** (`/api/chat`) by default, which fully supports streaming and tool calling simultaneously. No special configuration is needed.
#### [​](https://docs.openclaw.ai/providers/ollama#legacy-openai-compatible-mode)

Legacy OpenAI-Compatible Mode

**Tool calling is not reliable in OpenAI-compatible mode.** Use this mode only if you need OpenAI format for a proxy and do not depend on native tool calling behavior.

If you need to use the OpenAI-compatible endpoint instead (e.g., behind a proxy that only supports OpenAI format), set `api: "openai-completions"` explicitly:

```
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://ollama-host:11434/v1",
        api: "openai-completions",
        injectNumCtxForOpenAICompat: true, // default: true
        apiKey: "ollama-local",
        models: [...]
      }
    }
  }
}
```

This mode may not support streaming + tool calling simultaneously. You may need to disable streaming with `params: { streaming: false }` in model config.When `api: "openai-completions"` is used with Ollama, OpenClaw injects `options.num_ctx` by default so Ollama does not silently fall back to a 4096 context window. If your proxy/upstream rejects unknown `options` fields, disable this behavior:

```
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://ollama-host:11434/v1",
        api: "openai-completions",
        injectNumCtxForOpenAICompat: false,
        apiKey: "ollama-local",
        models: [...]
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/providers/ollama#context-windows)

Context windows

For auto-discovered models, OpenClaw uses the context window reported by Ollama when available, otherwise it falls back to the default Ollama context window used by OpenClaw. You can override `contextWindow` and `maxTokens` in explicit provider config.
## [​](https://docs.openclaw.ai/providers/ollama#troubleshooting)

Troubleshooting

### [​](https://docs.openclaw.ai/providers/ollama#ollama-not-detected)

Ollama not detected

Make sure Ollama is running and that you set `OLLAMA_API_KEY` (or an auth profile), and that you did **not** define an explicit `models.providers.ollama` entry:

```
ollama serve
```

And that the API is accessible:

```
curl http://localhost:11434/api/tags
```

### [​](https://docs.openclaw.ai/providers/ollama#no-models-available)

No models available

If your model is not listed, either:
*   Pull the model locally, or
*   Define the model explicitly in `models.providers.ollama`.

To add models:

```
ollama list  # See what's installed
ollama pull glm-4.7-flash
ollama pull gpt-oss:20b
ollama pull llama3.3     # Or another model
```

### [​](https://docs.openclaw.ai/providers/ollama#connection-refused)

Connection refused

Check that Ollama is running on the correct port:

```
# Check if Ollama is running
ps aux | grep ollama

# Or restart Ollama
ollama serve
```

## [​](https://docs.openclaw.ai/providers/ollama#see-also)

See Also

*   [Model Providers](https://docs.openclaw.ai/concepts/model-providers) - Overview of all providers
*   [Model Selection](https://docs.openclaw.ai/concepts/models) - How to choose models
*   [Configuration](https://docs.openclaw.ai/gateway/configuration) - Full config reference

[NVIDIA](https://docs.openclaw.ai/providers/nvidia)[OpenAI](https://docs.openclaw.ai/providers/openai)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
