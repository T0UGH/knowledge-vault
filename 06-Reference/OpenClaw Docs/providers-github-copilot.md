# providers/github-copilot

Source: https://docs.openclaw.ai/providers/github-copilot

Title: GitHub Copilot - OpenClaw

URL Source: https://docs.openclaw.ai/providers/github-copilot

Markdown Content:
# GitHub Copilot - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/github-copilot#content-area)

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

GitHub Copilot

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

*   [GitHub Copilot](https://docs.openclaw.ai/providers/github-copilot#github-copilot)
*   [What is GitHub Copilot?](https://docs.openclaw.ai/providers/github-copilot#what-is-github-copilot)
*   [Two ways to use Copilot in OpenClaw](https://docs.openclaw.ai/providers/github-copilot#two-ways-to-use-copilot-in-openclaw)
*   [1) Built-in GitHub Copilot provider (github-copilot)](https://docs.openclaw.ai/providers/github-copilot#1-built-in-github-copilot-provider-github-copilot)
*   [2) Copilot Proxy plugin (copilot-proxy)](https://docs.openclaw.ai/providers/github-copilot#2-copilot-proxy-plugin-copilot-proxy)
*   [CLI setup](https://docs.openclaw.ai/providers/github-copilot#cli-setup)
*   [Optional flags](https://docs.openclaw.ai/providers/github-copilot#optional-flags)
*   [Set a default model](https://docs.openclaw.ai/providers/github-copilot#set-a-default-model)
*   [Config snippet](https://docs.openclaw.ai/providers/github-copilot#config-snippet)
*   [Notes](https://docs.openclaw.ai/providers/github-copilot#notes)

Providers

# GitHub Copilot

# [​](https://docs.openclaw.ai/providers/github-copilot#github-copilot)

GitHub Copilot

## [​](https://docs.openclaw.ai/providers/github-copilot#what-is-github-copilot)

What is GitHub Copilot?

GitHub Copilot is GitHub’s AI coding assistant. It provides access to Copilot models for your GitHub account and plan. OpenClaw can use Copilot as a model provider in two different ways.
## [​](https://docs.openclaw.ai/providers/github-copilot#two-ways-to-use-copilot-in-openclaw)

Two ways to use Copilot in OpenClaw

### [​](https://docs.openclaw.ai/providers/github-copilot#1-built-in-github-copilot-provider-github-copilot)

1) Built-in GitHub Copilot provider (`github-copilot`)

Use the native device-login flow to obtain a GitHub token, then exchange it for Copilot API tokens when OpenClaw runs. This is the **default** and simplest path because it does not require VS Code.
### [​](https://docs.openclaw.ai/providers/github-copilot#2-copilot-proxy-plugin-copilot-proxy)

2) Copilot Proxy plugin (`copilot-proxy`)

Use the **Copilot Proxy** VS Code extension as a local bridge. OpenClaw talks to the proxy’s `/v1` endpoint and uses the model list you configure there. Choose this when you already run Copilot Proxy in VS Code or need to route through it. You must enable the plugin and keep the VS Code extension running.Use GitHub Copilot as a model provider (`github-copilot`). The login command runs the GitHub device flow, saves an auth profile, and updates your config to use that profile.
## [​](https://docs.openclaw.ai/providers/github-copilot#cli-setup)

CLI setup

```
openclaw models auth login-github-copilot
```

You’ll be prompted to visit a URL and enter a one-time code. Keep the terminal open until it completes.
### [​](https://docs.openclaw.ai/providers/github-copilot#optional-flags)

Optional flags

```
openclaw models auth login-github-copilot --profile-id github-copilot:work
openclaw models auth login-github-copilot --yes
```

## [​](https://docs.openclaw.ai/providers/github-copilot#set-a-default-model)

Set a default model

```
openclaw models set github-copilot/gpt-4o
```

### [​](https://docs.openclaw.ai/providers/github-copilot#config-snippet)

Config snippet

```
{
  agents: { defaults: { model: { primary: "github-copilot/gpt-4o" } } },
}
```

## [​](https://docs.openclaw.ai/providers/github-copilot#notes)

Notes

*   Requires an interactive TTY; run it directly in a terminal.
*   Copilot model availability depends on your plan; if a model is rejected, try another ID (for example `github-copilot/gpt-4.1`).
*   The login stores a GitHub token in the auth profile store and exchanges it for a Copilot API token when OpenClaw runs.

[Deepseek](https://docs.openclaw.ai/providers/deepseek)[GLM Models](https://docs.openclaw.ai/providers/glm)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
