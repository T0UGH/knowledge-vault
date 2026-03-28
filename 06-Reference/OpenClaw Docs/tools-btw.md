# tools/btw

Source: https://docs.openclaw.ai/tools/btw

Title: BTW Side Questions - OpenClaw

URL Source: https://docs.openclaw.ai/tools/btw

Markdown Content:
# BTW Side Questions - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/btw#content-area)

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

Tools

BTW Side Questions

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

*   [BTW Side Questions](https://docs.openclaw.ai/tools/btw#btw-side-questions)
*   [What it does](https://docs.openclaw.ai/tools/btw#what-it-does)
*   [What it does not do](https://docs.openclaw.ai/tools/btw#what-it-does-not-do)
*   [How context works](https://docs.openclaw.ai/tools/btw#how-context-works)
*   [Delivery model](https://docs.openclaw.ai/tools/btw#delivery-model)
*   [Surface behavior](https://docs.openclaw.ai/tools/btw#surface-behavior)
*   [TUI](https://docs.openclaw.ai/tools/btw#tui)
*   [External channels](https://docs.openclaw.ai/tools/btw#external-channels)
*   [Control UI / web](https://docs.openclaw.ai/tools/btw#control-ui-%2F-web)
*   [When to use BTW](https://docs.openclaw.ai/tools/btw#when-to-use-btw)
*   [When not to use BTW](https://docs.openclaw.ai/tools/btw#when-not-to-use-btw)
*   [Related](https://docs.openclaw.ai/tools/btw#related)

Tools

# BTW Side Questions

# [​](https://docs.openclaw.ai/tools/btw#btw-side-questions)

BTW Side Questions

`/btw` lets you ask a quick side question about the **current session** without turning that question into normal conversation history.It is modeled after Claude Code’s `/btw` behavior, but adapted to OpenClaw’s Gateway and multi-channel architecture.
## [​](https://docs.openclaw.ai/tools/btw#what-it-does)

What it does

When you send:

```
/btw what changed?
```

OpenClaw:
1.   snapshots the current session context,
2.   runs a separate **tool-less** model call,
3.   answers only the side question,
4.   leaves the main run alone,
5.   does **not** write the BTW question or answer to session history,
6.   emits the answer as a **live side result** rather than a normal assistant message.

The important mental model is:
*   same session context
*   separate one-shot side query
*   no tool calls
*   no future context pollution
*   no transcript persistence

## [​](https://docs.openclaw.ai/tools/btw#what-it-does-not-do)

What it does not do

`/btw` does **not**:
*   create a new durable session,
*   continue the unfinished main task,
*   run tools or agent tool loops,
*   write BTW question/answer data to transcript history,
*   appear in `chat.history`,
*   survive a reload.

It is intentionally **ephemeral**.
## [​](https://docs.openclaw.ai/tools/btw#how-context-works)

How context works

BTW uses the current session as **background context only**.If the main run is currently active, OpenClaw snapshots the current message state and includes the in-flight main prompt as background context, while explicitly telling the model:
*   answer only the side question,
*   do not resume or complete the unfinished main task,
*   do not emit tool calls or pseudo-tool calls.

That keeps BTW isolated from the main run while still making it aware of what the session is about.
## [​](https://docs.openclaw.ai/tools/btw#delivery-model)

Delivery model

BTW is **not** delivered as a normal assistant transcript message.At the Gateway protocol level:
*   normal assistant chat uses the `chat` event
*   BTW uses the `chat.side_result` event

This separation is intentional. If BTW reused the normal `chat` event path, clients would treat it like regular conversation history.Because BTW uses a separate live event and is not replayed from `chat.history`, it disappears after reload.
## [​](https://docs.openclaw.ai/tools/btw#surface-behavior)

Surface behavior

### [​](https://docs.openclaw.ai/tools/btw#tui)

TUI

In TUI, BTW is rendered inline in the current session view, but it remains ephemeral:
*   visibly distinct from a normal assistant reply
*   dismissible with `Enter` or `Esc`
*   not replayed on reload

### [​](https://docs.openclaw.ai/tools/btw#external-channels)

External channels

On channels like Telegram, WhatsApp, and Discord, BTW is delivered as a clearly labeled one-off reply because those surfaces do not have a local ephemeral overlay concept.The answer is still treated as a side result, not normal session history.
### [​](https://docs.openclaw.ai/tools/btw#control-ui-/-web)

Control UI / web

The Gateway emits BTW correctly as `chat.side_result`, and BTW is not included in `chat.history`, so the persistence contract is already correct for web.The current Control UI still needs a dedicated `chat.side_result` consumer to render BTW live in the browser. Until that client-side support lands, BTW is a Gateway-level feature with full TUI and external-channel behavior, but not yet a complete browser UX.
## [​](https://docs.openclaw.ai/tools/btw#when-to-use-btw)

When to use BTW

Use `/btw` when you want:
*   a quick clarification about the current work,
*   a factual side answer while a long run is still in progress,
*   a temporary answer that should not become part of future session context.

Examples:

```
/btw what file are we editing?
/btw what does this error mean?
/btw summarize the current task in one sentence
/btw what is 17 * 19?
```

## [​](https://docs.openclaw.ai/tools/btw#when-not-to-use-btw)

When not to use BTW

Do not use `/btw` when you want the answer to become part of the session’s future working context.In that case, ask normally in the main session instead of using BTW.
## [​](https://docs.openclaw.ai/tools/btw#related)

Related

*   [Slash commands](https://docs.openclaw.ai/tools/slash-commands)
*   [Thinking Levels](https://docs.openclaw.ai/tools/thinking)
*   [Session](https://docs.openclaw.ai/concepts/session)

[Tavily](https://docs.openclaw.ai/tools/tavily)[Diffs](https://docs.openclaw.ai/tools/diffs)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
