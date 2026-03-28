# tools/grok-search

Source: https://docs.openclaw.ai/tools/grok-search

Title: Grok Search - OpenClaw

URL Source: https://docs.openclaw.ai/tools/grok-search

Markdown Content:
# Grok Search - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/grok-search#content-area)

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

Grok Search

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

*   [Grok Search](https://docs.openclaw.ai/tools/grok-search#grok-search)
*   [Get an API key](https://docs.openclaw.ai/tools/grok-search#get-an-api-key)
*   [Config](https://docs.openclaw.ai/tools/grok-search#config)
*   [How it works](https://docs.openclaw.ai/tools/grok-search#how-it-works)
*   [Supported parameters](https://docs.openclaw.ai/tools/grok-search#supported-parameters)
*   [Related](https://docs.openclaw.ai/tools/grok-search#related)

Web Tools

# Grok Search

# [​](https://docs.openclaw.ai/tools/grok-search#grok-search)

Grok Search

OpenClaw supports Grok as a `web_search` provider, using xAI web-grounded responses to produce AI-synthesized answers backed by live search results with citations.
## [​](https://docs.openclaw.ai/tools/grok-search#get-an-api-key)

Get an API key

1

[](https://docs.openclaw.ai/tools/grok-search#)

Create a key

Get an API key from [xAI](https://console.x.ai/).

2

[](https://docs.openclaw.ai/tools/grok-search#)

Store the key

Set `XAI_API_KEY` in the Gateway environment, or configure via:

```
openclaw configure --section web
```

## [​](https://docs.openclaw.ai/tools/grok-search#config)

Config

```
{
  plugins: {
    entries: {
      xai: {
        config: {
          webSearch: {
            apiKey: "xai-...", // optional if XAI_API_KEY is set
          },
        },
      },
    },
  },
  tools: {
    web: {
      search: {
        provider: "grok",
      },
    },
  },
}
```

**Environment alternative:** set `XAI_API_KEY` in the Gateway environment. For a gateway install, put it in `~/.openclaw/.env`.
## [​](https://docs.openclaw.ai/tools/grok-search#how-it-works)

How it works

Grok uses xAI web-grounded responses to synthesize answers with inline citations, similar to Gemini’s Google Search grounding approach.
## [​](https://docs.openclaw.ai/tools/grok-search#supported-parameters)

Supported parameters

Grok search supports the standard `query` and `count` parameters. Provider-specific filters are not currently supported.
## [​](https://docs.openclaw.ai/tools/grok-search#related)

Related

*   [Web Search overview](https://docs.openclaw.ai/tools/web) — all providers and auto-detection
*   [Gemini Search](https://docs.openclaw.ai/tools/gemini-search) — AI-synthesized answers via Google grounding

[Gemini Search](https://docs.openclaw.ai/tools/gemini-search)[Kimi Search](https://docs.openclaw.ai/tools/kimi-search)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
