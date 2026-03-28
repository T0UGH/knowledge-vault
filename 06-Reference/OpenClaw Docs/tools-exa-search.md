# tools/exa-search

Source: https://docs.openclaw.ai/tools/exa-search

Title: Exa Search - OpenClaw

URL Source: https://docs.openclaw.ai/tools/exa-search

Markdown Content:
# Exa Search - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/exa-search#content-area)

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

Exa Search

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

*   [Exa Search](https://docs.openclaw.ai/tools/exa-search#exa-search)
*   [Get an API key](https://docs.openclaw.ai/tools/exa-search#get-an-api-key)
*   [Config](https://docs.openclaw.ai/tools/exa-search#config)
*   [Tool parameters](https://docs.openclaw.ai/tools/exa-search#tool-parameters)
*   [Content extraction](https://docs.openclaw.ai/tools/exa-search#content-extraction)
*   [Search modes](https://docs.openclaw.ai/tools/exa-search#search-modes)
*   [Notes](https://docs.openclaw.ai/tools/exa-search#notes)
*   [Related](https://docs.openclaw.ai/tools/exa-search#related)

Web Tools

# Exa Search

# [​](https://docs.openclaw.ai/tools/exa-search#exa-search)

Exa Search

OpenClaw supports [Exa AI](https://exa.ai/) as a `web_search` provider. Exa offers neural, keyword, and hybrid search modes with built-in content extraction (highlights, text, summaries).
## [​](https://docs.openclaw.ai/tools/exa-search#get-an-api-key)

Get an API key

1

[](https://docs.openclaw.ai/tools/exa-search#)

Create an account

Sign up at [exa.ai](https://exa.ai/) and generate an API key from your dashboard.

2

[](https://docs.openclaw.ai/tools/exa-search#)

Store the key

Set `EXA_API_KEY` in the Gateway environment, or configure via:

```
openclaw configure --section web
```

## [​](https://docs.openclaw.ai/tools/exa-search#config)

Config

```
{
  plugins: {
    entries: {
      exa: {
        config: {
          webSearch: {
            apiKey: "exa-...", // optional if EXA_API_KEY is set
          },
        },
      },
    },
  },
  tools: {
    web: {
      search: {
        provider: "exa",
      },
    },
  },
}
```

**Environment alternative:** set `EXA_API_KEY` in the Gateway environment. For a gateway install, put it in `~/.openclaw/.env`.
## [​](https://docs.openclaw.ai/tools/exa-search#tool-parameters)

Tool parameters

| Parameter | Description |
| --- | --- |
| `query` | Search query (required) |
| `count` | Results to return (1-100) |
| `type` | Search mode: `auto`, `neural`, `fast`, `deep`, `deep-reasoning`, or `instant` |
| `freshness` | Time filter: `day`, `week`, `month`, or `year` |
| `date_after` | Results after this date (YYYY-MM-DD) |
| `date_before` | Results before this date (YYYY-MM-DD) |
| `contents` | Content extraction options (see below) |

### [​](https://docs.openclaw.ai/tools/exa-search#content-extraction)

Content extraction

Exa can return extracted content alongside search results. Pass a `contents` object to enable:

```
await web_search({
  query: "transformer architecture explained",
  type: "neural",
  contents: {
    text: true, // full page text
    highlights: { numSentences: 3 }, // key sentences
    summary: true, // AI summary
  },
});
```

| Contents option | Type | Description |
| --- | --- | --- |
| `text` | `boolean | { maxCharacters }` | Extract full page text |
| `highlights` | `boolean | { maxCharacters, query, numSentences, highlightsPerUrl }` | Extract key sentences |
| `summary` | `boolean | { query }` | AI-generated summary |

### [​](https://docs.openclaw.ai/tools/exa-search#search-modes)

Search modes

| Mode | Description |
| --- | --- |
| `auto` | Exa picks the best mode (default) |
| `neural` | Semantic/meaning-based search |
| `fast` | Quick keyword search |
| `deep` | Thorough deep search |
| `deep-reasoning` | Deep search with reasoning |
| `instant` | Fastest results |

## [​](https://docs.openclaw.ai/tools/exa-search#notes)

Notes

*   If no `contents` option is provided, Exa defaults to `{ highlights: true }` so results include key sentence excerpts
*   Results preserve `highlightScores` and `summary` fields from the Exa API response when available
*   Result descriptions are resolved from highlights first, then summary, then full text — whichever is available
*   `freshness` and `date_after`/`date_before` cannot be combined — use one time-filter mode
*   Up to 100 results can be returned per query (subject to Exa search-type limits)
*   Results are cached for 15 minutes by default (configurable via `cacheTtlMinutes`)
*   Exa is an official API integration with structured JSON responses

## [​](https://docs.openclaw.ai/tools/exa-search#related)

Related

*   [Web Search overview](https://docs.openclaw.ai/tools/web) — all providers and auto-detection
*   [Brave Search](https://docs.openclaw.ai/tools/brave-search) — structured results with country/language filters
*   [Perplexity Search](https://docs.openclaw.ai/tools/perplexity-search) — structured results with domain filtering

[DuckDuckGo Search](https://docs.openclaw.ai/tools/duckduckgo-search)[Firecrawl](https://docs.openclaw.ai/tools/firecrawl)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
