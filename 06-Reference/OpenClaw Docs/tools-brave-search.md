# tools/brave-search

Source: https://docs.openclaw.ai/tools/brave-search

Title: Brave Search - OpenClaw

URL Source: https://docs.openclaw.ai/tools/brave-search

Markdown Content:
# Brave Search - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/brave-search#content-area)

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

Brave Search

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

*   [Brave Search API](https://docs.openclaw.ai/tools/brave-search#brave-search-api)
*   [Get an API key](https://docs.openclaw.ai/tools/brave-search#get-an-api-key)
*   [Config example](https://docs.openclaw.ai/tools/brave-search#config-example)
*   [Tool parameters](https://docs.openclaw.ai/tools/brave-search#tool-parameters)
*   [Notes](https://docs.openclaw.ai/tools/brave-search#notes)
*   [Related](https://docs.openclaw.ai/tools/brave-search#related)

Web Tools

# Brave Search

# [​](https://docs.openclaw.ai/tools/brave-search#brave-search-api)

Brave Search API

OpenClaw supports Brave Search API as a `web_search` provider.
## [​](https://docs.openclaw.ai/tools/brave-search#get-an-api-key)

Get an API key

1.   Create a Brave Search API account at [https://brave.com/search/api/](https://brave.com/search/api/)
2.   In the dashboard, choose the **Search** plan and generate an API key.
3.   Store the key in config or set `BRAVE_API_KEY` in the Gateway environment.

## [​](https://docs.openclaw.ai/tools/brave-search#config-example)

Config example

```
{
  plugins: {
    entries: {
      brave: {
        config: {
          webSearch: {
            apiKey: "BRAVE_API_KEY_HERE",
          },
        },
      },
    },
  },
  tools: {
    web: {
      search: {
        provider: "brave",
        maxResults: 5,
        timeoutSeconds: 30,
      },
    },
  },
}
```

Provider-specific Brave search settings now live under `plugins.entries.brave.config.webSearch.*`. Legacy `tools.web.search.apiKey` still loads through the compatibility shim, but it is no longer the canonical config path.
## [​](https://docs.openclaw.ai/tools/brave-search#tool-parameters)

Tool parameters

| Parameter | Description |
| --- | --- |
| `query` | Search query (required) |
| `count` | Number of results to return (1-10, default: 5) |
| `country` | 2-letter ISO country code (e.g., “US”, “DE”) |
| `language` | ISO 639-1 language code for search results (e.g., “en”, “de”, “fr”) |
| `ui_lang` | ISO language code for UI elements |
| `freshness` | Time filter: `day` (24h), `week`, `month`, or `year` |
| `date_after` | Only results published after this date (YYYY-MM-DD) |
| `date_before` | Only results published before this date (YYYY-MM-DD) |

**Examples:**

```
// Country and language-specific search
await web_search({
  query: "renewable energy",
  country: "DE",
  language: "de",
});

// Recent results (past week)
await web_search({
  query: "AI news",
  freshness: "week",
});

// Date range search
await web_search({
  query: "AI developments",
  date_after: "2024-01-01",
  date_before: "2024-06-30",
});
```

## [​](https://docs.openclaw.ai/tools/brave-search#notes)

Notes

*   OpenClaw uses the Brave **Search** plan. If you have a legacy subscription (e.g. the original Free plan with 2,000 queries/month), it remains valid but does not include newer features like LLM Context or higher rate limits.
*   Each Brave plan includes **$5/month in free credit** (renewing). The Search plan costs $5 per 1,000 requests, so the credit covers 1,000 queries/month. Set your usage limit in the Brave dashboard to avoid unexpected charges. See the [Brave API portal](https://brave.com/search/api/) for current plans.
*   The Search plan includes the LLM Context endpoint and AI inference rights. Storing results to train or tune models requires a plan with explicit storage rights. See the Brave [Terms of Service](https://api-dashboard.search.brave.com/terms-of-service).
*   Results are cached for 15 minutes by default (configurable via `cacheTtlMinutes`).

## [​](https://docs.openclaw.ai/tools/brave-search#related)

Related

*   [Web Search overview](https://docs.openclaw.ai/tools/web) — all providers and auto-detection
*   [Perplexity Search](https://docs.openclaw.ai/tools/perplexity-search) — structured results with domain filtering
*   [Exa Search](https://docs.openclaw.ai/tools/exa-search) — neural search with content extraction

[Web Search](https://docs.openclaw.ai/tools/web)[DuckDuckGo Search](https://docs.openclaw.ai/tools/duckduckgo-search)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
