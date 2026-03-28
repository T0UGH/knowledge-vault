# plugins/sdk-setup

Source: https://docs.openclaw.ai/plugins/sdk-setup

Title: Plugin Setup and Config - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/sdk-setup

Markdown Content:
# Plugin Setup and Config - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/sdk-setup#content-area)

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

Plugin Setup and Config

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

*   [Plugin Setup and Config](https://docs.openclaw.ai/plugins/sdk-setup#plugin-setup-and-config)
*   [Package metadata](https://docs.openclaw.ai/plugins/sdk-setup#package-metadata)
*   [openclaw fields](https://docs.openclaw.ai/plugins/sdk-setup#openclaw-fields)
*   [Deferred full load](https://docs.openclaw.ai/plugins/sdk-setup#deferred-full-load)
*   [Plugin manifest](https://docs.openclaw.ai/plugins/sdk-setup#plugin-manifest)
*   [Setup entry](https://docs.openclaw.ai/plugins/sdk-setup#setup-entry)
*   [Config schema](https://docs.openclaw.ai/plugins/sdk-setup#config-schema)
*   [Building channel config schemas](https://docs.openclaw.ai/plugins/sdk-setup#building-channel-config-schemas)
*   [Setup wizards](https://docs.openclaw.ai/plugins/sdk-setup#setup-wizards)
*   [Publishing and installing](https://docs.openclaw.ai/plugins/sdk-setup#publishing-and-installing)
*   [Related](https://docs.openclaw.ai/plugins/sdk-setup#related)

SDK Reference

# Plugin Setup and Config

# [​](https://docs.openclaw.ai/plugins/sdk-setup#plugin-setup-and-config)

Plugin Setup and Config

Reference for plugin packaging (`package.json` metadata), manifests (`openclaw.plugin.json`), setup entries, and config schemas.

**Looking for a walkthrough?** The how-to guides cover packaging in context: [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins#step-1-package-and-manifest) and [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins#step-1-package-and-manifest).

## [​](https://docs.openclaw.ai/plugins/sdk-setup#package-metadata)

Package metadata

Your `package.json` needs an `openclaw` field that tells the plugin system what your plugin provides:**Channel plugin:**

```
{
  "name": "@myorg/openclaw-my-channel",
  "version": "1.0.0",
  "type": "module",
  "openclaw": {
    "extensions": ["./index.ts"],
    "setupEntry": "./setup-entry.ts",
    "channel": {
      "id": "my-channel",
      "label": "My Channel",
      "blurb": "Short description of the channel."
    }
  }
}
```

**Provider plugin:**

```
{
  "name": "@myorg/openclaw-my-provider",
  "version": "1.0.0",
  "type": "module",
  "openclaw": {
    "extensions": ["./index.ts"],
    "providers": ["my-provider"]
  }
}
```

### [​](https://docs.openclaw.ai/plugins/sdk-setup#openclaw-fields)

`openclaw` fields

| Field | Type | Description |
| --- | --- | --- |
| `extensions` | `string[]` | Entry point files (relative to package root) |
| `setupEntry` | `string` | Lightweight setup-only entry (optional) |
| `channel` | `object` | Channel metadata: `id`, `label`, `blurb`, `selectionLabel`, `docsPath`, `order`, `aliases` |
| `providers` | `string[]` | Provider ids registered by this plugin |
| `install` | `object` | Install hints: `npmSpec`, `localPath`, `defaultChoice` |
| `startup` | `object` | Startup behavior flags |

### [​](https://docs.openclaw.ai/plugins/sdk-setup#deferred-full-load)

Deferred full load

Channel plugins can opt into deferred loading with:

```
{
  "openclaw": {
    "extensions": ["./index.ts"],
    "setupEntry": "./setup-entry.ts",
    "startup": {
      "deferConfiguredChannelFullLoadUntilAfterListen": true
    }
  }
}
```

When enabled, OpenClaw loads only `setupEntry` during the pre-listen startup phase, even for already-configured channels. The full entry loads after the gateway starts listening.

Only enable deferred loading when your `setupEntry` registers everything the gateway needs before it starts listening (channel registration, HTTP routes, gateway methods). If the full entry owns required startup capabilities, keep the default behavior.

## [​](https://docs.openclaw.ai/plugins/sdk-setup#plugin-manifest)

Plugin manifest

Every native plugin must ship an `openclaw.plugin.json` in the package root. OpenClaw uses this to validate config without executing plugin code.

```
{
  "id": "my-plugin",
  "name": "My Plugin",
  "description": "Adds My Plugin capabilities to OpenClaw",
  "configSchema": {
    "type": "object",
    "additionalProperties": false,
    "properties": {
      "webhookSecret": {
        "type": "string",
        "description": "Webhook verification secret"
      }
    }
  }
}
```

For channel plugins, add `kind` and `channels`:

```
{
  "id": "my-channel",
  "kind": "channel",
  "channels": ["my-channel"],
  "configSchema": {
    "type": "object",
    "additionalProperties": false,
    "properties": {}
  }
}
```

Even plugins with no config must ship a schema. An empty schema is valid:

```
{
  "id": "my-plugin",
  "configSchema": {
    "type": "object",
    "additionalProperties": false
  }
}
```

See [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest) for the full schema reference.
## [​](https://docs.openclaw.ai/plugins/sdk-setup#setup-entry)

Setup entry

The `setup-entry.ts` file is a lightweight alternative to `index.ts` that OpenClaw loads when it only needs setup surfaces (onboarding, config repair, disabled channel inspection).

```
// setup-entry.ts
import { defineSetupPluginEntry } from "openclaw/plugin-sdk/core";
import { myChannelPlugin } from "./src/channel.js";

export default defineSetupPluginEntry(myChannelPlugin);
```

This avoids loading heavy runtime code (crypto libraries, CLI registrations, background services) during setup flows.**When OpenClaw uses `setupEntry` instead of the full entry:**
*   The channel is disabled but needs setup/onboarding surfaces
*   The channel is enabled but unconfigured
*   Deferred loading is enabled (`deferConfiguredChannelFullLoadUntilAfterListen`)

**What `setupEntry` must register:**
*   The channel plugin object (via `defineSetupPluginEntry`)
*   Any HTTP routes required before gateway listen
*   Any gateway methods needed during startup

**What `setupEntry` should NOT include:**
*   CLI registrations
*   Background services
*   Heavy runtime imports (crypto, SDKs)
*   Gateway methods only needed after startup

## [​](https://docs.openclaw.ai/plugins/sdk-setup#config-schema)

Config schema

Plugin config is validated against the JSON Schema in your manifest. Users configure plugins via:

```
{
  plugins: {
    entries: {
      "my-plugin": {
        config: {
          webhookSecret: "abc123",
        },
      },
    },
  },
}
```

Your plugin receives this config as `api.pluginConfig` during registration.For channel-specific config, use the channel config section instead:

```
{
  channels: {
    "my-channel": {
      token: "bot-token",
      allowFrom: ["user1", "user2"],
    },
  },
}
```

### [​](https://docs.openclaw.ai/plugins/sdk-setup#building-channel-config-schemas)

Building channel config schemas

Use `buildChannelConfigSchema` from `openclaw/plugin-sdk/core` to convert a Zod schema into the `ChannelConfigSchema` wrapper that OpenClaw validates:

```
import { z } from "zod";
import { buildChannelConfigSchema } from "openclaw/plugin-sdk/core";

const accountSchema = z.object({
  token: z.string().optional(),
  allowFrom: z.array(z.string()).optional(),
  accounts: z.object({}).catchall(z.any()).optional(),
  defaultAccount: z.string().optional(),
});

const configSchema = buildChannelConfigSchema(accountSchema);
```

## [​](https://docs.openclaw.ai/plugins/sdk-setup#setup-wizards)

Setup wizards

Channel plugins can provide interactive setup wizards for `openclaw onboard`. The wizard is a `ChannelSetupWizard` object on the `ChannelPlugin`:

```
import type { ChannelSetupWizard } from "openclaw/plugin-sdk/channel-setup";

const setupWizard: ChannelSetupWizard = {
  channel: "my-channel",
  status: {
    configuredLabel: "Connected",
    unconfiguredLabel: "Not configured",
    resolveConfigured: ({ cfg }) => Boolean((cfg.channels as any)?.["my-channel"]?.token),
  },
  credentials: [
    {
      inputKey: "token",
      providerHint: "my-channel",
      credentialLabel: "Bot token",
      preferredEnvVar: "MY_CHANNEL_BOT_TOKEN",
      envPrompt: "Use MY_CHANNEL_BOT_TOKEN from environment?",
      keepPrompt: "Keep current token?",
      inputPrompt: "Enter your bot token:",
      inspect: ({ cfg, accountId }) => {
        const token = (cfg.channels as any)?.["my-channel"]?.token;
        return {
          accountConfigured: Boolean(token),
          hasConfiguredValue: Boolean(token),
        };
      },
    },
  ],
};
```

The `ChannelSetupWizard` type supports `credentials`, `textInputs`, `dmPolicy`, `allowFrom`, `groupAccess`, `prepare`, `finalize`, and more. See bundled plugins (e.g. `extensions/discord/src/channel.setup.ts`) for full examples.For DM allowlist prompts that only need the standard `note -> prompt -> parse -> merge -> patch` flow, prefer the shared setup helpers from `openclaw/plugin-sdk/setup`: `createPromptParsedAllowFromForAccount(...)`, `createTopLevelChannelParsedAllowFromPrompt(...)`, and `createNestedChannelParsedAllowFromPrompt(...)`.For channel setup status blocks that only vary by labels, scores, and optional extra lines, prefer `createStandardChannelSetupStatus(...)` from `openclaw/plugin-sdk/setup` instead of hand-rolling the same `status` object in each plugin.For optional setup surfaces that should only appear in certain contexts, use `createOptionalChannelSetupSurface` from `openclaw/plugin-sdk/channel-setup`:

```
import { createOptionalChannelSetupSurface } from "openclaw/plugin-sdk/channel-setup";

const setupSurface = createOptionalChannelSetupSurface({
  channel: "my-channel",
  label: "My Channel",
  npmSpec: "@myorg/openclaw-my-channel",
  docsPath: "/channels/my-channel",
});
// Returns { setupAdapter, setupWizard }
```

## [​](https://docs.openclaw.ai/plugins/sdk-setup#publishing-and-installing)

Publishing and installing

**External plugins:** publish to [ClawHub](https://docs.openclaw.ai/tools/clawhub) or npm, then install:

```
openclaw plugins install @myorg/openclaw-my-plugin
```

OpenClaw tries ClawHub first and falls back to npm automatically. You can also force a specific source:

```
openclaw plugins install clawhub:@myorg/openclaw-my-plugin   # ClawHub only
openclaw plugins install npm:@myorg/openclaw-my-plugin       # npm only
```

**In-repo plugins:** place under `extensions/` and they are automatically discovered during build.**Users can browse and install:**

```
openclaw plugins search <query>
openclaw plugins install <package-name>
```

For npm-sourced installs, `openclaw plugins install` runs `npm install --ignore-scripts` (no lifecycle scripts). Keep plugin dependency trees pure JS/TS and avoid packages that require `postinstall` builds.

## [​](https://docs.openclaw.ai/plugins/sdk-setup#related)

Related

*   [SDK Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints) — `definePluginEntry` and `defineChannelPluginEntry`
*   [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest) — full manifest schema reference
*   [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins) — step-by-step getting started guide

[Runtime Helpers](https://docs.openclaw.ai/plugins/sdk-runtime)[Testing](https://docs.openclaw.ai/plugins/sdk-testing)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
