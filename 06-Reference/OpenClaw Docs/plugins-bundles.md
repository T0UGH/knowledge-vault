# plugins/bundles

Source: https://docs.openclaw.ai/plugins/bundles

Title: Plugin Bundles - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/bundles

Markdown Content:
# Plugin Bundles - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/bundles#content-area)

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

Plugin Bundles

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

*   [Plugin Bundles](https://docs.openclaw.ai/plugins/bundles#plugin-bundles)
*   [Why bundles exist](https://docs.openclaw.ai/plugins/bundles#why-bundles-exist)
*   [Install a bundle](https://docs.openclaw.ai/plugins/bundles#install-a-bundle)
*   [What OpenClaw maps from bundles](https://docs.openclaw.ai/plugins/bundles#what-openclaw-maps-from-bundles)
*   [Supported now](https://docs.openclaw.ai/plugins/bundles#supported-now)
*   [Detected but not executed](https://docs.openclaw.ai/plugins/bundles#detected-but-not-executed)
*   [Bundle formats](https://docs.openclaw.ai/plugins/bundles#bundle-formats)
*   [Detection precedence](https://docs.openclaw.ai/plugins/bundles#detection-precedence)
*   [Security](https://docs.openclaw.ai/plugins/bundles#security)
*   [Troubleshooting](https://docs.openclaw.ai/plugins/bundles#troubleshooting)
*   [Related](https://docs.openclaw.ai/plugins/bundles#related)

Plugins

# Plugin Bundles

# [​](https://docs.openclaw.ai/plugins/bundles#plugin-bundles)

Plugin Bundles

OpenClaw can install plugins from three external ecosystems: **Codex**, **Claude**, and **Cursor**. These are called **bundles** — content and metadata packs that OpenClaw maps into native features like skills, hooks, and MCP tools.

Bundles are **not** the same as native OpenClaw plugins. Native plugins run in-process and can register any capability. Bundles are content packs with selective feature mapping and a narrower trust boundary.

## [​](https://docs.openclaw.ai/plugins/bundles#why-bundles-exist)

Why bundles exist

Many useful plugins are published in Codex, Claude, or Cursor format. Instead of requiring authors to rewrite them as native OpenClaw plugins, OpenClaw detects these formats and maps their supported content into the native feature set. This means you can install a Claude command pack or a Codex skill bundle and use it immediately.
## [​](https://docs.openclaw.ai/plugins/bundles#install-a-bundle)

Install a bundle

1

[](https://docs.openclaw.ai/plugins/bundles#)

Install from a directory, archive, or marketplace

```
# Local directory
openclaw plugins install ./my-bundle

# Archive
openclaw plugins install ./my-bundle.tgz

# Claude marketplace
openclaw plugins marketplace list <marketplace-name>
openclaw plugins install <plugin-name>@<marketplace-name>
```

2

[](https://docs.openclaw.ai/plugins/bundles#)

Verify detection

```
openclaw plugins list
openclaw plugins inspect <id>
```

Bundles show as `Format: bundle` with a subtype of `codex`, `claude`, or `cursor`.

3

[](https://docs.openclaw.ai/plugins/bundles#)

Restart and use

```
openclaw gateway restart
```

Mapped features (skills, hooks, MCP tools) are available in the next session.

## [​](https://docs.openclaw.ai/plugins/bundles#what-openclaw-maps-from-bundles)

What OpenClaw maps from bundles

Not every bundle feature runs in OpenClaw today. Here is what works and what is detected but not yet wired.
### [​](https://docs.openclaw.ai/plugins/bundles#supported-now)

Supported now

| Feature | How it maps | Applies to |
| --- | --- | --- |
| Skill content | Bundle skill roots load as normal OpenClaw skills | All formats |
| Commands | `commands/` and `.cursor/commands/` treated as skill roots | Claude, Cursor |
| Hook packs | OpenClaw-style `HOOK.md` + `handler.ts` layouts | Codex |
| MCP tools | Bundle MCP config merged into embedded Pi settings; supported stdio servers launched as subprocesses | All formats |
| Settings | Claude `settings.json` imported as embedded Pi defaults | Claude |

### [​](https://docs.openclaw.ai/plugins/bundles#detected-but-not-executed)

Detected but not executed

These are recognized and shown in diagnostics, but OpenClaw does not run them:
*   Claude `agents`, `hooks.json` automation, `lspServers`, `outputStyles`
*   Cursor `.cursor/agents`, `.cursor/hooks.json`, `.cursor/rules`
*   Codex inline/app metadata beyond capability reporting

## [​](https://docs.openclaw.ai/plugins/bundles#bundle-formats)

Bundle formats

Codex bundles

Markers: `.codex-plugin/plugin.json`Optional content: `skills/`, `hooks/`, `.mcp.json`, `.app.json`Codex bundles fit OpenClaw best when they use skill roots and OpenClaw-style hook-pack directories (`HOOK.md` + `handler.ts`).

Claude bundles

Two detection modes:
*   **Manifest-based:**`.claude-plugin/plugin.json`
*   **Manifestless:** default Claude layout (`skills/`, `commands/`, `agents/`, `hooks/`, `.mcp.json`, `settings.json`)

Claude-specific behavior:
*   `commands/` is treated as skill content
*   `settings.json` is imported into embedded Pi settings (shell override keys are sanitized)
*   `.mcp.json` exposes supported stdio tools to embedded Pi
*   `hooks/hooks.json` is detected but not executed
*   Custom component paths in the manifest are additive (they extend defaults, not replace them)

Cursor bundles

Markers: `.cursor-plugin/plugin.json`Optional content: `skills/`, `.cursor/commands/`, `.cursor/agents/`, `.cursor/rules/`, `.cursor/hooks.json`, `.mcp.json`
*   `.cursor/commands/` is treated as skill content
*   `.cursor/rules/`, `.cursor/agents/`, and `.cursor/hooks.json` are detect-only

## [​](https://docs.openclaw.ai/plugins/bundles#detection-precedence)

Detection precedence

OpenClaw checks for native plugin format first:
1.   `openclaw.plugin.json` or valid `package.json` with `openclaw.extensions` — treated as **native plugin**
2.   Bundle markers (`.codex-plugin/`, `.claude-plugin/`, or default Claude/Cursor layout) — treated as **bundle**

If a directory contains both, OpenClaw uses the native path. This prevents dual-format packages from being partially installed as bundles.
## [​](https://docs.openclaw.ai/plugins/bundles#security)

Security

Bundles have a narrower trust boundary than native plugins:
*   OpenClaw does **not** load arbitrary bundle runtime modules in-process
*   Skills and hook-pack paths must stay inside the plugin root (boundary-checked)
*   Settings files are read with the same boundary checks
*   Supported stdio MCP servers may be launched as subprocesses

This makes bundles safer by default, but you should still treat third-party bundles as trusted content for the features they do expose.
## [​](https://docs.openclaw.ai/plugins/bundles#troubleshooting)

Troubleshooting

Bundle is detected but capabilities do not run

Run `openclaw plugins inspect <id>`. If a capability is listed but marked as not wired, that is a product limit — not a broken install.

Claude command files do not appear

Make sure the bundle is enabled and the markdown files are inside a detected `commands/` or `skills/` root.

Claude settings do not apply

Only embedded Pi settings from `settings.json` are supported. OpenClaw does not treat bundle settings as raw config patches.

Claude hooks do not execute

`hooks/hooks.json` is detect-only. If you need runnable hooks, use the OpenClaw hook-pack layout or ship a native plugin.

## [​](https://docs.openclaw.ai/plugins/bundles#related)

Related

*   [Install and Configure Plugins](https://docs.openclaw.ai/tools/plugin)
*   [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins) — create a native plugin
*   [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest) — native manifest schema

[Community Plugins](https://docs.openclaw.ai/plugins/community)[Getting Started](https://docs.openclaw.ai/plugins/building-plugins)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
