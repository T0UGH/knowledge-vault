# plugins/building-plugins

Source: https://docs.openclaw.ai/plugins/building-plugins

Title: Building Plugins - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/building-plugins

Markdown Content:
# Building Plugins - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/building-plugins#content-area)

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

Building Plugins

Building Plugins

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Tools and Plugins](https://docs.openclaw.ai/tools)

##### Plugins

*   [Install and Configure](https://docs.openclaw.ai/tools/plugin)
*   [Community Plugins](https://docs.openclaw.ai/plugins/community)
*   [Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)
*   Building Plugins 
    *   [Getting Started](https://docs.openclaw.ai/plugins/building-plugins)
    *   [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins)
    *   [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins)
    *   [Migrate to SDK](https://docs.openclaw.ai/plugins/sdk-migration)

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

*   [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins#building-plugins)
*   [Prerequisites](https://docs.openclaw.ai/plugins/building-plugins#prerequisites)
*   [What kind of plugin?](https://docs.openclaw.ai/plugins/building-plugins#what-kind-of-plugin)
*   [Quick start: tool plugin](https://docs.openclaw.ai/plugins/building-plugins#quick-start-tool-plugin)
*   [Plugin capabilities](https://docs.openclaw.ai/plugins/building-plugins#plugin-capabilities)
*   [Registering agent tools](https://docs.openclaw.ai/plugins/building-plugins#registering-agent-tools)
*   [Import conventions](https://docs.openclaw.ai/plugins/building-plugins#import-conventions)
*   [Pre-submission checklist](https://docs.openclaw.ai/plugins/building-plugins#pre-submission-checklist)
*   [Beta Release Testing](https://docs.openclaw.ai/plugins/building-plugins#beta-release-testing)
*   [Next steps](https://docs.openclaw.ai/plugins/building-plugins#next-steps)

Building Plugins

# Building Plugins

# [​](https://docs.openclaw.ai/plugins/building-plugins#building-plugins)

Building Plugins

Plugins extend OpenClaw with new capabilities: channels, model providers, speech, image generation, web search, agent tools, or any combination.You do not need to add your plugin to the OpenClaw repository. Publish to [ClawHub](https://docs.openclaw.ai/tools/clawhub) or npm and users install with `openclaw plugins install <package-name>`. OpenClaw tries ClawHub first and falls back to npm automatically.
## [​](https://docs.openclaw.ai/plugins/building-plugins#prerequisites)

Prerequisites

*   Node >= 22 and a package manager (npm or pnpm)
*   Familiarity with TypeScript (ESM)
*   For in-repo plugins: repository cloned and `pnpm install` done

## [​](https://docs.openclaw.ai/plugins/building-plugins#what-kind-of-plugin)

What kind of plugin?

## Channel plugin

Connect OpenClaw to a messaging platform (Discord, IRC, etc.)

## Provider plugin

Add a model provider (LLM, proxy, or custom endpoint)

## Tool / hook plugin

Register agent tools, event hooks, or services — continue below

## [​](https://docs.openclaw.ai/plugins/building-plugins#quick-start-tool-plugin)

Quick start: tool plugin

This walkthrough creates a minimal plugin that registers an agent tool. Channel and provider plugins have dedicated guides linked above.

1

[](https://docs.openclaw.ai/plugins/building-plugins#)

Create the package and manifest

package.json

openclaw.plugin.json

```
{
  "name": "@myorg/openclaw-my-plugin",
  "version": "1.0.0",
  "type": "module",
  "openclaw": {
    "extensions": ["./index.ts"]
  }
}
```

Every plugin needs a manifest, even with no config. See [Manifest](https://docs.openclaw.ai/plugins/manifest) for the full schema.

2

[](https://docs.openclaw.ai/plugins/building-plugins#)

Write the entry point

```
// index.ts
import { definePluginEntry } from "openclaw/plugin-sdk/plugin-entry";
import { Type } from "@sinclair/typebox";

export default definePluginEntry({
  id: "my-plugin",
  name: "My Plugin",
  description: "Adds a custom tool to OpenClaw",
  register(api) {
    api.registerTool({
      name: "my_tool",
      description: "Do a thing",
      parameters: Type.Object({ input: Type.String() }),
      async execute(_id, params) {
        return { content: [{ type: "text", text: `Got: ${params.input}` }] };
      },
    });
  },
});
```

`definePluginEntry` is for non-channel plugins. For channels, use `defineChannelPluginEntry` — see [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins). For full entry point options, see [Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints).

3

[](https://docs.openclaw.ai/plugins/building-plugins#)

Test and publish

**External plugins:** publish to [ClawHub](https://docs.openclaw.ai/tools/clawhub) or npm, then install:

```
openclaw plugins install @myorg/openclaw-my-plugin
```

OpenClaw checks ClawHub first, then falls back to npm.**In-repo plugins:** place under `extensions/` — automatically discovered.

```
pnpm test -- extensions/my-plugin/
```

## [​](https://docs.openclaw.ai/plugins/building-plugins#plugin-capabilities)

Plugin capabilities

A single plugin can register any number of capabilities via the `api` object:

| Capability | Registration method | Detailed guide |
| --- | --- | --- |
| Text inference (LLM) | `api.registerProvider(...)` | [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) |
| CLI inference backend | `api.registerCliBackend(...)` | [CLI Backends](https://docs.openclaw.ai/gateway/cli-backends) |
| Channel / messaging | `api.registerChannel(...)` | [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) |
| Speech (TTS/STT) | `api.registerSpeechProvider(...)` | [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins#step-5-add-extra-capabilities) |
| Media understanding | `api.registerMediaUnderstandingProvider(...)` | [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins#step-5-add-extra-capabilities) |
| Image generation | `api.registerImageGenerationProvider(...)` | [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins#step-5-add-extra-capabilities) |
| Web search | `api.registerWebSearchProvider(...)` | [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins#step-5-add-extra-capabilities) |
| Agent tools | `api.registerTool(...)` | Below |
| Custom commands | `api.registerCommand(...)` | [Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints) |
| Event hooks | `api.registerHook(...)` | [Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints) |
| HTTP routes | `api.registerHttpRoute(...)` | [Internals](https://docs.openclaw.ai/plugins/architecture#gateway-http-routes) |
| CLI subcommands | `api.registerCli(...)` | [Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints) |

For the full registration API, see [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview#registration-api).Hook guard semantics to keep in mind:
*   `before_tool_call`: `{ block: true }` is terminal and stops lower-priority handlers.
*   `before_tool_call`: `{ block: false }` is treated as no decision.
*   `message_sending`: `{ cancel: true }` is terminal and stops lower-priority handlers.
*   `message_sending`: `{ cancel: false }` is treated as no decision.

See [SDK Overview hook decision semantics](https://docs.openclaw.ai/plugins/sdk-overview#hook-decision-semantics) for details.
## [​](https://docs.openclaw.ai/plugins/building-plugins#registering-agent-tools)

Registering agent tools

Tools are typed functions the LLM can call. They can be required (always available) or optional (user opt-in):

```
register(api) {
  // Required tool — always available
  api.registerTool({
    name: "my_tool",
    description: "Do a thing",
    parameters: Type.Object({ input: Type.String() }),
    async execute(_id, params) {
      return { content: [{ type: "text", text: params.input }] };
    },
  });

  // Optional tool — user must add to allowlist
  api.registerTool(
    {
      name: "workflow_tool",
      description: "Run a workflow",
      parameters: Type.Object({ pipeline: Type.String() }),
      async execute(_id, params) {
        return { content: [{ type: "text", text: params.pipeline }] };
      },
    },
    { optional: true },
  );
}
```

Users enable optional tools in config:

```
{
  tools: { allow: ["workflow_tool"] },
}
```

*   Tool names must not clash with core tools (conflicts are skipped)
*   Use `optional: true` for tools with side effects or extra binary requirements
*   Users can enable all tools from a plugin by adding the plugin id to `tools.allow`

## [​](https://docs.openclaw.ai/plugins/building-plugins#import-conventions)

Import conventions

Always import from focused `openclaw/plugin-sdk/<subpath>` paths:

```
import { definePluginEntry } from "openclaw/plugin-sdk/plugin-entry";
import { createPluginRuntimeStore } from "openclaw/plugin-sdk/runtime-store";

// Wrong: monolithic root (deprecated, will be removed)
import { ... } from "openclaw/plugin-sdk";
```

For the full subpath reference, see [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview).Within your plugin, use local barrel files (`api.ts`, `runtime-api.ts`) for internal imports — never import your own plugin through its SDK path.
## [​](https://docs.openclaw.ai/plugins/building-plugins#pre-submission-checklist)

Pre-submission checklist

**package.json** has correct `openclaw` metadata

**openclaw.plugin.json** manifest is present and valid

Entry point uses `defineChannelPluginEntry` or `definePluginEntry`

All imports use focused `plugin-sdk/<subpath>` paths

Internal imports use local modules, not SDK self-imports

Tests pass (`pnpm test -- extensions/my-plugin/`)

`pnpm check` passes (in-repo plugins)

## [​](https://docs.openclaw.ai/plugins/building-plugins#beta-release-testing)

Beta Release Testing

1.   Watch for GitHub release tags on [openclaw/openclaw](https://github.com/openclaw/openclaw/releases) and subscribe via `Watch`>`Releases`. Beta tags look like `v2026.3.N-beta.1`. You can also turn on notifications for the official OpenClaw X account [@openclaw](https://x.com/openclaw) for release announcements.
2.   Test your plugin against the beta tag as soon as it appears. The window before stable is typically only a few hours.
3.   Post in your plugin’s thread in the `plugin-forum` Discord channel after testing with either `all good` or what broke. If you do not have a thread yet, create one.
4.   If something breaks, open or update an issue titled `Beta blocker: <plugin-name> - <summary>` and apply the `beta-blocker` label. Put the issue link in your thread.
5.   Open a PR to `main` titled `fix(<plugin-id>): beta blocker - <summary>` and link the issue in both the PR and your Discord thread. Contributors cannot label PRs, so the title is the PR-side signal for maintainers and automation. Blockers with a PR get merged; blockers without one might ship anyway. Maintainers watch these threads during beta testing.
6.   Silence means green. If you miss the window, your fix likely lands in the next cycle.

## [​](https://docs.openclaw.ai/plugins/building-plugins#next-steps)

Next steps

## Channel Plugins

Build a messaging channel plugin

## Provider Plugins

Build a model provider plugin

## SDK Overview

Import map and registration API reference

## Runtime Helpers

TTS, search, subagent via api.runtime

## Testing

Test utilities and patterns

## Plugin Manifest

Full manifest schema reference

[Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)[Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
