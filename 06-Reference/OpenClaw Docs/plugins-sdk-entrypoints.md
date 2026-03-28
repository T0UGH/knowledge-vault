# plugins/sdk-entrypoints

Source: https://docs.openclaw.ai/plugins/sdk-entrypoints

Title: Plugin Entry Points - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/sdk-entrypoints

Markdown Content:
# Plugin Entry Points - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/sdk-entrypoints#content-area)

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

SDK Reference

Plugin Entry Points

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Tools and Plugins](https://docs.openclaw.ai/tools)

##### Plugins

*   [Install and Configure](https://docs.openclaw.ai/tools/plugin)
*   [Community Plugins](https://docs.openclaw.ai/plugins/community)
*   [Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)
*   Building Plugins 
*   SDK Reference 
    *   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview)
    *   [Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints)
    *   [Runtime Helpers](https://docs.openclaw.ai/plugins/sdk-runtime)
    *   [Setup and Config](https://docs.openclaw.ai/plugins/sdk-setup)
    *   [Testing](https://docs.openclaw.ai/plugins/sdk-testing)
    *   [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest)
    *   [Internals](https://docs.openclaw.ai/plugins/architecture)

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

*   [Plugin Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints#plugin-entry-points)
*   [definePluginEntry](https://docs.openclaw.ai/plugins/sdk-entrypoints#definepluginentry)
*   [defineChannelPluginEntry](https://docs.openclaw.ai/plugins/sdk-entrypoints#definechannelpluginentry)
*   [defineSetupPluginEntry](https://docs.openclaw.ai/plugins/sdk-entrypoints#definesetuppluginentry)
*   [Registration mode](https://docs.openclaw.ai/plugins/sdk-entrypoints#registration-mode)
*   [Plugin shapes](https://docs.openclaw.ai/plugins/sdk-entrypoints#plugin-shapes)
*   [Related](https://docs.openclaw.ai/plugins/sdk-entrypoints#related)

SDK Reference

# Plugin Entry Points

# [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#plugin-entry-points)

Plugin Entry Points

Every plugin exports a default entry object. The SDK provides three helpers for creating them.

**Looking for a walkthrough?** See [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) or [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) for step-by-step guides.

## [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#definepluginentry)

`definePluginEntry`

**Import:**`openclaw/plugin-sdk/plugin-entry`For provider plugins, tool plugins, hook plugins, and anything that is **not** a messaging channel.

```
import { definePluginEntry } from "openclaw/plugin-sdk/plugin-entry";

export default definePluginEntry({
  id: "my-plugin",
  name: "My Plugin",
  description: "Short summary",
  register(api) {
    api.registerProvider({
      /* ... */
    });
    api.registerTool({
      /* ... */
    });
  },
});
```

| Field | Type | Required | Default |
| --- | --- | --- | --- |
| `id` | `string` | Yes | — |
| `name` | `string` | Yes | — |
| `description` | `string` | Yes | — |
| `kind` | `string` | No | — |
| `configSchema` | `OpenClawPluginConfigSchema | () => OpenClawPluginConfigSchema` | No | Empty object schema |
| `register` | `(api: OpenClawPluginApi) => void` | Yes | — |

*   `id` must match your `openclaw.plugin.json` manifest.
*   `kind` is for exclusive slots: `"memory"` or `"context-engine"`.
*   `configSchema` can be a function for lazy evaluation.

## [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#definechannelpluginentry)

`defineChannelPluginEntry`

**Import:**`openclaw/plugin-sdk/core`Wraps `definePluginEntry` with channel-specific wiring. Automatically calls `api.registerChannel({ plugin })` and gates `registerFull` on registration mode.

```
import { defineChannelPluginEntry } from "openclaw/plugin-sdk/core";

export default defineChannelPluginEntry({
  id: "my-channel",
  name: "My Channel",
  description: "Short summary",
  plugin: myChannelPlugin,
  setRuntime: setMyRuntime,
  registerFull(api) {
    api.registerCli(/* ... */);
    api.registerGatewayMethod(/* ... */);
  },
});
```

| Field | Type | Required | Default |
| --- | --- | --- | --- |
| `id` | `string` | Yes | — |
| `name` | `string` | Yes | — |
| `description` | `string` | Yes | — |
| `plugin` | `ChannelPlugin` | Yes | — |
| `configSchema` | `OpenClawPluginConfigSchema | () => OpenClawPluginConfigSchema` | No | Empty object schema |
| `setRuntime` | `(runtime: PluginRuntime) => void` | No | — |
| `registerFull` | `(api: OpenClawPluginApi) => void` | No | — |

*   `setRuntime` is called during registration so you can store the runtime reference (typically via `createPluginRuntimeStore`).
*   `registerFull` only runs when `api.registrationMode === "full"`. It is skipped during setup-only loading.

## [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#definesetuppluginentry)

`defineSetupPluginEntry`

**Import:**`openclaw/plugin-sdk/core`For the lightweight `setup-entry.ts` file. Returns just `{ plugin }` with no runtime or CLI wiring.

```
import { defineSetupPluginEntry } from "openclaw/plugin-sdk/core";

export default defineSetupPluginEntry(myChannelPlugin);
```

OpenClaw loads this instead of the full entry when a channel is disabled, unconfigured, or when deferred loading is enabled. See [Setup and Config](https://docs.openclaw.ai/plugins/sdk-setup#setup-entry) for when this matters.
## [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#registration-mode)

Registration mode

`api.registrationMode` tells your plugin how it was loaded:

| Mode | When | What to register |
| --- | --- | --- |
| `"full"` | Normal gateway startup | Everything |
| `"setup-only"` | Disabled/unconfigured channel | Channel registration only |
| `"setup-runtime"` | Setup flow with runtime available | Channel + lightweight runtime |

`defineChannelPluginEntry` handles this split automatically. If you use `definePluginEntry` directly for a channel, check mode yourself:

```
register(api) {
  api.registerChannel({ plugin: myPlugin });
  if (api.registrationMode !== "full") return;

  // Heavy runtime-only registrations
  api.registerCli(/* ... */);
  api.registerService(/* ... */);
}
```

## [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#plugin-shapes)

Plugin shapes

OpenClaw classifies loaded plugins by their registration behavior:

| Shape | Description |
| --- | --- |
| **plain-capability** | One capability type (e.g. provider-only) |
| **hybrid-capability** | Multiple capability types (e.g. provider + speech) |
| **hook-only** | Only hooks, no capabilities |
| **non-capability** | Tools/commands/services but no capabilities |

Use `openclaw plugins inspect <id>` to see a plugin’s shape.
## [​](https://docs.openclaw.ai/plugins/sdk-entrypoints#related)

Related

*   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview) — registration API and subpath reference
*   [Runtime Helpers](https://docs.openclaw.ai/plugins/sdk-runtime) — `api.runtime` and `createPluginRuntimeStore`
*   [Setup and Config](https://docs.openclaw.ai/plugins/sdk-setup) — manifest, setup entry, deferred loading
*   [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) — building the `ChannelPlugin` object
*   [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) — provider registration and hooks

[SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview)[Runtime Helpers](https://docs.openclaw.ai/plugins/sdk-runtime)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
