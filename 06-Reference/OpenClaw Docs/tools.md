# tools

Source: https://docs.openclaw.ai/tools

Title: Tools and Plugins - OpenClaw

URL Source: https://docs.openclaw.ai/tools

Markdown Content:
Everything the agent does beyond generating text happens through **tools**. Tools are how the agent reads files, runs commands, browses the web, sends messages, and interacts with devices.

## Tools, skills, and plugins

OpenClaw has three layers that work together:

1

2

3

## Built-in tools

These tools ship with OpenClaw and are available without installing any plugins:

| Tool | What it does | Page |
| --- | --- | --- |
| `exec` / `process` | Run shell commands, manage background processes | [Exec](https://docs.openclaw.ai/tools/exec) |
| `browser` | Control a Chromium browser (navigate, click, screenshot) | [Browser](https://docs.openclaw.ai/tools/browser) |
| `web_search` / `web_fetch` | Search the web, fetch page content | [Web](https://docs.openclaw.ai/tools/web) |
| `read` / `write` / `edit` | File I/O in the workspace |  |
| `apply_patch` | Multi-hunk file patches | [Apply Patch](https://docs.openclaw.ai/tools/apply-patch) |
| `message` | Send messages across all channels | [Agent Send](https://docs.openclaw.ai/tools/agent-send) |
| `canvas` | Drive node Canvas (present, eval, snapshot) |  |
| `nodes` | Discover and target paired devices |  |
| `cron` / `gateway` | Manage scheduled jobs, restart gateway |  |
| `image` / `image_generate` | Analyze or generate images |  |
| `sessions_*` / `agents_list` | Session management, sub-agents | [Sub-agents](https://docs.openclaw.ai/tools/subagents) |

For image work, use `image` for analysis and `image_generate` for generation or editing. If you target `openai/*`, `google/*`, `fal/*`, or another non-default image provider, configure that provider’s auth/API key first.

### Plugin-provided tools

Plugins can register additional tools. Some examples:

*   [Lobster](https://docs.openclaw.ai/tools/lobster) — typed workflow runtime with resumable approvals
*   [LLM Task](https://docs.openclaw.ai/tools/llm-task) — JSON-only LLM step for structured output
*   [Diffs](https://docs.openclaw.ai/tools/diffs) — diff viewer and renderer
*   [OpenProse](https://docs.openclaw.ai/prose) — markdown-first workflow orchestration

## Tool configuration

### Allow and deny lists

Control which tools the agent can call via `tools.allow` / `tools.deny` in config. Deny always wins over allow.

```
{
  tools: {
    allow: ["group:fs", "browser", "web_search"],
    deny: ["exec"],
  },
}
```

### Tool profiles

`tools.profile` sets a base allowlist before `allow`/`deny` is applied. Per-agent override: `agents.list[].tools.profile`.

| Profile | What it includes |
| --- | --- |
| `full` | All tools (default) |
| `coding` | File I/O, runtime, sessions, memory, image |
| `messaging` | Messaging, session list/history/send/status |
| `minimal` | `session_status` only |

### Tool groups

Use `group:*` shorthands in allow/deny lists:

| Group | Tools |
| --- | --- |
| `group:runtime` | exec, bash, process |
| `group:fs` | read, write, edit, apply_patch |
| `group:sessions` | sessions_list, sessions_history, sessions_send, sessions_spawn, sessions_yield, subagents, session_status |
| `group:memory` | memory_search, memory_get |
| `group:web` | web_search, web_fetch |
| `group:ui` | browser, canvas |
| `group:automation` | cron, gateway |
| `group:messaging` | message |
| `group:nodes` | nodes |
| `group:openclaw` | All built-in OpenClaw tools (excludes plugin tools) |

### Provider-specific restrictions

Use `tools.byProvider` to restrict tools for specific providers without changing global defaults:

```
{
  tools: {
    profile: "coding",
    byProvider: {
      "google-antigravity": { profile: "minimal" },
    },
  },
}
```
