# providers/perplexity-provider

Source: https://docs.openclaw.ai/providers/perplexity-provider

Title: Perplexity (Provider) - OpenClaw

URL Source: https://docs.openclaw.ai/providers/perplexity-provider

Markdown Content:
# Perplexity (Provider) - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/perplexity-provider#content-area)

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

Perplexity (Provider)

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

*   [Perplexity (Web Search Provider)](https://docs.openclaw.ai/providers/perplexity-provider#perplexity-web-search-provider)
*   [Quick start](https://docs.openclaw.ai/providers/perplexity-provider#quick-start)
*   [Search modes](https://docs.openclaw.ai/providers/perplexity-provider#search-modes)
*   [Native API filtering](https://docs.openclaw.ai/providers/perplexity-provider#native-api-filtering)
*   [Environment note](https://docs.openclaw.ai/providers/perplexity-provider#environment-note)

Providers

# Perplexity (Provider)

# [​](https://docs.openclaw.ai/providers/perplexity-provider#perplexity-web-search-provider)

Perplexity (Web Search Provider)

The Perplexity plugin provides web search capabilities through the Perplexity Search API or Perplexity Sonar via OpenRouter.

This page covers the Perplexity **provider** setup. For the Perplexity **tool** (how the agent uses it), see [Perplexity tool](https://docs.openclaw.ai/tools/perplexity-search).

*   Type: web search provider (not a model provider)
*   Auth: `PERPLEXITY_API_KEY` (direct) or `OPENROUTER_API_KEY` (via OpenRouter)
*   Config path: `plugins.entries.perplexity.config.webSearch.apiKey`

## [​](https://docs.openclaw.ai/providers/perplexity-provider#quick-start)

Quick start

1.   Set the API key:

```
openclaw configure --section web
```

Or set it directly:

```
openclaw config set plugins.entries.perplexity.config.webSearch.apiKey "pplx-xxxxxxxxxxxx"
```

1.   The agent will automatically use Perplexity for web searches when configured.

## [​](https://docs.openclaw.ai/providers/perplexity-provider#search-modes)

Search modes

The plugin auto-selects the transport based on API key prefix:

| Key prefix | Transport | Features |
| --- | --- | --- |
| `pplx-` | Native Perplexity Search API | Structured results, domain/language/date filters |
| `sk-or-` | OpenRouter (Sonar) | AI-synthesized answers with citations |

## [​](https://docs.openclaw.ai/providers/perplexity-provider#native-api-filtering)

Native API filtering

When using the native Perplexity API (`pplx-` key), searches support:
*   **Country**: 2-letter country code
*   **Language**: ISO 639-1 language code
*   **Date range**: day, week, month, year
*   **Domain filters**: allowlist/denylist (max 20 domains)
*   **Content budget**: `max_tokens`, `max_tokens_per_page`

## [​](https://docs.openclaw.ai/providers/perplexity-provider#environment-note)

Environment note

If the Gateway runs as a daemon (launchd/systemd), make sure `PERPLEXITY_API_KEY` is available to that process (for example, in `~/.openclaw/.env` or via `env.shellEnv`).

[OpenRouter](https://docs.openclaw.ai/providers/openrouter)[Qianfan](https://docs.openclaw.ai/providers/qianfan)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
