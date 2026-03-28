# tools/firecrawl

Source: https://docs.openclaw.ai/tools/firecrawl

Title: Firecrawl - OpenClaw

URL Source: https://docs.openclaw.ai/tools/firecrawl

Markdown Content:
# Firecrawl - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/firecrawl#content-area)

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

Firecrawl

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

*   [Firecrawl](https://docs.openclaw.ai/tools/firecrawl#firecrawl)
*   [Get an API key](https://docs.openclaw.ai/tools/firecrawl#get-an-api-key)
*   [Configure Firecrawl search](https://docs.openclaw.ai/tools/firecrawl#configure-firecrawl-search)
*   [Configure Firecrawl scrape + web_fetch fallback](https://docs.openclaw.ai/tools/firecrawl#configure-firecrawl-scrape-%2B-web_fetch-fallback)
*   [Firecrawl plugin tools](https://docs.openclaw.ai/tools/firecrawl#firecrawl-plugin-tools)
*   [firecrawl_search](https://docs.openclaw.ai/tools/firecrawl#firecrawl_search)
*   [firecrawl_scrape](https://docs.openclaw.ai/tools/firecrawl#firecrawl_scrape)
*   [Stealth / bot circumvention](https://docs.openclaw.ai/tools/firecrawl#stealth-%2F-bot-circumvention)
*   [How web_fetch uses Firecrawl](https://docs.openclaw.ai/tools/firecrawl#how-web_fetch-uses-firecrawl)
*   [Related](https://docs.openclaw.ai/tools/firecrawl#related)

Web Tools

# Firecrawl

# [​](https://docs.openclaw.ai/tools/firecrawl#firecrawl)

Firecrawl

OpenClaw can use **Firecrawl** in three ways:
*   as the `web_search` provider
*   as explicit plugin tools: `firecrawl_search` and `firecrawl_scrape`
*   as a fallback extractor for `web_fetch`

It is a hosted extraction/search service that supports bot circumvention and caching, which helps with JS-heavy sites or pages that block plain HTTP fetches.
## [​](https://docs.openclaw.ai/tools/firecrawl#get-an-api-key)

Get an API key

1.   Create a Firecrawl account and generate an API key.
2.   Store it in config or set `FIRECRAWL_API_KEY` in the gateway environment.

## [​](https://docs.openclaw.ai/tools/firecrawl#configure-firecrawl-search)

Configure Firecrawl search

```
{
  tools: {
    web: {
      search: {
        provider: "firecrawl",
      },
    },
  },
  plugins: {
    entries: {
      firecrawl: {
        enabled: true,
        config: {
          webSearch: {
            apiKey: "FIRECRAWL_API_KEY_HERE",
            baseUrl: "https://api.firecrawl.dev",
          },
        },
      },
    },
  },
}
```

Notes:
*   Choosing Firecrawl in onboarding or `openclaw configure --section web` enables the bundled Firecrawl plugin automatically.
*   `web_search` with Firecrawl supports `query` and `count`.
*   For Firecrawl-specific controls like `sources`, `categories`, or result scraping, use `firecrawl_search`.

## [​](https://docs.openclaw.ai/tools/firecrawl#configure-firecrawl-scrape-+-web_fetch-fallback)

Configure Firecrawl scrape + web_fetch fallback

```
{
  plugins: {
    entries: {
      firecrawl: {
        enabled: true,
      },
    },
  },
  tools: {
    web: {
      fetch: {
        firecrawl: {
          apiKey: "FIRECRAWL_API_KEY_HERE",
          baseUrl: "https://api.firecrawl.dev",
          onlyMainContent: true,
          maxAgeMs: 172800000,
          timeoutSeconds: 60,
        },
      },
    },
  },
}
```

Notes:
*   `firecrawl.enabled` defaults to `true` unless explicitly set to `false`.
*   Firecrawl fallback attempts run only when an API key is available (`tools.web.fetch.firecrawl.apiKey` or `FIRECRAWL_API_KEY`).
*   `maxAgeMs` controls how old cached results can be (ms). Default is 2 days.

`firecrawl_scrape` reuses the same `tools.web.fetch.firecrawl.*` settings and env vars.
## [​](https://docs.openclaw.ai/tools/firecrawl#firecrawl-plugin-tools)

Firecrawl plugin tools

### [​](https://docs.openclaw.ai/tools/firecrawl#firecrawl_search)

`firecrawl_search`

Use this when you want Firecrawl-specific search controls instead of generic `web_search`.Core parameters:
*   `query`
*   `count`
*   `sources`
*   `categories`
*   `scrapeResults`
*   `timeoutSeconds`

### [​](https://docs.openclaw.ai/tools/firecrawl#firecrawl_scrape)

`firecrawl_scrape`

Use this for JS-heavy or bot-protected pages where plain `web_fetch` is weak.Core parameters:
*   `url`
*   `extractMode`
*   `maxChars`
*   `onlyMainContent`
*   `maxAgeMs`
*   `proxy`
*   `storeInCache`
*   `timeoutSeconds`

## [​](https://docs.openclaw.ai/tools/firecrawl#stealth-/-bot-circumvention)

Stealth / bot circumvention

Firecrawl exposes a **proxy mode** parameter for bot circumvention (`basic`, `stealth`, or `auto`). OpenClaw always uses `proxy: "auto"` plus `storeInCache: true` for Firecrawl requests. If proxy is omitted, Firecrawl defaults to `auto`. `auto` retries with stealth proxies if a basic attempt fails, which may use more credits than basic-only scraping.
## [​](https://docs.openclaw.ai/tools/firecrawl#how-web_fetch-uses-firecrawl)

How `web_fetch` uses Firecrawl

`web_fetch` extraction order:
1.   Readability (local)
2.   Firecrawl (if configured)
3.   Basic HTML cleanup (last fallback)

## [​](https://docs.openclaw.ai/tools/firecrawl#related)

Related

*   [Web Search overview](https://docs.openclaw.ai/tools/web) — all providers and auto-detection
*   [Web Fetch](https://docs.openclaw.ai/tools/web-fetch) — web_fetch tool with Firecrawl fallback
*   [Tavily](https://docs.openclaw.ai/tools/tavily) — search + extract tools

[Exa Search](https://docs.openclaw.ai/tools/exa-search)[Gemini Search](https://docs.openclaw.ai/tools/gemini-search)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
