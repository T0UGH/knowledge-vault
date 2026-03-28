# tools/elevated

Source: https://docs.openclaw.ai/tools/elevated

Title: Elevated Mode - OpenClaw

URL Source: https://docs.openclaw.ai/tools/elevated

Markdown Content:
# Elevated Mode - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/elevated#content-area)

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

Elevated Mode

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

*   [Elevated Mode](https://docs.openclaw.ai/tools/elevated#elevated-mode)
*   [Directives](https://docs.openclaw.ai/tools/elevated#directives)
*   [How it works](https://docs.openclaw.ai/tools/elevated#how-it-works)
*   [Resolution order](https://docs.openclaw.ai/tools/elevated#resolution-order)
*   [Availability and allowlists](https://docs.openclaw.ai/tools/elevated#availability-and-allowlists)
*   [What elevated does not control](https://docs.openclaw.ai/tools/elevated#what-elevated-does-not-control)
*   [Related](https://docs.openclaw.ai/tools/elevated#related)

Tools

# Elevated Mode

# [​](https://docs.openclaw.ai/tools/elevated#elevated-mode)

Elevated Mode

When an agent runs inside a sandbox, its `exec` commands are confined to the sandbox environment. **Elevated mode** lets the agent break out and run commands on the gateway host instead, with configurable approval gates.

Elevated mode only changes behavior when the agent is **sandboxed**. For unsandboxed agents, exec already runs on the host.

## [​](https://docs.openclaw.ai/tools/elevated#directives)

Directives

Control elevated mode per-session with slash commands:

| Directive | What it does |
| --- | --- |
| `/elevated on` | Run on the gateway host, keep exec approvals |
| `/elevated ask` | Same as `on` (alias) |
| `/elevated full` | Run on the gateway host **and** skip exec approvals |
| `/elevated off` | Return to sandbox-confined execution |

Also available as `/elev on|off|ask|full`.Send `/elevated` with no argument to see the current level.
## [​](https://docs.openclaw.ai/tools/elevated#how-it-works)

How it works

1

[](https://docs.openclaw.ai/tools/elevated#)

Check availability

Elevated must be enabled in config and the sender must be on the allowlist:

```
{
  tools: {
    elevated: {
      enabled: true,
      allowFrom: {
        discord: ["user-id-123"],
        whatsapp: ["+15555550123"],
      },
    },
  },
}
```

2

[](https://docs.openclaw.ai/tools/elevated#)

Set the level

Send a directive-only message to set the session default:

```
/elevated full
```

Or use it inline (applies to that message only):

```
/elevated on run the deployment script
```

3

[](https://docs.openclaw.ai/tools/elevated#)

Commands run on the host

With elevated active, `exec` calls route to the gateway host instead of the sandbox. In `full` mode, exec approvals are skipped. In `on`/`ask` mode, configured approval rules still apply.

## [​](https://docs.openclaw.ai/tools/elevated#resolution-order)

Resolution order

1.   **Inline directive** on the message (applies only to that message)
2.   **Session override** (set by sending a directive-only message)
3.   **Global default** (`agents.defaults.elevatedDefault` in config)

## [​](https://docs.openclaw.ai/tools/elevated#availability-and-allowlists)

Availability and allowlists

*   **Global gate**: `tools.elevated.enabled` (must be `true`)
*   **Sender allowlist**: `tools.elevated.allowFrom` with per-channel lists
*   **Per-agent gate**: `agents.list[].tools.elevated.enabled` (can only further restrict)
*   **Per-agent allowlist**: `agents.list[].tools.elevated.allowFrom` (sender must match both global + per-agent)
*   **Discord fallback**: if `tools.elevated.allowFrom.discord` is omitted, `channels.discord.allowFrom` is used as fallback
*   **All gates must pass**; otherwise elevated is treated as unavailable

Allowlist entry formats:

| Prefix | Matches |
| --- | --- |
| (none) | Sender ID, E.164, or From field |
| `name:` | Sender display name |
| `username:` | Sender username |
| `tag:` | Sender tag |
| `id:`, `from:`, `e164:` | Explicit identity targeting |

## [​](https://docs.openclaw.ai/tools/elevated#what-elevated-does-not-control)

What elevated does not control

*   **Tool policy**: if `exec` is denied by tool policy, elevated cannot override it
*   **Separate from `/exec`**: the `/exec` directive adjusts per-session exec defaults for authorized senders and does not require elevated mode

## [​](https://docs.openclaw.ai/tools/elevated#related)

Related

*   [Exec tool](https://docs.openclaw.ai/tools/exec) — shell command execution
*   [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals) — approval and allowlist system
*   [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing) — sandbox configuration
*   [Sandbox vs Tool Policy vs Elevated](https://docs.openclaw.ai/gateway/sandbox-vs-tool-policy-vs-elevated)

[Diffs](https://docs.openclaw.ai/tools/diffs)[Exec Tool](https://docs.openclaw.ai/tools/exec)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
