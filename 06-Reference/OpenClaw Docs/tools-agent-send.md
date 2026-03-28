# tools/agent-send

Source: https://docs.openclaw.ai/tools/agent-send

Title: Agent Send - OpenClaw

URL Source: https://docs.openclaw.ai/tools/agent-send

Markdown Content:
# Agent Send - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/agent-send#content-area)

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

Agent coordination

Agent Send

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

*   [Agent Send](https://docs.openclaw.ai/tools/agent-send#agent-send)
*   [Quick start](https://docs.openclaw.ai/tools/agent-send#quick-start)
*   [Flags](https://docs.openclaw.ai/tools/agent-send#flags)
*   [Behavior](https://docs.openclaw.ai/tools/agent-send#behavior)
*   [Examples](https://docs.openclaw.ai/tools/agent-send#examples)
*   [Related](https://docs.openclaw.ai/tools/agent-send#related)

Agent coordination

# Agent Send

# [​](https://docs.openclaw.ai/tools/agent-send#agent-send)

Agent Send

`openclaw agent` runs a single agent turn from the command line without needing an inbound chat message. Use it for scripted workflows, testing, and programmatic delivery.
## [​](https://docs.openclaw.ai/tools/agent-send#quick-start)

Quick start

1

[](https://docs.openclaw.ai/tools/agent-send#)

Run a simple agent turn

```
openclaw agent --message "What is the weather today?"
```

This sends the message through the Gateway and prints the reply.

2

[](https://docs.openclaw.ai/tools/agent-send#)

Target a specific agent or session

```
# Target a specific agent
openclaw agent --agent ops --message "Summarize logs"

# Target a phone number (derives session key)
openclaw agent --to +15555550123 --message "Status update"

# Reuse an existing session
openclaw agent --session-id abc123 --message "Continue the task"
```

3

[](https://docs.openclaw.ai/tools/agent-send#)

Deliver the reply to a channel

```
# Deliver to WhatsApp (default channel)
openclaw agent --to +15555550123 --message "Report ready" --deliver

# Deliver to Slack
openclaw agent --agent ops --message "Generate report" \
  --deliver --reply-channel slack --reply-to "#reports"
```

## [​](https://docs.openclaw.ai/tools/agent-send#flags)

Flags

| Flag | Description |
| --- | --- |
| `--message \<text\>` | Message to send (required) |
| `--to \<dest\>` | Derive session key from a target (phone, chat id) |
| `--agent \<id\>` | Target a configured agent (uses its `main` session) |
| `--session-id \<id\>` | Reuse an existing session by id |
| `--local` | Force local embedded runtime (skip Gateway) |
| `--deliver` | Send the reply to a chat channel |
| `--channel \<name\>` | Delivery channel (whatsapp, telegram, discord, slack, etc.) |
| `--reply-to \<target\>` | Delivery target override |
| `--reply-channel \<name\>` | Delivery channel override |
| `--reply-account \<id\>` | Delivery account id override |
| `--thinking \<level\>` | Set thinking level (off, minimal, low, medium, high, xhigh) |
| `--verbose \<on|full|off\>` | Set verbose level |
| `--timeout \<seconds\>` | Override agent timeout |
| `--json` | Output structured JSON |

## [​](https://docs.openclaw.ai/tools/agent-send#behavior)

Behavior

*   By default, the CLI goes **through the Gateway**. Add `--local` to force the embedded runtime on the current machine.
*   If the Gateway is unreachable, the CLI **falls back** to the local embedded run.
*   Session selection: `--to` derives the session key (group/channel targets preserve isolation; direct chats collapse to `main`).
*   Thinking and verbose flags persist into the session store.
*   Output: plain text by default, or `--json` for structured payload + metadata.

## [​](https://docs.openclaw.ai/tools/agent-send#examples)

Examples

```
# Simple turn with JSON output
openclaw agent --to +15555550123 --message "Trace logs" --verbose on --json

# Turn with thinking level
openclaw agent --session-id 1234 --message "Summarize inbox" --thinking medium

# Deliver to a different channel than the session
openclaw agent --agent ops --message "Alert" --deliver --reply-channel telegram --reply-to "@admin"
```

## [​](https://docs.openclaw.ai/tools/agent-send#related)

Related

*   [Agent CLI reference](https://docs.openclaw.ai/cli/agent)
*   [Sub-agents](https://docs.openclaw.ai/tools/subagents) — background sub-agent spawning
*   [Sessions](https://docs.openclaw.ai/concepts/session) — how session keys work

[Thinking Levels](https://docs.openclaw.ai/tools/thinking)[Sub-Agents](https://docs.openclaw.ai/tools/subagents)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
