# plugins/community

Source: https://docs.openclaw.ai/plugins/community

Title: Community Plugins - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/community

Markdown Content:
# Community Plugins - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/community#content-area)

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

Plugins

Community Plugins

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

*   [Community Plugins](https://docs.openclaw.ai/plugins/community#community-plugins)
*   [Listed plugins](https://docs.openclaw.ai/plugins/community#listed-plugins)
*   [Codex App Server Bridge](https://docs.openclaw.ai/plugins/community#codex-app-server-bridge)
*   [DingTalk](https://docs.openclaw.ai/plugins/community#dingtalk)
*   [Lossless Claw (LCM)](https://docs.openclaw.ai/plugins/community#lossless-claw-lcm)
*   [Opik](https://docs.openclaw.ai/plugins/community#opik)
*   [QQbot](https://docs.openclaw.ai/plugins/community#qqbot)
*   [wecom](https://docs.openclaw.ai/plugins/community#wecom)
*   [Submit your plugin](https://docs.openclaw.ai/plugins/community#submit-your-plugin)
*   [Quality bar](https://docs.openclaw.ai/plugins/community#quality-bar)
*   [Related](https://docs.openclaw.ai/plugins/community#related)

Plugins

# Community Plugins

# [​](https://docs.openclaw.ai/plugins/community#community-plugins)

Community Plugins

Community plugins are third-party packages that extend OpenClaw with new channels, tools, providers, or other capabilities. They are built and maintained by the community, published on [ClawHub](https://docs.openclaw.ai/tools/clawhub) or npm, and installable with a single command.

```
openclaw plugins install <package-name>
```

OpenClaw checks ClawHub first and falls back to npm automatically.
## [​](https://docs.openclaw.ai/plugins/community#listed-plugins)

Listed plugins

### [​](https://docs.openclaw.ai/plugins/community#codex-app-server-bridge)

Codex App Server Bridge

Independent OpenClaw bridge for Codex App Server conversations. Bind a chat to a Codex thread, talk to it with plain text, and control it with chat-native commands for resume, planning, review, model selection, compaction, and more.
*   **npm:**`openclaw-codex-app-server`
*   **repo:**[github.com/pwrdrvr/openclaw-codex-app-server](https://github.com/pwrdrvr/openclaw-codex-app-server)

```
openclaw plugins install openclaw-codex-app-server
```

### [​](https://docs.openclaw.ai/plugins/community#dingtalk)

DingTalk

Enterprise robot integration using Stream mode. Supports text, images, and file messages via any DingTalk client.
*   **npm:**`@largezhou/ddingtalk`
*   **repo:**[github.com/largezhou/openclaw-dingtalk](https://github.com/largezhou/openclaw-dingtalk)

```
openclaw plugins install @largezhou/ddingtalk
```

### [​](https://docs.openclaw.ai/plugins/community#lossless-claw-lcm)

Lossless Claw (LCM)

Lossless Context Management plugin for OpenClaw. DAG-based conversation summarization with incremental compaction — preserves full context fidelity while reducing token usage.
*   **npm:**`@martian-engineering/lossless-claw`
*   **repo:**[github.com/Martian-Engineering/lossless-claw](https://github.com/Martian-Engineering/lossless-claw)

```
openclaw plugins install @martian-engineering/lossless-claw
```

### [​](https://docs.openclaw.ai/plugins/community#opik)

Opik

Official plugin that exports agent traces to Opik. Monitor agent behavior, cost, tokens, errors, and more.
*   **npm:**`@opik/opik-openclaw`
*   **repo:**[github.com/comet-ml/opik-openclaw](https://github.com/comet-ml/opik-openclaw)

```
openclaw plugins install @opik/opik-openclaw
```

### [​](https://docs.openclaw.ai/plugins/community#qqbot)

QQbot

Connect OpenClaw to QQ via the QQ Bot API. Supports private chats, group mentions, channel messages, and rich media including voice, images, videos, and files.
*   **npm:**`@sliverp/qqbot`
*   **repo:**[github.com/sliverp/qqbot](https://github.com/sliverp/qqbot)

```
openclaw plugins install @sliverp/qqbot
```

### [​](https://docs.openclaw.ai/plugins/community#wecom)

wecom

OpenClaw Enterprise WeCom Channel Plugin. A bot plugin powered by WeCom AI Bot WebSocket persistent connections, supports direct messages & group chats, streaming replies, and proactive messaging.
*   **npm:**`@wecom/wecom-openclaw-plugin`
*   **repo:**[github.com/WecomTeam/wecom-openclaw-plugin](https://github.com/WecomTeam/wecom-openclaw-plugin)

```
openclaw plugins install @wecom/wecom-openclaw-plugin
```

## [​](https://docs.openclaw.ai/plugins/community#submit-your-plugin)

Submit your plugin

We welcome community plugins that are useful, documented, and safe to operate.

1

[](https://docs.openclaw.ai/plugins/community#)

Publish to ClawHub or npm

Your plugin must be installable via `openclaw plugins install \<package-name\>`. Publish to [ClawHub](https://docs.openclaw.ai/tools/clawhub) (preferred) or npm. See [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins) for the full guide.

2

[](https://docs.openclaw.ai/plugins/community#)

Host on GitHub

Source code must be in a public repository with setup docs and an issue tracker.

3

[](https://docs.openclaw.ai/plugins/community#)

Open a PR

Add your plugin to this page with:
*   Plugin name
*   npm package name
*   GitHub repository URL
*   One-line description
*   Install command

## [​](https://docs.openclaw.ai/plugins/community#quality-bar)

Quality bar

| Requirement | Why |
| --- | --- |
| Published on ClawHub or npm | Users need `openclaw plugins install` to work |
| Public GitHub repo | Source review, issue tracking, transparency |
| Setup and usage docs | Users need to know how to configure it |
| Active maintenance | Recent updates or responsive issue handling |

Low-effort wrappers, unclear ownership, or unmaintained packages may be declined.
## [​](https://docs.openclaw.ai/plugins/community#related)

Related

*   [Install and Configure Plugins](https://docs.openclaw.ai/tools/plugin) — how to install any plugin
*   [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins) — create your own
*   [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest) — manifest schema

[Install and Configure](https://docs.openclaw.ai/tools/plugin)[Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
