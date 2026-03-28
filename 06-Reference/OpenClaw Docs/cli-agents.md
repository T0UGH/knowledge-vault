# cli/agents

Source: https://docs.openclaw.ai/cli/agents

Title: agents - OpenClaw

URL Source: https://docs.openclaw.ai/cli/agents

Markdown Content:
# agents - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/agents#content-area)

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

Agents and sessions

agents

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
    *   [agent](https://docs.openclaw.ai/cli/agent)
    *   [agents](https://docs.openclaw.ai/cli/agents)
    *   [hooks](https://docs.openclaw.ai/cli/hooks)
    *   [memory](https://docs.openclaw.ai/cli/memory)
    *   [message](https://docs.openclaw.ai/cli/message)
    *   [models](https://docs.openclaw.ai/cli/models)
    *   [sessions](https://docs.openclaw.ai/cli/sessions)
    *   [system](https://docs.openclaw.ai/cli/system)

*   Channels and messaging 
*   Tools and execution 
*   Configuration 
*   Plugins and skills 
*   Interfaces 
*   Utility 

##### RPC and API

*   [RPC Adapters](https://docs.openclaw.ai/reference/rpc)
*   [Device Model Database](https://docs.openclaw.ai/reference/device-models)

##### Templates

*   [Default AGENTS.md](https://docs.openclaw.ai/reference/AGENTS.default)
*   [AGENTS.md Template](https://docs.openclaw.ai/reference/templates/AGENTS)
*   [BOOT.md Template](https://docs.openclaw.ai/reference/templates/BOOT)
*   [BOOTSTRAP.md Template](https://docs.openclaw.ai/reference/templates/BOOTSTRAP)
*   [HEARTBEAT.md Template](https://docs.openclaw.ai/reference/templates/HEARTBEAT)
*   [IDENTITY Template](https://docs.openclaw.ai/reference/templates/IDENTITY)
*   [SOUL.md Template](https://docs.openclaw.ai/reference/templates/SOUL)
*   [TOOLS.md Template](https://docs.openclaw.ai/reference/templates/TOOLS)
*   [USER Template](https://docs.openclaw.ai/reference/templates/USER)

##### Technical reference

*   [Pi Integration Architecture](https://docs.openclaw.ai/pi)
*   [Onboarding Reference](https://docs.openclaw.ai/reference/wizard)
*   [Token Use and Costs](https://docs.openclaw.ai/reference/token-use)
*   [SecretRef Credential Surface](https://docs.openclaw.ai/reference/secretref-credential-surface)
*   [Prompt Caching](https://docs.openclaw.ai/reference/prompt-caching)
*   [API Usage and Costs](https://docs.openclaw.ai/reference/api-usage-costs)
*   [Transcript Hygiene](https://docs.openclaw.ai/reference/transcript-hygiene)
*   [Memory configuration reference](https://docs.openclaw.ai/reference/memory-config)
*   [Date and Time](https://docs.openclaw.ai/date-time)

##### Concept internals

*   [TypeBox](https://docs.openclaw.ai/concepts/typebox)
*   [Markdown Formatting](https://docs.openclaw.ai/concepts/markdown-formatting)
*   [Typing Indicators](https://docs.openclaw.ai/concepts/typing-indicators)
*   [Usage Tracking](https://docs.openclaw.ai/concepts/usage-tracking)
*   [Timezones](https://docs.openclaw.ai/concepts/timezone)

##### Project

*   [Credits](https://docs.openclaw.ai/reference/credits)

##### Release policy

*   [Release Policy](https://docs.openclaw.ai/reference/RELEASING)
*   [Tests](https://docs.openclaw.ai/reference/test)

On this page

*   [openclaw agents](https://docs.openclaw.ai/cli/agents#openclaw-agents)
*   [Examples](https://docs.openclaw.ai/cli/agents#examples)
*   [Routing bindings](https://docs.openclaw.ai/cli/agents#routing-bindings)
*   [Binding scope behavior](https://docs.openclaw.ai/cli/agents#binding-scope-behavior)
*   [Identity files](https://docs.openclaw.ai/cli/agents#identity-files)
*   [Set identity](https://docs.openclaw.ai/cli/agents#set-identity)

Agents and sessions

# agents

# [​](https://docs.openclaw.ai/cli/agents#openclaw-agents)

`openclaw agents`

Manage isolated agents (workspaces + auth + routing).Related:
*   Multi-agent routing: [Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent)
*   Agent workspace: [Agent workspace](https://docs.openclaw.ai/concepts/agent-workspace)

## [​](https://docs.openclaw.ai/cli/agents#examples)

Examples

```
openclaw agents list
openclaw agents add work --workspace ~/.openclaw/workspace-work
openclaw agents bindings
openclaw agents bind --agent work --bind telegram:ops
openclaw agents unbind --agent work --bind telegram:ops
openclaw agents set-identity --workspace ~/.openclaw/workspace --from-identity
openclaw agents set-identity --agent main --avatar avatars/openclaw.png
openclaw agents delete work
```

## [​](https://docs.openclaw.ai/cli/agents#routing-bindings)

Routing bindings

Use routing bindings to pin inbound channel traffic to a specific agent.List bindings:

```
openclaw agents bindings
openclaw agents bindings --agent work
openclaw agents bindings --json
```

Add bindings:

```
openclaw agents bind --agent work --bind telegram:ops --bind discord:guild-a
```

If you omit `accountId` (`--bind <channel>`), OpenClaw resolves it from channel defaults and plugin setup hooks when available.
### [​](https://docs.openclaw.ai/cli/agents#binding-scope-behavior)

Binding scope behavior

*   A binding without `accountId` matches the channel default account only.
*   `accountId: "*"` is the channel-wide fallback (all accounts) and is less specific than an explicit account binding.
*   If the same agent already has a matching channel binding without `accountId`, and you later bind with an explicit or resolved `accountId`, OpenClaw upgrades that existing binding in place instead of adding a duplicate.

Example:

```
# initial channel-only binding
openclaw agents bind --agent work --bind telegram

# later upgrade to account-scoped binding
openclaw agents bind --agent work --bind telegram:ops
```

After the upgrade, routing for that binding is scoped to `telegram:ops`. If you also want default-account routing, add it explicitly (for example `--bind telegram:default`).Remove bindings:

```
openclaw agents unbind --agent work --bind telegram:ops
openclaw agents unbind --agent work --all
```

## [​](https://docs.openclaw.ai/cli/agents#identity-files)

Identity files

Each agent workspace can include an `IDENTITY.md` at the workspace root:
*   Example path: `~/.openclaw/workspace/IDENTITY.md`
*   `set-identity --from-identity` reads from the workspace root (or an explicit `--identity-file`)

Avatar paths resolve relative to the workspace root.
## [​](https://docs.openclaw.ai/cli/agents#set-identity)

Set identity

`set-identity` writes fields into `agents.list[].identity`:
*   `name`
*   `theme`
*   `emoji`
*   `avatar` (workspace-relative path, http(s) URL, or data URI)

Load from `IDENTITY.md`:

```
openclaw agents set-identity --workspace ~/.openclaw/workspace --from-identity
```

Override fields explicitly:

```
openclaw agents set-identity --agent main --name "OpenClaw" --emoji "🦞" --avatar avatars/openclaw.png
```

Config sample:

```
{
  agents: {
    list: [
      {
        id: "main",
        identity: {
          name: "OpenClaw",
          theme: "space lobster",
          emoji: "🦞",
          avatar: "avatars/openclaw.png",
        },
      },
    ],
  },
}
```

[agent](https://docs.openclaw.ai/cli/agent)[hooks](https://docs.openclaw.ai/cli/hooks)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
