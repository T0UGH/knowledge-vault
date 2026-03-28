# automation/poll

Source: https://docs.openclaw.ai/automation/poll

Title: Polls - OpenClaw

URL Source: https://docs.openclaw.ai/automation/poll

Markdown Content:
# Polls - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/poll#content-area)

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

Automation

Polls

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

*   [Polls](https://docs.openclaw.ai/automation/poll#polls)
*   [Supported channels](https://docs.openclaw.ai/automation/poll#supported-channels)
*   [CLI](https://docs.openclaw.ai/automation/poll#cli)
*   [Gateway RPC](https://docs.openclaw.ai/automation/poll#gateway-rpc)
*   [Channel differences](https://docs.openclaw.ai/automation/poll#channel-differences)
*   [Agent tool (Message)](https://docs.openclaw.ai/automation/poll#agent-tool-message)

Automation

# Polls

# [​](https://docs.openclaw.ai/automation/poll#polls)

Polls

## [​](https://docs.openclaw.ai/automation/poll#supported-channels)

Supported channels

*   Telegram
*   WhatsApp (web channel)
*   Discord
*   Microsoft Teams (Adaptive Cards)

## [​](https://docs.openclaw.ai/automation/poll#cli)

CLI

```
# Telegram
openclaw message poll --channel telegram --target 123456789 \
  --poll-question "Ship it?" --poll-option "Yes" --poll-option "No"
openclaw message poll --channel telegram --target -1001234567890:topic:42 \
  --poll-question "Pick a time" --poll-option "10am" --poll-option "2pm" \
  --poll-duration-seconds 300

# WhatsApp
openclaw message poll --target +15555550123 \
  --poll-question "Lunch today?" --poll-option "Yes" --poll-option "No" --poll-option "Maybe"
openclaw message poll --target 123456789@g.us \
  --poll-question "Meeting time?" --poll-option "10am" --poll-option "2pm" --poll-option "4pm" --poll-multi

# Discord
openclaw message poll --channel discord --target channel:123456789 \
  --poll-question "Snack?" --poll-option "Pizza" --poll-option "Sushi"
openclaw message poll --channel discord --target channel:123456789 \
  --poll-question "Plan?" --poll-option "A" --poll-option "B" --poll-duration-hours 48

# Microsoft Teams
openclaw message poll --channel msteams --target conversation:19:abc@thread.tacv2 \
  --poll-question "Lunch?" --poll-option "Pizza" --poll-option "Sushi"
```

Options:
*   `--channel`: `whatsapp` (default), `telegram`, `discord`, or `msteams`
*   `--poll-multi`: allow selecting multiple options
*   `--poll-duration-hours`: Discord-only (defaults to 24 when omitted)
*   `--poll-duration-seconds`: Telegram-only (5-600 seconds)
*   `--poll-anonymous` / `--poll-public`: Telegram-only poll visibility

## [​](https://docs.openclaw.ai/automation/poll#gateway-rpc)

Gateway RPC

Method: `poll`Params:
*   `to` (string, required)
*   `question` (string, required)
*   `options` (string[], required)
*   `maxSelections` (number, optional)
*   `durationHours` (number, optional)
*   `durationSeconds` (number, optional, Telegram-only)
*   `isAnonymous` (boolean, optional, Telegram-only)
*   `channel` (string, optional, default: `whatsapp`)
*   `idempotencyKey` (string, required)

## [​](https://docs.openclaw.ai/automation/poll#channel-differences)

Channel differences

*   Telegram: 2-10 options. Supports forum topics via `threadId` or `:topic:` targets. Uses `durationSeconds` instead of `durationHours`, limited to 5-600 seconds. Supports anonymous and public polls.
*   WhatsApp: 2-12 options, `maxSelections` must be within option count, ignores `durationHours`.
*   Discord: 2-10 options, `durationHours` clamped to 1-768 hours (default 24). `maxSelections > 1` enables multi-select; Discord does not support a strict selection count.
*   Microsoft Teams: Adaptive Card polls (OpenClaw-managed). No native poll API; `durationHours` is ignored.

## [​](https://docs.openclaw.ai/automation/poll#agent-tool-message)

Agent tool (Message)

Use the `message` tool with `poll` action (`to`, `pollQuestion`, `pollOption`, optional `pollMulti`, `pollDurationHours`, `channel`).For Telegram, the tool also accepts `pollDurationSeconds`, `pollAnonymous`, and `pollPublic`.Use `action: "poll"` for poll creation. Poll fields passed with `action: "send"` are rejected.Note: Discord has no “pick exactly N” mode; `pollMulti` maps to multi-select. Teams polls are rendered as Adaptive Cards and require the gateway to stay online to record votes in `~/.openclaw/msteams-polls.json`.

[Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub)[Auth Monitoring](https://docs.openclaw.ai/automation/auth-monitoring)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
