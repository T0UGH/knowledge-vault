# automation/auth-monitoring

Source: https://docs.openclaw.ai/automation/auth-monitoring

Title: Auth Monitoring - OpenClaw

URL Source: https://docs.openclaw.ai/automation/auth-monitoring

Markdown Content:
# Auth Monitoring - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/auth-monitoring#content-area)

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

Auth Monitoring

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

*   [Auth monitoring](https://docs.openclaw.ai/automation/auth-monitoring#auth-monitoring)
*   [Preferred: CLI check (portable)](https://docs.openclaw.ai/automation/auth-monitoring#preferred-cli-check-portable)
*   [Optional scripts (ops / phone workflows)](https://docs.openclaw.ai/automation/auth-monitoring#optional-scripts-ops-%2F-phone-workflows)

Automation

# Auth Monitoring

# [​](https://docs.openclaw.ai/automation/auth-monitoring#auth-monitoring)

Auth monitoring

OpenClaw exposes OAuth expiry health via `openclaw models status`. Use that for automation and alerting; scripts are optional extras for phone workflows.
## [​](https://docs.openclaw.ai/automation/auth-monitoring#preferred-cli-check-portable)

Preferred: CLI check (portable)

```
openclaw models status --check
```

Exit codes:
*   `0`: OK
*   `1`: expired or missing credentials
*   `2`: expiring soon (within 24h)

This works in cron/systemd and requires no extra scripts.
## [​](https://docs.openclaw.ai/automation/auth-monitoring#optional-scripts-ops-/-phone-workflows)

Optional scripts (ops / phone workflows)

These live under `scripts/` and are **optional**. They assume SSH access to the gateway host and are tuned for systemd + Termux.
*   `scripts/claude-auth-status.sh` now uses `openclaw models status --json` as the source of truth (falling back to direct file reads if the CLI is unavailable), so keep `openclaw` on `PATH` for timers.
*   `scripts/auth-monitor.sh`: cron/systemd timer target; sends alerts (ntfy or phone).
*   `scripts/systemd/openclaw-auth-monitor.{service,timer}`: systemd user timer.
*   `scripts/claude-auth-status.sh`: Claude Code + OpenClaw auth checker (full/json/simple).
*   `scripts/mobile-reauth.sh`: guided re‑auth flow over SSH.
*   `scripts/termux-quick-auth.sh`: one‑tap widget status + open auth URL.
*   `scripts/termux-auth-widget.sh`: full guided widget flow.
*   `scripts/termux-sync-widget.sh`: sync Claude Code creds → OpenClaw.

If you don’t need phone automation or systemd timers, skip these scripts.

[Polls](https://docs.openclaw.ai/automation/poll)[apply_patch Tool](https://docs.openclaw.ai/tools/apply-patch)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
