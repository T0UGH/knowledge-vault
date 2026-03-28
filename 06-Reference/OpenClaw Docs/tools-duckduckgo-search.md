# tools/duckduckgo-search

Source: https://docs.openclaw.ai/tools/duckduckgo-search

Title: DuckDuckGo Search - OpenClaw

URL Source: https://docs.openclaw.ai/tools/duckduckgo-search

Markdown Content:
# DuckDuckGo Search - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/duckduckgo-search#content-area)

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

DuckDuckGo Search

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

*   [DuckDuckGo Search](https://docs.openclaw.ai/tools/duckduckgo-search#duckduckgo-search)
*   [Setup](https://docs.openclaw.ai/tools/duckduckgo-search#setup)
*   [Config](https://docs.openclaw.ai/tools/duckduckgo-search#config)
*   [Tool parameters](https://docs.openclaw.ai/tools/duckduckgo-search#tool-parameters)
*   [Notes](https://docs.openclaw.ai/tools/duckduckgo-search#notes)
*   [Related](https://docs.openclaw.ai/tools/duckduckgo-search#related)

Web Tools

# DuckDuckGo Search

# [​](https://docs.openclaw.ai/tools/duckduckgo-search#duckduckgo-search)

DuckDuckGo Search

OpenClaw supports DuckDuckGo as a **key-free**`web_search` provider. No API key or account is required.

DuckDuckGo is an **experimental, unofficial** integration that pulls results from DuckDuckGo’s non-JavaScript search pages — not an official API. Expect occasional breakage from bot-challenge pages or HTML changes.

## [​](https://docs.openclaw.ai/tools/duckduckgo-search#setup)

Setup

No API key needed — just set DuckDuckGo as your provider:

1

[](https://docs.openclaw.ai/tools/duckduckgo-search#)

Configure

```
openclaw configure --section web
# Select "duckduckgo" as the provider
```

## [​](https://docs.openclaw.ai/tools/duckduckgo-search#config)

Config

```
{
  tools: {
    web: {
      search: {
        provider: "duckduckgo",
      },
    },
  },
}
```

Optional plugin-level settings for region and SafeSearch:

```
{
  plugins: {
    entries: {
      duckduckgo: {
        config: {
          webSearch: {
            region: "us-en", // DuckDuckGo region code
            safeSearch: "moderate", // "strict", "moderate", or "off"
          },
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/tools/duckduckgo-search#tool-parameters)

Tool parameters

| Parameter | Description |
| --- | --- |
| `query` | Search query (required) |
| `count` | Results to return (1-10, default: 5) |
| `region` | DuckDuckGo region code (e.g. `us-en`, `uk-en`, `de-de`) |
| `safeSearch` | SafeSearch level: `strict`, `moderate` (default), or `off` |

Region and SafeSearch can also be set in plugin config (see above) — tool parameters override config values per-query.
## [​](https://docs.openclaw.ai/tools/duckduckgo-search#notes)

Notes

*   **No API key** — works out of the box, zero configuration
*   **Experimental** — gathers results from DuckDuckGo’s non-JavaScript HTML search pages, not an official API or SDK
*   **Bot-challenge risk** — DuckDuckGo may serve CAPTCHAs or block requests under heavy or automated use
*   **HTML parsing** — results depend on page structure, which can change without notice
*   **Auto-detection order** — DuckDuckGo is checked last (order 100) in auto-detection, so any API-backed provider with a key takes priority
*   **SafeSearch defaults to moderate** when not configured

For production use, consider [Brave Search](https://docs.openclaw.ai/tools/brave-search) (free tier available) or another API-backed provider.

## [​](https://docs.openclaw.ai/tools/duckduckgo-search#related)

Related

*   [Web Search overview](https://docs.openclaw.ai/tools/web) — all providers and auto-detection
*   [Brave Search](https://docs.openclaw.ai/tools/brave-search) — structured results with free tier
*   [Exa Search](https://docs.openclaw.ai/tools/exa-search) — neural search with content extraction

[Brave Search](https://docs.openclaw.ai/tools/brave-search)[Exa Search](https://docs.openclaw.ai/tools/exa-search)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
