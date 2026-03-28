# automation/troubleshooting

Source: https://docs.openclaw.ai/automation/troubleshooting

Title: Automation Troubleshooting - OpenClaw

URL Source: https://docs.openclaw.ai/automation/troubleshooting

Markdown Content:
# Automation Troubleshooting - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/troubleshooting#content-area)

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

Automation Troubleshooting

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

*   [Automation troubleshooting](https://docs.openclaw.ai/automation/troubleshooting#automation-troubleshooting)
*   [Command ladder](https://docs.openclaw.ai/automation/troubleshooting#command-ladder)
*   [Cron not firing](https://docs.openclaw.ai/automation/troubleshooting#cron-not-firing)
*   [Cron fired but no delivery](https://docs.openclaw.ai/automation/troubleshooting#cron-fired-but-no-delivery)
*   [Heartbeat suppressed or skipped](https://docs.openclaw.ai/automation/troubleshooting#heartbeat-suppressed-or-skipped)
*   [Timezone and activeHours gotchas](https://docs.openclaw.ai/automation/troubleshooting#timezone-and-activehours-gotchas)

Automation

# Automation Troubleshooting

# [​](https://docs.openclaw.ai/automation/troubleshooting#automation-troubleshooting)

Automation troubleshooting

Use this page for scheduler and delivery issues (`cron` + `heartbeat`).
## [​](https://docs.openclaw.ai/automation/troubleshooting#command-ladder)

Command ladder

```
openclaw status
openclaw gateway status
openclaw logs --follow
openclaw doctor
openclaw channels status --probe
```

Then run automation checks:

```
openclaw cron status
openclaw cron list
openclaw system heartbeat last
```

## [​](https://docs.openclaw.ai/automation/troubleshooting#cron-not-firing)

Cron not firing

```
openclaw cron status
openclaw cron list
openclaw cron runs --id <jobId> --limit 20
openclaw logs --follow
```

Good output looks like:
*   `cron status` reports enabled and a future `nextWakeAtMs`.
*   Job is enabled and has a valid schedule/timezone.
*   `cron runs` shows `ok` or explicit skip reason.

Common signatures:
*   `cron: scheduler disabled; jobs will not run automatically` → cron disabled in config/env.
*   `cron: timer tick failed` → scheduler tick crashed; inspect surrounding stack/log context.
*   `reason: not-due` in run output → manual run called without `--force` and job not due yet.

## [​](https://docs.openclaw.ai/automation/troubleshooting#cron-fired-but-no-delivery)

Cron fired but no delivery

```
openclaw cron runs --id <jobId> --limit 20
openclaw cron list
openclaw channels status --probe
openclaw logs --follow
```

Good output looks like:
*   Run status is `ok`.
*   Delivery mode/target are set for isolated jobs.
*   Channel probe reports target channel connected.

Common signatures:
*   Run succeeded but delivery mode is `none` → no external message is expected.
*   Delivery target missing/invalid (`channel`/`to`) → run may succeed internally but skip outbound.
*   Channel auth errors (`unauthorized`, `missing_scope`, `Forbidden`) → delivery blocked by channel credentials/permissions.

## [​](https://docs.openclaw.ai/automation/troubleshooting#heartbeat-suppressed-or-skipped)

Heartbeat suppressed or skipped

```
openclaw system heartbeat last
openclaw logs --follow
openclaw config get agents.defaults.heartbeat
openclaw channels status --probe
```

Good output looks like:
*   Heartbeat enabled with non-zero interval.
*   Last heartbeat result is `ran` (or skip reason is understood).

Common signatures:
*   `heartbeat skipped` with `reason=quiet-hours` → outside `activeHours`.
*   `requests-in-flight` → main lane busy; heartbeat deferred.
*   `empty-heartbeat-file` → interval heartbeat skipped because `HEARTBEAT.md` has no actionable content and no tagged cron event is queued.
*   `alerts-disabled` → visibility settings suppress outbound heartbeat messages.

## [​](https://docs.openclaw.ai/automation/troubleshooting#timezone-and-activehours-gotchas)

Timezone and activeHours gotchas

```
openclaw config get agents.defaults.heartbeat.activeHours
openclaw config get agents.defaults.heartbeat.activeHours.timezone
openclaw config get agents.defaults.userTimezone || echo "agents.defaults.userTimezone not set"
openclaw cron list
openclaw logs --follow
```

Quick rules:
*   `Config path not found: agents.defaults.userTimezone` means the key is unset; heartbeat falls back to host timezone (or `activeHours.timezone` if set).
*   Cron without `--tz` uses gateway host timezone.
*   Heartbeat `activeHours` uses configured timezone resolution (`user`, `local`, or explicit IANA tz).
*   Cron `at` schedules treat ISO timestamps without timezone as UTC unless you used CLI `--at "<offset-less-iso>" --tz <iana>`.

Common signatures:
*   Jobs run at the wrong wall-clock time after host timezone changes.
*   Heartbeat always skipped during your daytime because `activeHours.timezone` is wrong.

Related:
*   [/automation/cron-jobs](https://docs.openclaw.ai/automation/cron-jobs)
*   [/gateway/heartbeat](https://docs.openclaw.ai/gateway/heartbeat)
*   [/automation/cron-vs-heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)
*   [/concepts/timezone](https://docs.openclaw.ai/concepts/timezone)

[Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)[Webhooks](https://docs.openclaw.ai/automation/webhook)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
