# tools/gemini-search

Source: https://docs.openclaw.ai/tools/gemini-search

Title: Gemini Search - OpenClaw

URL Source: https://docs.openclaw.ai/tools/gemini-search

Markdown Content:
# Gemini Search - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/gemini-search#content-area)

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

Web Tools

Gemini Search

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Tools and Plugins](https://docs.openclaw.ai/tools)

##### Plugins

*   [Install and Configure](https://docs.openclaw.ai/tools/plugin)
*   [Community Plugins](https://docs.openclaw.ai/plugins/community)
*   [Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)
*   Building Plugins 
*   SDK Reference 

##### Skills

*   [Skills](https://docs.openclaw.ai/tools/skills)
*   [Creating Skills](https://docs.openclaw.ai/tools/creating-skills)
*   [Skills Config](https://docs.openclaw.ai/tools/skills-config)
*   [Slash Commands](https://docs.openclaw.ai/tools/slash-commands)
*   [ClawHub](https://docs.openclaw.ai/tools/clawhub)
*   [OpenProse](https://docs.openclaw.ai/prose)

##### Automation

*   [Hooks](https://docs.openclaw.ai/automation/hooks)
*   [Standing Orders](https://docs.openclaw.ai/automation/standing-orders)
*   [Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs)
*   [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)
*   [Automation Troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)
*   [Webhooks](https://docs.openclaw.ai/automation/webhook)
*   [Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub)
*   [Polls](https://docs.openclaw.ai/automation/poll)
*   [Auth Monitoring](https://docs.openclaw.ai/automation/auth-monitoring)

##### Tools

*   [apply_patch Tool](https://docs.openclaw.ai/tools/apply-patch)
*   Web Browser 
*   Web Tools 
    *   [Web Fetch](https://docs.openclaw.ai/tools/web-fetch)
    *   [Web Search](https://docs.openclaw.ai/tools/web)
    *   [Brave Search](https://docs.openclaw.ai/tools/brave-search)
    *   [DuckDuckGo Search](https://docs.openclaw.ai/tools/duckduckgo-search)
    *   [Exa Search](https://docs.openclaw.ai/tools/exa-search)
    *   [Firecrawl](https://docs.openclaw.ai/tools/firecrawl)
    *   [Gemini Search](https://docs.openclaw.ai/tools/gemini-search)
    *   [Grok Search](https://docs.openclaw.ai/tools/grok-search)
    *   [Kimi Search](https://docs.openclaw.ai/tools/kimi-search)
    *   [Perplexity Search](https://docs.openclaw.ai/tools/perplexity-search)
    *   [Tavily](https://docs.openclaw.ai/tools/tavily)

*   [BTW Side Questions](https://docs.openclaw.ai/tools/btw)
*   [Diffs](https://docs.openclaw.ai/tools/diffs)
*   [Elevated Mode](https://docs.openclaw.ai/tools/elevated)
*   [Exec Tool](https://docs.openclaw.ai/tools/exec)
*   [Exec Approvals](https://docs.openclaw.ai/tools/exec-approvals)
*   [LLM Task](https://docs.openclaw.ai/tools/llm-task)
*   [Lobster](https://docs.openclaw.ai/tools/lobster)
*   [Tool-loop detection](https://docs.openclaw.ai/tools/loop-detection)
*   [PDF Tool](https://docs.openclaw.ai/tools/pdf)
*   [Reactions](https://docs.openclaw.ai/tools/reactions)
*   [Thinking Levels](https://docs.openclaw.ai/tools/thinking)

##### Agent coordination

*   [Agent Send](https://docs.openclaw.ai/tools/agent-send)
*   [Sub-Agents](https://docs.openclaw.ai/tools/subagents)
*   [ACP Agents](https://docs.openclaw.ai/tools/acp-agents)
*   [Multi-Agent Sandbox & Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools)

On this page

*   [Gemini Search](https://docs.openclaw.ai/tools/gemini-search#gemini-search)
*   [Get an API key](https://docs.openclaw.ai/tools/gemini-search#get-an-api-key)
*   [Config](https://docs.openclaw.ai/tools/gemini-search#config)
*   [How it works](https://docs.openclaw.ai/tools/gemini-search#how-it-works)
*   [Supported parameters](https://docs.openclaw.ai/tools/gemini-search#supported-parameters)
*   [Model selection](https://docs.openclaw.ai/tools/gemini-search#model-selection)
*   [Related](https://docs.openclaw.ai/tools/gemini-search#related)

Web Tools

# Gemini Search

# [​](https://docs.openclaw.ai/tools/gemini-search#gemini-search)

Gemini Search

OpenClaw supports Gemini models with built-in [Google Search grounding](https://ai.google.dev/gemini-api/docs/grounding), which returns AI-synthesized answers backed by live Google Search results with citations.
## [​](https://docs.openclaw.ai/tools/gemini-search#get-an-api-key)

Get an API key

1

[](https://docs.openclaw.ai/tools/gemini-search#)

Create a key

Go to [Google AI Studio](https://aistudio.google.com/apikey) and create an API key.

2

[](https://docs.openclaw.ai/tools/gemini-search#)

Store the key

Set `GEMINI_API_KEY` in the Gateway environment, or configure via:

```
openclaw configure --section web
```

## [​](https://docs.openclaw.ai/tools/gemini-search#config)

Config

```
{
  plugins: {
    entries: {
      google: {
        config: {
          webSearch: {
            apiKey: "AIza...", // optional if GEMINI_API_KEY is set
            model: "gemini-2.5-flash", // default
          },
        },
      },
    },
  },
  tools: {
    web: {
      search: {
        provider: "gemini",
      },
    },
  },
}
```

**Environment alternative:** set `GEMINI_API_KEY` in the Gateway environment. For a gateway install, put it in `~/.openclaw/.env`.
## [​](https://docs.openclaw.ai/tools/gemini-search#how-it-works)

How it works

Unlike traditional search providers that return a list of links and snippets, Gemini uses Google Search grounding to produce AI-synthesized answers with inline citations. The results include both the synthesized answer and the source URLs.
*   Citation URLs from Gemini grounding are automatically resolved from Google redirect URLs to direct URLs.
*   Redirect resolution uses the SSRF guard path (HEAD + redirect checks + http/https validation) before returning the final citation URL.
*   Redirect resolution uses strict SSRF defaults, so redirects to private/internal targets are blocked.

## [​](https://docs.openclaw.ai/tools/gemini-search#supported-parameters)

Supported parameters

Gemini search supports the standard `query` and `count` parameters. Provider-specific filters like `country`, `language`, `freshness`, and `domain_filter` are not supported.
## [​](https://docs.openclaw.ai/tools/gemini-search#model-selection)

Model selection

The default model is `gemini-2.5-flash` (fast and cost-effective). Any Gemini model that supports grounding can be used via `plugins.entries.google.config.webSearch.model`.
## [​](https://docs.openclaw.ai/tools/gemini-search#related)

Related

*   [Web Search overview](https://docs.openclaw.ai/tools/web) — all providers and auto-detection
*   [Brave Search](https://docs.openclaw.ai/tools/brave-search) — structured results with snippets
*   [Perplexity Search](https://docs.openclaw.ai/tools/perplexity-search) — structured results + content extraction

[Firecrawl](https://docs.openclaw.ai/tools/firecrawl)[Grok Search](https://docs.openclaw.ai/tools/grok-search)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
