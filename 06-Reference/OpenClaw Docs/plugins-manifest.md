# plugins/manifest

Source: https://docs.openclaw.ai/plugins/manifest

Title: Plugin Manifest - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/manifest

Markdown Content:
# Plugin Manifest - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/manifest#content-area)

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

Plugin Manifest

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

*   [Plugin manifest (openclaw.plugin.json)](https://docs.openclaw.ai/plugins/manifest#plugin-manifest-openclaw-plugin-json)
*   [What this file does](https://docs.openclaw.ai/plugins/manifest#what-this-file-does)
*   [Minimal example](https://docs.openclaw.ai/plugins/manifest#minimal-example)
*   [Rich example](https://docs.openclaw.ai/plugins/manifest#rich-example)
*   [Top-level field reference](https://docs.openclaw.ai/plugins/manifest#top-level-field-reference)
*   [providerAuthChoices reference](https://docs.openclaw.ai/plugins/manifest#providerauthchoices-reference)
*   [uiHints reference](https://docs.openclaw.ai/plugins/manifest#uihints-reference)
*   [contracts reference](https://docs.openclaw.ai/plugins/manifest#contracts-reference)
*   [Manifest versus package.json](https://docs.openclaw.ai/plugins/manifest#manifest-versus-package-json)
*   [JSON Schema requirements](https://docs.openclaw.ai/plugins/manifest#json-schema-requirements)
*   [Validation behavior](https://docs.openclaw.ai/plugins/manifest#validation-behavior)
*   [Notes](https://docs.openclaw.ai/plugins/manifest#notes)

SDK Reference

# Plugin Manifest

# [​](https://docs.openclaw.ai/plugins/manifest#plugin-manifest-openclaw-plugin-json)

Plugin manifest (openclaw.plugin.json)

This page is for the **native OpenClaw plugin manifest** only.For compatible bundle layouts, see [Plugin bundles](https://docs.openclaw.ai/plugins/bundles).Compatible bundle formats use different manifest files:
*   Codex bundle: `.codex-plugin/plugin.json`
*   Claude bundle: `.claude-plugin/plugin.json` or the default Claude component layout without a manifest
*   Cursor bundle: `.cursor-plugin/plugin.json`

OpenClaw auto-detects those bundle layouts too, but they are not validated against the `openclaw.plugin.json` schema described here.For compatible bundles, OpenClaw currently reads bundle metadata plus declared skill roots, Claude command roots, Claude bundle `settings.json` defaults, and supported hook packs when the layout matches OpenClaw runtime expectations.Every native OpenClaw plugin **must** ship a `openclaw.plugin.json` file in the **plugin root**. OpenClaw uses this manifest to validate configuration **without executing plugin code**. Missing or invalid manifests are treated as plugin errors and block config validation.See the full plugin system guide: [Plugins](https://docs.openclaw.ai/tools/plugin). For the native capability model and current external-compatibility guidance: [Capability model](https://docs.openclaw.ai/plugins/architecture#public-capability-model).
## [​](https://docs.openclaw.ai/plugins/manifest#what-this-file-does)

What this file does

`openclaw.plugin.json` is the metadata OpenClaw reads before it loads your plugin code.Use it for:
*   plugin identity
*   config validation
*   auth and onboarding metadata that should be available without booting plugin runtime
*   static capability ownership snapshots used for bundled compat wiring and contract coverage
*   config UI hints

Do not use it for:
*   registering runtime behavior
*   declaring code entrypoints
*   npm install metadata

Those belong in your plugin code and `package.json`.
## [​](https://docs.openclaw.ai/plugins/manifest#minimal-example)

Minimal example

```
{
  "id": "voice-call",
  "configSchema": {
    "type": "object",
    "additionalProperties": false,
    "properties": {}
  }
}
```

## [​](https://docs.openclaw.ai/plugins/manifest#rich-example)

Rich example

```
{
  "id": "openrouter",
  "name": "OpenRouter",
  "description": "OpenRouter provider plugin",
  "version": "1.0.0",
  "providers": ["openrouter"],
  "cliBackends": ["openrouter-cli"],
  "providerAuthEnvVars": {
    "openrouter": ["OPENROUTER_API_KEY"]
  },
  "providerAuthChoices": [
    {
      "provider": "openrouter",
      "method": "api-key",
      "choiceId": "openrouter-api-key",
      "choiceLabel": "OpenRouter API key",
      "groupId": "openrouter",
      "groupLabel": "OpenRouter",
      "optionKey": "openrouterApiKey",
      "cliFlag": "--openrouter-api-key",
      "cliOption": "--openrouter-api-key <key>",
      "cliDescription": "OpenRouter API key",
      "onboardingScopes": ["text-inference"]
    }
  ],
  "uiHints": {
    "apiKey": {
      "label": "API key",
      "placeholder": "sk-or-v1-...",
      "sensitive": true
    }
  },
  "configSchema": {
    "type": "object",
    "additionalProperties": false,
    "properties": {
      "apiKey": {
        "type": "string"
      }
    }
  }
}
```

## [​](https://docs.openclaw.ai/plugins/manifest#top-level-field-reference)

Top-level field reference

| Field | Required | Type | What it means |
| --- | --- | --- | --- |
| `id` | Yes | `string` | Canonical plugin id. This is the id used in `plugins.entries.<id>`. |
| `configSchema` | Yes | `object` | Inline JSON Schema for this plugin’s config. |
| `enabledByDefault` | No | `true` | Marks a bundled plugin as enabled by default. Omit it, or set any non-`true` value, to leave the plugin disabled by default. |
| `kind` | No | `"memory"` | `"context-engine"` | Declares an exclusive plugin kind used by `plugins.slots.*`. |
| `channels` | No | `string[]` | Channel ids owned by this plugin. Used for discovery and config validation. |
| `providers` | No | `string[]` | Provider ids owned by this plugin. |
| `cliBackends` | No | `string[]` | CLI inference backend ids owned by this plugin. Used for startup auto-activation from explicit config refs. |
| `providerAuthEnvVars` | No | `Record<string, string[]>` | Cheap provider-auth env metadata that OpenClaw can inspect without loading plugin code. |
| `providerAuthChoices` | No | `object[]` | Cheap auth-choice metadata for onboarding pickers, preferred-provider resolution, and simple CLI flag wiring. |
| `contracts` | No | `object` | Static bundled capability snapshot for speech, media-understanding, image-generation, web search, and tool ownership. |
| `skills` | No | `string[]` | Skill directories to load, relative to the plugin root. |
| `name` | No | `string` | Human-readable plugin name. |
| `description` | No | `string` | Short summary shown in plugin surfaces. |
| `version` | No | `string` | Informational plugin version. |
| `uiHints` | No | `Record<string, object>` | UI labels, placeholders, and sensitivity hints for config fields. |

## [​](https://docs.openclaw.ai/plugins/manifest#providerauthchoices-reference)

providerAuthChoices reference

Each `providerAuthChoices` entry describes one onboarding or auth choice. OpenClaw reads this before provider runtime loads.

| Field | Required | Type | What it means |
| --- | --- | --- | --- |
| `provider` | Yes | `string` | Provider id this choice belongs to. |
| `method` | Yes | `string` | Auth method id to dispatch to. |
| `choiceId` | Yes | `string` | Stable auth-choice id used by onboarding and CLI flows. |
| `choiceLabel` | No | `string` | User-facing label. If omitted, OpenClaw falls back to `choiceId`. |
| `choiceHint` | No | `string` | Short helper text for the picker. |
| `groupId` | No | `string` | Optional group id for grouping related choices. |
| `groupLabel` | No | `string` | User-facing label for that group. |
| `groupHint` | No | `string` | Short helper text for the group. |
| `optionKey` | No | `string` | Internal option key for simple one-flag auth flows. |
| `cliFlag` | No | `string` | CLI flag name, such as `--openrouter-api-key`. |
| `cliOption` | No | `string` | Full CLI option shape, such as `--openrouter-api-key <key>`. |
| `cliDescription` | No | `string` | Description used in CLI help. |
| `onboardingScopes` | No | `Array<"text-inference" | "image-generation">` | Which onboarding surfaces this choice should appear in. If omitted, it defaults to `["text-inference"]`. |

## [​](https://docs.openclaw.ai/plugins/manifest#uihints-reference)

uiHints reference

`uiHints` is a map from config field names to small rendering hints.

```
{
  "uiHints": {
    "apiKey": {
      "label": "API key",
      "help": "Used for OpenRouter requests",
      "placeholder": "sk-or-v1-...",
      "sensitive": true
    }
  }
}
```

Each field hint can include:

| Field | Type | What it means |
| --- | --- | --- |
| `label` | `string` | User-facing field label. |
| `help` | `string` | Short helper text. |
| `tags` | `string[]` | Optional UI tags. |
| `advanced` | `boolean` | Marks the field as advanced. |
| `sensitive` | `boolean` | Marks the field as secret or sensitive. |
| `placeholder` | `string` | Placeholder text for form inputs. |

## [​](https://docs.openclaw.ai/plugins/manifest#contracts-reference)

contracts reference

Use `contracts` only for static capability ownership metadata that OpenClaw can read without importing the plugin runtime.

```
{
  "contracts": {
    "speechProviders": ["openai"],
    "mediaUnderstandingProviders": ["openai", "openai-codex"],
    "imageGenerationProviders": ["openai"],
    "webSearchProviders": ["gemini"],
    "tools": ["firecrawl_search", "firecrawl_scrape"]
  }
}
```

Each list is optional:

| Field | Type | What it means |
| --- | --- | --- |
| `speechProviders` | `string[]` | Speech provider ids this plugin owns. |
| `mediaUnderstandingProviders` | `string[]` | Media-understanding provider ids this plugin owns. |
| `imageGenerationProviders` | `string[]` | Image-generation provider ids this plugin owns. |
| `webSearchProviders` | `string[]` | Web-search provider ids this plugin owns. |
| `tools` | `string[]` | Agent tool names this plugin owns for bundled contract checks. |

Legacy top-level `speechProviders`, `mediaUnderstandingProviders`, and `imageGenerationProviders` are deprecated. Use `openclaw doctor --fix` to move them under `contracts`; normal manifest loading no longer treats them as capability ownership.
## [​](https://docs.openclaw.ai/plugins/manifest#manifest-versus-package-json)

Manifest versus package.json

The two files serve different jobs:

| File | Use it for |
| --- | --- |
| `openclaw.plugin.json` | Discovery, config validation, auth-choice metadata, and UI hints that must exist before plugin code runs |
| `package.json` | npm metadata, dependency installation, and the `openclaw` block used for entrypoints and setup or catalog metadata |

If you are unsure where a piece of metadata belongs, use this rule:
*   if OpenClaw must know it before loading plugin code, put it in `openclaw.plugin.json`
*   if it is about packaging, entry files, or npm install behavior, put it in `package.json`

## [​](https://docs.openclaw.ai/plugins/manifest#json-schema-requirements)

JSON Schema requirements

*   **Every plugin must ship a JSON Schema**, even if it accepts no config.
*   An empty schema is acceptable (for example, `{ "type": "object", "additionalProperties": false }`).
*   Schemas are validated at config read/write time, not at runtime.

## [​](https://docs.openclaw.ai/plugins/manifest#validation-behavior)

Validation behavior

*   Unknown `channels.*` keys are **errors**, unless the channel id is declared by a plugin manifest.
*   `plugins.entries.<id>`, `plugins.allow`, `plugins.deny`, and `plugins.slots.*` must reference **discoverable** plugin ids. Unknown ids are **errors**.
*   If a plugin is installed but has a broken or missing manifest or schema, validation fails and Doctor reports the plugin error.
*   If plugin config exists but the plugin is **disabled**, the config is kept and a **warning** is surfaced in Doctor + logs.

See [Configuration reference](https://docs.openclaw.ai/gateway/configuration) for the full `plugins.*` schema.
## [​](https://docs.openclaw.ai/plugins/manifest#notes)

Notes

*   The manifest is **required for native OpenClaw plugins**, including local filesystem loads.
*   Runtime still loads the plugin module separately; the manifest is only for discovery + validation.
*   Only documented manifest fields are read by the manifest loader. Avoid adding custom top-level keys here.
*   `providerAuthEnvVars` is the cheap metadata path for auth probes, env-marker validation, and similar provider-auth surfaces that should not boot plugin runtime just to inspect env names.
*   `providerAuthChoices` is the cheap metadata path for auth-choice pickers, `--auth-choice` resolution, preferred-provider mapping, and simple onboarding CLI flag registration before provider runtime loads. For runtime wizard metadata that requires provider code, see [Provider runtime hooks](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks).
*   Exclusive plugin kinds are selected through `plugins.slots.*`.
    *   `kind: "memory"` is selected by `plugins.slots.memory`.
    *   `kind: "context-engine"` is selected by `plugins.slots.contextEngine` (default: built-in `legacy`).

*   `channels`, `providers`, `cliBackends`, and `skills` can be omitted when a plugin does not need them.
*   If your plugin depends on native modules, document the build steps and any package-manager allowlist requirements (for example, pnpm `allow-build-scripts`
    *   `pnpm rebuild <package>`).

[Testing](https://docs.openclaw.ai/plugins/sdk-testing)[Internals](https://docs.openclaw.ai/plugins/architecture)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
