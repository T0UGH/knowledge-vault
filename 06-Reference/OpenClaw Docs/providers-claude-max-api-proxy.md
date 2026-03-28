# providers/claude-max-api-proxy

Source: https://docs.openclaw.ai/providers/claude-max-api-proxy

Title: Claude Max API Proxy - OpenClaw

URL Source: https://docs.openclaw.ai/providers/claude-max-api-proxy

Markdown Content:
# Claude Max API Proxy - OpenClaw

[Skip to main content](https://docs.openclaw.ai/providers/claude-max-api-proxy#content-area)

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

Claude Max API Proxy

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

*   [Claude Max API Proxy](https://docs.openclaw.ai/providers/claude-max-api-proxy#claude-max-api-proxy)
*   [Why Use This?](https://docs.openclaw.ai/providers/claude-max-api-proxy#why-use-this)
*   [How It Works](https://docs.openclaw.ai/providers/claude-max-api-proxy#how-it-works)
*   [Installation](https://docs.openclaw.ai/providers/claude-max-api-proxy#installation)
*   [Usage](https://docs.openclaw.ai/providers/claude-max-api-proxy#usage)
*   [Start the server](https://docs.openclaw.ai/providers/claude-max-api-proxy#start-the-server)
*   [Test it](https://docs.openclaw.ai/providers/claude-max-api-proxy#test-it)
*   [With OpenClaw](https://docs.openclaw.ai/providers/claude-max-api-proxy#with-openclaw)
*   [Available Models](https://docs.openclaw.ai/providers/claude-max-api-proxy#available-models)
*   [Auto-Start on macOS](https://docs.openclaw.ai/providers/claude-max-api-proxy#auto-start-on-macos)
*   [Links](https://docs.openclaw.ai/providers/claude-max-api-proxy#links)
*   [Notes](https://docs.openclaw.ai/providers/claude-max-api-proxy#notes)
*   [See Also](https://docs.openclaw.ai/providers/claude-max-api-proxy#see-also)

Providers

# Claude Max API Proxy

# [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#claude-max-api-proxy)

Claude Max API Proxy

**claude-max-api-proxy** is a community tool that exposes your Claude Max/Pro subscription as an OpenAI-compatible API endpoint. This allows you to use your subscription with any tool that supports the OpenAI API format.

This path is technical compatibility only. Anthropic has blocked some subscription usage outside Claude Code in the past. You must decide for yourself whether to use it and verify Anthropic’s current terms before relying on it.

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#why-use-this)

Why Use This?

| Approach | Cost | Best For |
| --- | --- | --- |
| Anthropic API | Pay per token (~15/M i n p u t,15/M input, 15/M in p u t,75/M output for Opus) | Production apps, high volume |
| Claude Max subscription | $200/month flat | Personal use, development, unlimited usage |

If you have a Claude Max subscription and want to use it with OpenAI-compatible tools, this proxy may reduce cost for some workflows. API keys remain the clearer policy path for production use.
## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#how-it-works)

How It Works

```
Your App → claude-max-api-proxy → Claude Code CLI → Anthropic (via subscription)
     (OpenAI format)              (converts format)      (uses your login)
```

The proxy:
1.   Accepts OpenAI-format requests at `http://localhost:3456/v1/chat/completions`
2.   Converts them to Claude Code CLI commands
3.   Returns responses in OpenAI format (streaming supported)

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#installation)

Installation

```
# Requires Node.js 20+ and Claude Code CLI
npm install -g claude-max-api-proxy

# Verify Claude CLI is authenticated
claude --version
```

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#usage)

Usage

### [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#start-the-server)

Start the server

```
claude-max-api
# Server runs at http://localhost:3456
```

### [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#test-it)

Test it

```
# Health check
curl http://localhost:3456/health

# List models
curl http://localhost:3456/v1/models

# Chat completion
curl http://localhost:3456/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-opus-4",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#with-openclaw)

With OpenClaw

You can point OpenClaw at the proxy as a custom OpenAI-compatible endpoint:

```
{
  env: {
    OPENAI_API_KEY: "not-needed",
    OPENAI_BASE_URL: "http://localhost:3456/v1",
  },
  agents: {
    defaults: {
      model: { primary: "openai/claude-opus-4" },
    },
  },
}
```

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#available-models)

Available Models

| Model ID | Maps To |
| --- | --- |
| `claude-opus-4` | Claude Opus 4 |
| `claude-sonnet-4` | Claude Sonnet 4 |
| `claude-haiku-4` | Claude Haiku 4 |

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#auto-start-on-macos)

Auto-Start on macOS

Create a LaunchAgent to run the proxy automatically:

```
cat > ~/Library/LaunchAgents/com.claude-max-api.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.claude-max-api</string>
  <key>RunAtLoad</key>
  <true/>
  <key>KeepAlive</key>
  <true/>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/local/bin/node</string>
    <string>/usr/local/lib/node_modules/claude-max-api-proxy/dist/server/standalone.js</string>
  </array>
  <key>EnvironmentVariables</key>
  <dict>
    <key>PATH</key>
    <string>/usr/local/bin:/opt/homebrew/bin:~/.local/bin:/usr/bin:/bin</string>
  </dict>
</dict>
</plist>
EOF

launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.claude-max-api.plist
```

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#links)

Links

*   **npm:**[https://www.npmjs.com/package/claude-max-api-proxy](https://www.npmjs.com/package/claude-max-api-proxy)
*   **GitHub:**[https://github.com/atalovesyou/claude-max-api-proxy](https://github.com/atalovesyou/claude-max-api-proxy)
*   **Issues:**[https://github.com/atalovesyou/claude-max-api-proxy/issues](https://github.com/atalovesyou/claude-max-api-proxy/issues)

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#notes)

Notes

*   This is a **community tool**, not officially supported by Anthropic or OpenClaw
*   Requires an active Claude Max/Pro subscription with Claude Code CLI authenticated
*   The proxy runs locally and does not send data to any third-party servers
*   Streaming responses are fully supported

## [​](https://docs.openclaw.ai/providers/claude-max-api-proxy#see-also)

See Also

*   [Anthropic provider](https://docs.openclaw.ai/providers/anthropic) - Native OpenClaw integration with Claude setup-token or API keys
*   [OpenAI provider](https://docs.openclaw.ai/providers/openai) - For OpenAI/Codex subscriptions

[Amazon Bedrock](https://docs.openclaw.ai/providers/bedrock)[Cloudflare AI Gateway](https://docs.openclaw.ai/providers/cloudflare-ai-gateway)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
