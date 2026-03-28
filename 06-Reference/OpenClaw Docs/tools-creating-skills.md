# tools/creating-skills

Source: https://docs.openclaw.ai/tools/creating-skills

Title: Creating Skills - OpenClaw

URL Source: https://docs.openclaw.ai/tools/creating-skills

Markdown Content:
# Creating Skills - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/creating-skills#content-area)

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

Creating Skills

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

*   [Creating Skills](https://docs.openclaw.ai/tools/creating-skills#creating-skills)
*   [Create your first skill](https://docs.openclaw.ai/tools/creating-skills#create-your-first-skill)
*   [Skill metadata reference](https://docs.openclaw.ai/tools/creating-skills#skill-metadata-reference)
*   [Best practices](https://docs.openclaw.ai/tools/creating-skills#best-practices)
*   [Where skills live](https://docs.openclaw.ai/tools/creating-skills#where-skills-live)
*   [Related](https://docs.openclaw.ai/tools/creating-skills#related)

Skills

# Creating Skills

# [​](https://docs.openclaw.ai/tools/creating-skills#creating-skills)

Creating Skills

Skills teach the agent how and when to use tools. Each skill is a directory containing a `SKILL.md` file with YAML frontmatter and markdown instructions.For how skills are loaded and prioritized, see [Skills](https://docs.openclaw.ai/tools/skills).
## [​](https://docs.openclaw.ai/tools/creating-skills#create-your-first-skill)

Create your first skill

1

[](https://docs.openclaw.ai/tools/creating-skills#)

Create the skill directory

Skills live in your workspace. Create a new folder:

```
mkdir -p ~/.openclaw/workspace/skills/hello-world
```

2

[](https://docs.openclaw.ai/tools/creating-skills#)

Write SKILL.md

Create `SKILL.md` inside that directory. The frontmatter defines metadata, and the markdown body contains instructions for the agent.

```
---
name: hello_world
description: A simple skill that says hello.
---

# Hello World Skill

When the user asks for a greeting, use the `echo` tool to say
"Hello from your custom skill!".
```

3

[](https://docs.openclaw.ai/tools/creating-skills#)

Add tools (optional)

You can define custom tool schemas in the frontmatter or instruct the agent to use existing system tools (like `exec` or `browser`). Skills can also ship inside plugins alongside the tools they document.

4

[](https://docs.openclaw.ai/tools/creating-skills#)

Load the skill

Start a new session so OpenClaw picks up the skill:

```
# From chat
/new

# Or restart the gateway
openclaw gateway restart
```

Verify the skill loaded:

```
openclaw skills list
```

5

[](https://docs.openclaw.ai/tools/creating-skills#)

Test it

Send a message that should trigger the skill:

```
openclaw agent --message "give me a greeting"
```

Or just chat with the agent and ask for a greeting.

## [​](https://docs.openclaw.ai/tools/creating-skills#skill-metadata-reference)

Skill metadata reference

The YAML frontmatter supports these fields:

| Field | Required | Description |
| --- | --- | --- |
| `name` | Yes | Unique identifier (snake_case) |
| `description` | Yes | One-line description shown to the agent |
| `metadata.openclaw.os` | No | OS filter (`["darwin"]`, `["linux"]`, etc.) |
| `metadata.openclaw.requires.bins` | No | Required binaries on PATH |
| `metadata.openclaw.requires.config` | No | Required config keys |

## [​](https://docs.openclaw.ai/tools/creating-skills#best-practices)

Best practices

*   **Be concise** — instruct the model on _what_ to do, not how to be an AI
*   **Safety first** — if your skill uses `exec`, ensure prompts don’t allow arbitrary command injection from untrusted input
*   **Test locally** — use `openclaw agent --message "..."` to test before sharing
*   **Use ClawHub** — browse and contribute skills at [ClawHub](https://clawhub.com/)

## [​](https://docs.openclaw.ai/tools/creating-skills#where-skills-live)

Where skills live

| Location | Precedence | Scope |
| --- | --- | --- |
| `\<workspace\>/skills/` | Highest | Per-agent |
| `~/.openclaw/skills/` | Medium | Shared (all agents) |
| Bundled (shipped with OpenClaw) | Lowest | Global |
| `skills.load.extraDirs` | Lowest | Custom shared folders |

## [​](https://docs.openclaw.ai/tools/creating-skills#related)

Related

*   [Skills reference](https://docs.openclaw.ai/tools/skills) — loading, precedence, and gating rules
*   [Skills config](https://docs.openclaw.ai/tools/skills-config) — `skills.*` config schema
*   [ClawHub](https://docs.openclaw.ai/tools/clawhub) — public skill registry
*   [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins) — plugins can ship skills

[Skills](https://docs.openclaw.ai/tools/skills)[Skills Config](https://docs.openclaw.ai/tools/skills-config)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
