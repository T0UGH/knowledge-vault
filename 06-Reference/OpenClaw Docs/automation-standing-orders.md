# automation/standing-orders

Source: https://docs.openclaw.ai/automation/standing-orders

Title: Standing Orders - OpenClaw

URL Source: https://docs.openclaw.ai/automation/standing-orders

Markdown Content:
# Standing Orders - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/standing-orders#content-area)

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

Standing Orders

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

*   [Standing Orders](https://docs.openclaw.ai/automation/standing-orders#standing-orders)
*   [Why Standing Orders?](https://docs.openclaw.ai/automation/standing-orders#why-standing-orders)
*   [How They Work](https://docs.openclaw.ai/automation/standing-orders#how-they-work)
*   [Anatomy of a Standing Order](https://docs.openclaw.ai/automation/standing-orders#anatomy-of-a-standing-order)
*   [Standing Orders + Cron Jobs](https://docs.openclaw.ai/automation/standing-orders#standing-orders-%2B-cron-jobs)
*   [Examples](https://docs.openclaw.ai/automation/standing-orders#examples)
*   [Example 1: Content & Social Media (Weekly Cycle)](https://docs.openclaw.ai/automation/standing-orders#example-1-content-%26-social-media-weekly-cycle)
*   [Example 2: Finance Operations (Event-Triggered)](https://docs.openclaw.ai/automation/standing-orders#example-2-finance-operations-event-triggered)
*   [Example 3: Monitoring & Alerts (Continuous)](https://docs.openclaw.ai/automation/standing-orders#example-3-monitoring-%26-alerts-continuous)
*   [The Execute-Verify-Report Pattern](https://docs.openclaw.ai/automation/standing-orders#the-execute-verify-report-pattern)
*   [Multi-Program Architecture](https://docs.openclaw.ai/automation/standing-orders#multi-program-architecture)
*   [Best Practices](https://docs.openclaw.ai/automation/standing-orders#best-practices)
*   [Do](https://docs.openclaw.ai/automation/standing-orders#do)
*   [Avoid](https://docs.openclaw.ai/automation/standing-orders#avoid)
*   [Related](https://docs.openclaw.ai/automation/standing-orders#related)

Automation

# Standing Orders

# [​](https://docs.openclaw.ai/automation/standing-orders#standing-orders)

Standing Orders

Standing orders grant your agent **permanent operating authority** for defined programs. Instead of giving individual task instructions each time, you define programs with clear scope, triggers, and escalation rules — and the agent executes autonomously within those boundaries.This is the difference between telling your assistant “send the weekly report” every Friday vs. granting standing authority: “You own the weekly report. Compile it every Friday, send it, and only escalate if something looks wrong.”
## [​](https://docs.openclaw.ai/automation/standing-orders#why-standing-orders)

Why Standing Orders?

**Without standing orders:**
*   You must prompt the agent for every task
*   The agent sits idle between requests
*   Routine work gets forgotten or delayed
*   You become the bottleneck

**With standing orders:**
*   The agent executes autonomously within defined boundaries
*   Routine work happens on schedule without prompting
*   You only get involved for exceptions and approvals
*   The agent fills idle time productively

## [​](https://docs.openclaw.ai/automation/standing-orders#how-they-work)

How They Work

Standing orders are defined in your [agent workspace](https://docs.openclaw.ai/concepts/agent-workspace) files. The recommended approach is to include them directly in `AGENTS.md` (which is auto-injected every session) so the agent always has them in context. For larger configurations, you can also place them in a dedicated file like `standing-orders.md` and reference it from `AGENTS.md`.Each program specifies:
1.   **Scope** — what the agent is authorized to do
2.   **Triggers** — when to execute (schedule, event, or condition)
3.   **Approval gates** — what requires human sign-off before acting
4.   **Escalation rules** — when to stop and ask for help

The agent loads these instructions every session via the workspace bootstrap files (see [Agent Workspace](https://docs.openclaw.ai/concepts/agent-workspace) for the full list of auto-injected files) and executes against them, combined with [cron jobs](https://docs.openclaw.ai/automation/cron-jobs) for time-based enforcement.

Put standing orders in `AGENTS.md` to guarantee they’re loaded every session. The workspace bootstrap automatically injects `AGENTS.md`, `SOUL.md`, `TOOLS.md`, `IDENTITY.md`, `USER.md`, `HEARTBEAT.md`, `BOOTSTRAP.md`, and `MEMORY.md` — but not arbitrary files in subdirectories.

## [​](https://docs.openclaw.ai/automation/standing-orders#anatomy-of-a-standing-order)

Anatomy of a Standing Order

```
## Program: Weekly Status Report

**Authority:** Compile data, generate report, deliver to stakeholders
**Trigger:** Every Friday at 4 PM (enforced via cron job)
**Approval gate:** None for standard reports. Flag anomalies for human review.
**Escalation:** If data source is unavailable or metrics look unusual (>2σ from norm)

### Execution Steps

1. Pull metrics from configured sources
2. Compare to prior week and targets
3. Generate report in Reports/weekly/YYYY-MM-DD.md
4. Deliver summary via configured channel
5. Log completion to Agent/Logs/

### What NOT to Do

- Do not send reports to external parties
- Do not modify source data
- Do not skip delivery if metrics look bad — report accurately
```

## [​](https://docs.openclaw.ai/automation/standing-orders#standing-orders-+-cron-jobs)

Standing Orders + Cron Jobs

Standing orders define **what** the agent is authorized to do. [Cron jobs](https://docs.openclaw.ai/automation/cron-jobs) define **when** it happens. They work together:

```
Standing Order: "You own the daily inbox triage"
    ↓
Cron Job (8 AM daily): "Execute inbox triage per standing orders"
    ↓
Agent: Reads standing orders → executes steps → reports results
```

The cron job prompt should reference the standing order rather than duplicating it:

```
openclaw cron add \
  --name daily-inbox-triage \
  --cron "0 8 * * 1-5" \
  --tz America/New_York \
  --timeout-seconds 300 \
  --announce \
  --channel bluebubbles \
  --to "+1XXXXXXXXXX" \
  --message "Execute daily inbox triage per standing orders. Check mail for new alerts. Parse, categorize, and persist each item. Report summary to owner. Escalate unknowns."
```

## [​](https://docs.openclaw.ai/automation/standing-orders#examples)

Examples

### [​](https://docs.openclaw.ai/automation/standing-orders#example-1-content-&-social-media-weekly-cycle)

Example 1: Content & Social Media (Weekly Cycle)

```
## Program: Content & Social Media

**Authority:** Draft content, schedule posts, compile engagement reports
**Approval gate:** All posts require owner review for first 30 days, then standing approval
**Trigger:** Weekly cycle (Monday review → mid-week drafts → Friday brief)

### Weekly Cycle

- **Monday:** Review platform metrics and audience engagement
- **Tuesday–Thursday:** Draft social posts, create blog content
- **Friday:** Compile weekly marketing brief → deliver to owner

### Content Rules

- Voice must match the brand (see SOUL.md or brand voice guide)
- Never identify as AI in public-facing content
- Include metrics when available
- Focus on value to audience, not self-promotion
```

### [​](https://docs.openclaw.ai/automation/standing-orders#example-2-finance-operations-event-triggered)

Example 2: Finance Operations (Event-Triggered)

```
## Program: Financial Processing

**Authority:** Process transaction data, generate reports, send summaries
**Approval gate:** None for analysis. Recommendations require owner approval.
**Trigger:** New data file detected OR scheduled monthly cycle

### When New Data Arrives

1. Detect new file in designated input directory
2. Parse and categorize all transactions
3. Compare against budget targets
4. Flag: unusual items, threshold breaches, new recurring charges
5. Generate report in designated output directory
6. Deliver summary to owner via configured channel

### Escalation Rules

- Single item > $500: immediate alert
- Category > budget by 20%: flag in report
- Unrecognizable transaction: ask owner for categorization
- Failed processing after 2 retries: report failure, do not guess
```

### [​](https://docs.openclaw.ai/automation/standing-orders#example-3-monitoring-&-alerts-continuous)

Example 3: Monitoring & Alerts (Continuous)

```
## Program: System Monitoring

**Authority:** Check system health, restart services, send alerts
**Approval gate:** Restart services automatically. Escalate if restart fails twice.
**Trigger:** Every heartbeat cycle

### Checks

- Service health endpoints responding
- Disk space above threshold
- Pending tasks not stale (>24 hours)
- Delivery channels operational

### Response Matrix

| Condition        | Action                   | Escalate?                |
| ---------------- | ------------------------ | ------------------------ |
| Service down     | Restart automatically    | Only if restart fails 2x |
| Disk space < 10% | Alert owner              | Yes                      |
| Stale task > 24h | Remind owner             | No                       |
| Channel offline  | Log and retry next cycle | If offline > 2 hours     |
```

## [​](https://docs.openclaw.ai/automation/standing-orders#the-execute-verify-report-pattern)

The Execute-Verify-Report Pattern

Standing orders work best when combined with strict execution discipline. Every task in a standing order should follow this loop:
1.   **Execute** — Do the actual work (don’t just acknowledge the instruction)
2.   **Verify** — Confirm the result is correct (file exists, message delivered, data parsed)
3.   **Report** — Tell the owner what was done and what was verified

```
### Execution Rules

- Every task follows Execute-Verify-Report. No exceptions.
- "I'll do that" is not execution. Do it, then report.
- "Done" without verification is not acceptable. Prove it.
- If execution fails: retry once with adjusted approach.
- If still fails: report failure with diagnosis. Never silently fail.
- Never retry indefinitely — 3 attempts max, then escalate.
```

This pattern prevents the most common agent failure mode: acknowledging a task without completing it.
## [​](https://docs.openclaw.ai/automation/standing-orders#multi-program-architecture)

Multi-Program Architecture

For agents managing multiple concerns, organize standing orders as separate programs with clear boundaries:

```
# Standing Orders

## Program 1: [Domain A] (Weekly)

...

## Program 2: [Domain B] (Monthly + On-Demand)

...

## Program 3: [Domain C] (As-Needed)

...

## Escalation Rules (All Programs)

- [Common escalation criteria]
- [Approval gates that apply across programs]
```

Each program should have:
*   Its own **trigger cadence** (weekly, monthly, event-driven, continuous)
*   Its own **approval gates** (some programs need more oversight than others)
*   Clear **boundaries** (the agent should know where one program ends and another begins)

## [​](https://docs.openclaw.ai/automation/standing-orders#best-practices)

Best Practices

### [​](https://docs.openclaw.ai/automation/standing-orders#do)

Do

*   Start with narrow authority and expand as trust builds
*   Define explicit approval gates for high-risk actions
*   Include “What NOT to do” sections — boundaries matter as much as permissions
*   Combine with cron jobs for reliable time-based execution
*   Review agent logs weekly to verify standing orders are being followed
*   Update standing orders as your needs evolve — they’re living documents

### [​](https://docs.openclaw.ai/automation/standing-orders#avoid)

Avoid

*   Grant broad authority on day one (“do whatever you think is best”)
*   Skip escalation rules — every program needs a “when to stop and ask” clause
*   Assume the agent will remember verbal instructions — put everything in the file
*   Mix concerns in a single program — separate programs for separate domains
*   Forget to enforce with cron jobs — standing orders without triggers become suggestions

## [​](https://docs.openclaw.ai/automation/standing-orders#related)

Related

*   [Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs) — Schedule enforcement for standing orders
*   [Agent Workspace](https://docs.openclaw.ai/concepts/agent-workspace) — Where standing orders live, including the full list of auto-injected bootstrap files (AGENTS.md, SOUL.md, etc.)

[Hooks](https://docs.openclaw.ai/automation/hooks)[Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
