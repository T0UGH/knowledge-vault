# prose

Source: https://docs.openclaw.ai/prose

Title: OpenProse - OpenClaw

URL Source: https://docs.openclaw.ai/prose

Markdown Content:
# OpenProse - OpenClaw

[Skip to main content](https://docs.openclaw.ai/prose#content-area)

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

Skills

OpenProse

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

*   [OpenProse](https://docs.openclaw.ai/prose#openprose)
*   [What it can do](https://docs.openclaw.ai/prose#what-it-can-do)
*   [Install + enable](https://docs.openclaw.ai/prose#install-%2B-enable)
*   [Slash command](https://docs.openclaw.ai/prose#slash-command)
*   [Example: a simple .prose file](https://docs.openclaw.ai/prose#example-a-simple-prose-file)
*   [File locations](https://docs.openclaw.ai/prose#file-locations)
*   [State modes](https://docs.openclaw.ai/prose#state-modes)
*   [Remote programs](https://docs.openclaw.ai/prose#remote-programs)
*   [OpenClaw runtime mapping](https://docs.openclaw.ai/prose#openclaw-runtime-mapping)
*   [Security + approvals](https://docs.openclaw.ai/prose#security-%2B-approvals)

Skills

# OpenProse

# [​](https://docs.openclaw.ai/prose#openprose)

OpenProse

OpenProse is a portable, markdown-first workflow format for orchestrating AI sessions. In OpenClaw it ships as a plugin that installs an OpenProse skill pack plus a `/prose` slash command. Programs live in `.prose` files and can spawn multiple sub-agents with explicit control flow.Official site: [https://www.prose.md](https://www.prose.md/)
## [​](https://docs.openclaw.ai/prose#what-it-can-do)

What it can do

*   Multi-agent research + synthesis with explicit parallelism.
*   Repeatable approval-safe workflows (code review, incident triage, content pipelines).
*   Reusable `.prose` programs you can run across supported agent runtimes.

## [​](https://docs.openclaw.ai/prose#install-+-enable)

Install + enable

Bundled plugins are disabled by default. Enable OpenProse:

```
openclaw plugins enable open-prose
```

Restart the Gateway after enabling the plugin.Dev/local checkout: `openclaw plugins install ./extensions/open-prose`Related docs: [Plugins](https://docs.openclaw.ai/tools/plugin), [Plugin manifest](https://docs.openclaw.ai/plugins/manifest), [Skills](https://docs.openclaw.ai/tools/skills).
## [​](https://docs.openclaw.ai/prose#slash-command)

Slash command

OpenProse registers `/prose` as a user-invocable skill command. It routes to the OpenProse VM instructions and uses OpenClaw tools under the hood.Common commands:

```
/prose help
/prose run <file.prose>
/prose run <handle/slug>
/prose run <https://example.com/file.prose>
/prose compile <file.prose>
/prose examples
/prose update
```

## [​](https://docs.openclaw.ai/prose#example-a-simple-prose-file)

Example: a simple `.prose` file

```
# Research + synthesis with two agents running in parallel.

input topic: "What should we research?"

agent researcher:
  model: sonnet
  prompt: "You research thoroughly and cite sources."

agent writer:
  model: opus
  prompt: "You write a concise summary."

parallel:
  findings = session: researcher
    prompt: "Research {topic}."
  draft = session: writer
    prompt: "Summarize {topic}."

session "Merge the findings + draft into a final answer."
context: { findings, draft }
```

## [​](https://docs.openclaw.ai/prose#file-locations)

File locations

OpenProse keeps state under `.prose/` in your workspace:

```
.prose/
├── .env
├── runs/
│   └── {YYYYMMDD}-{HHMMSS}-{random}/
│       ├── program.prose
│       ├── state.md
│       ├── bindings/
│       └── agents/
└── agents/
```

User-level persistent agents live at:

```
~/.prose/agents/
```

## [​](https://docs.openclaw.ai/prose#state-modes)

State modes

OpenProse supports multiple state backends:
*   **filesystem** (default): `.prose/runs/...`
*   **in-context**: transient, for small programs
*   **sqlite** (experimental): requires `sqlite3` binary
*   **postgres** (experimental): requires `psql` and a connection string

Notes:
*   sqlite/postgres are opt-in and experimental.
*   postgres credentials flow into subagent logs; use a dedicated, least-privileged DB.

## [​](https://docs.openclaw.ai/prose#remote-programs)

Remote programs

`/prose run <handle/slug>` resolves to `https://p.prose.md/<handle>/<slug>`. Direct URLs are fetched as-is. This uses the `web_fetch` tool (or `exec` for POST).
## [​](https://docs.openclaw.ai/prose#openclaw-runtime-mapping)

OpenClaw runtime mapping

OpenProse programs map to OpenClaw primitives:

| OpenProse concept | OpenClaw tool |
| --- | --- |
| Spawn session / Task tool | `sessions_spawn` |
| File read/write | `read` / `write` |
| Web fetch | `web_fetch` |

If your tool allowlist blocks these tools, OpenProse programs will fail. See [Skills config](https://docs.openclaw.ai/tools/skills-config).
## [​](https://docs.openclaw.ai/prose#security-+-approvals)

Security + approvals

Treat `.prose` files like code. Review before running. Use OpenClaw tool allowlists and approval gates to control side effects.For deterministic, approval-gated workflows, compare with [Lobster](https://docs.openclaw.ai/tools/lobster).

[ClawHub](https://docs.openclaw.ai/tools/clawhub)[Hooks](https://docs.openclaw.ai/automation/hooks)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
