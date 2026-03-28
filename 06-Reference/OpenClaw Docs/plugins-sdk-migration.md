# plugins/sdk-migration

Source: https://docs.openclaw.ai/plugins/sdk-migration

Title: Plugin SDK Migration - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/sdk-migration

Markdown Content:
# Plugin SDK Migration - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/sdk-migration#content-area)

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

Plugin SDK Migration

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

*   [Plugin SDK Migration](https://docs.openclaw.ai/plugins/sdk-migration#plugin-sdk-migration)
*   [What is changing](https://docs.openclaw.ai/plugins/sdk-migration#what-is-changing)
*   [Why this changed](https://docs.openclaw.ai/plugins/sdk-migration#why-this-changed)
*   [How to migrate](https://docs.openclaw.ai/plugins/sdk-migration#how-to-migrate)
*   [Import path reference](https://docs.openclaw.ai/plugins/sdk-migration#import-path-reference)
*   [Removal timeline](https://docs.openclaw.ai/plugins/sdk-migration#removal-timeline)
*   [Suppressing the warnings temporarily](https://docs.openclaw.ai/plugins/sdk-migration#suppressing-the-warnings-temporarily)
*   [Related](https://docs.openclaw.ai/plugins/sdk-migration#related)

Building Plugins

# Plugin SDK Migration

# [​](https://docs.openclaw.ai/plugins/sdk-migration#plugin-sdk-migration)

Plugin SDK Migration

OpenClaw has moved from a broad backwards-compatibility layer to a modern plugin architecture with focused, documented imports. If your plugin was built before the new architecture, this guide helps you migrate.
## [​](https://docs.openclaw.ai/plugins/sdk-migration#what-is-changing)

What is changing

The old plugin system provided two wide-open surfaces that let plugins import anything they needed from a single entry point:
*   **`openclaw/plugin-sdk/compat`** — a single import that re-exported dozens of helpers. It was introduced to keep older hook-based plugins working while the new plugin architecture was being built.
*   **`openclaw/extension-api`** — a bridge that gave plugins direct access to host-side helpers like the embedded agent runner.

Both surfaces are now **deprecated**. They still work at runtime, but new plugins must not use them, and existing plugins should migrate before the next major release removes them.

The backwards-compatibility layer will be removed in a future major release. Plugins that still import from these surfaces will break when that happens.

## [​](https://docs.openclaw.ai/plugins/sdk-migration#why-this-changed)

Why this changed

The old approach caused problems:
*   **Slow startup** — importing one helper loaded dozens of unrelated modules
*   **Circular dependencies** — broad re-exports made it easy to create import cycles
*   **Unclear API surface** — no way to tell which exports were stable vs internal

The modern plugin SDK fixes this: each import path (`openclaw/plugin-sdk/\<subpath\>`) is a small, self-contained module with a clear purpose and documented contract.
## [​](https://docs.openclaw.ai/plugins/sdk-migration#how-to-migrate)

How to migrate

1

[](https://docs.openclaw.ai/plugins/sdk-migration#)

Find deprecated imports

Search your plugin for imports from either deprecated surface:

```
grep -r "plugin-sdk/compat" my-plugin/
grep -r "openclaw/extension-api" my-plugin/
```

2

[](https://docs.openclaw.ai/plugins/sdk-migration#)

Replace with focused imports

Each export from the old surface maps to a specific modern import path:

```
// Before (deprecated backwards-compatibility layer)
import {
  createChannelReplyPipeline,
  createPluginRuntimeStore,
  resolveControlCommandGate,
} from "openclaw/plugin-sdk/compat";

// After (modern focused imports)
import { createChannelReplyPipeline } from "openclaw/plugin-sdk/channel-reply-pipeline";
import { createPluginRuntimeStore } from "openclaw/plugin-sdk/runtime-store";
import { resolveControlCommandGate } from "openclaw/plugin-sdk/command-auth";
```

For host-side helpers, use the injected plugin runtime instead of importing directly:

```
// Before (deprecated extension-api bridge)
import { runEmbeddedPiAgent } from "openclaw/extension-api";
const result = await runEmbeddedPiAgent({ sessionId, prompt });

// After (injected runtime)
const result = await api.runtime.agent.runEmbeddedPiAgent({ sessionId, prompt });
```

The same pattern applies to other legacy bridge helpers:

| Old import | Modern equivalent |
| --- | --- |
| `resolveAgentDir` | `api.runtime.agent.resolveAgentDir` |
| `resolveAgentWorkspaceDir` | `api.runtime.agent.resolveAgentWorkspaceDir` |
| `resolveAgentIdentity` | `api.runtime.agent.resolveAgentIdentity` |
| `resolveThinkingDefault` | `api.runtime.agent.resolveThinkingDefault` |
| `resolveAgentTimeoutMs` | `api.runtime.agent.resolveAgentTimeoutMs` |
| `ensureAgentWorkspace` | `api.runtime.agent.ensureAgentWorkspace` |
| session store helpers | `api.runtime.agent.session.*` |

3

[](https://docs.openclaw.ai/plugins/sdk-migration#)

Build and test

```
pnpm build
pnpm test -- my-plugin/
```

## [​](https://docs.openclaw.ai/plugins/sdk-migration#import-path-reference)

Import path reference

Full import path table

| Import path | Purpose | Key exports |
| --- | --- | --- |
| `plugin-sdk/plugin-entry` | Canonical plugin entry helper | `definePluginEntry` |
| `plugin-sdk/core` | Channel entry definitions, channel builders, base types | `defineChannelPluginEntry`, `createChatChannelPlugin` |
| `plugin-sdk/channel-setup` | Setup wizard adapters | `createOptionalChannelSetupSurface` |
| `plugin-sdk/channel-pairing` | DM pairing primitives | `createChannelPairingController` |
| `plugin-sdk/channel-reply-pipeline` | Reply prefix + typing wiring | `createChannelReplyPipeline` |
| `plugin-sdk/channel-config-helpers` | Config adapter factories | `createHybridChannelConfigAdapter` |
| `plugin-sdk/channel-config-schema` | Config schema builders | Channel config schema types |
| `plugin-sdk/channel-policy` | Group/DM policy resolution | `resolveChannelGroupRequireMention` |
| `plugin-sdk/channel-lifecycle` | Account status tracking | `createAccountStatusSink` |
| `plugin-sdk/channel-runtime` | Runtime wiring helpers | Channel runtime utilities |
| `plugin-sdk/channel-send-result` | Send result types | Reply result types |
| `plugin-sdk/runtime-store` | Persistent plugin storage | `createPluginRuntimeStore` |
| `plugin-sdk/approval-runtime` | Approval prompt helpers | Exec/plugin approval payload and reply helpers |
| `plugin-sdk/collection-runtime` | Bounded cache helpers | `pruneMapToMaxSize` |
| `plugin-sdk/diagnostic-runtime` | Diagnostic gating helpers | `isDiagnosticFlagEnabled`, `isDiagnosticsEnabled` |
| `plugin-sdk/error-runtime` | Error formatting helpers | `formatUncaughtError`, error graph helpers |
| `plugin-sdk/fetch-runtime` | Wrapped fetch/proxy helpers | `resolveFetch`, proxy helpers |
| `plugin-sdk/host-runtime` | Host normalization helpers | `normalizeHostname`, `normalizeScpRemoteHost` |
| `plugin-sdk/retry-runtime` | Retry helpers | `RetryConfig`, `retryAsync`, policy runners |
| `plugin-sdk/allow-from` | Allowlist formatting | `formatAllowFromLowercase` |
| `plugin-sdk/allowlist-resolution` | Allowlist input mapping | `mapAllowlistResolutionInputs` |
| `plugin-sdk/command-auth` | Command gating | `resolveControlCommandGate` |
| `plugin-sdk/secret-input` | Secret input parsing | Secret input helpers |
| `plugin-sdk/webhook-ingress` | Webhook request helpers | Webhook target utilities |
| `plugin-sdk/webhook-request-guards` | Webhook body guard helpers | Request body read/limit helpers |
| `plugin-sdk/reply-payload` | Message reply types | Reply payload types |
| `plugin-sdk/provider-onboard` | Provider onboarding patches | Onboarding config helpers |
| `plugin-sdk/keyed-async-queue` | Ordered async queue | `KeyedAsyncQueue` |
| `plugin-sdk/testing` | Test utilities | Test helpers and mocks |

Use the narrowest import that matches the job. If you cannot find an export, check the source at `src/plugin-sdk/` or ask in Discord.
## [​](https://docs.openclaw.ai/plugins/sdk-migration#removal-timeline)

Removal timeline

| When | What happens |
| --- | --- |
| **Now** | Deprecated surfaces emit runtime warnings |
| **Next major release** | Deprecated surfaces will be removed; plugins still using them will fail |

All core plugins have already been migrated. External plugins should migrate before the next major release.
## [​](https://docs.openclaw.ai/plugins/sdk-migration#suppressing-the-warnings-temporarily)

Suppressing the warnings temporarily

Set these environment variables while you work on migrating:

```
OPENCLAW_SUPPRESS_PLUGIN_SDK_COMPAT_WARNING=1 openclaw gateway run
OPENCLAW_SUPPRESS_EXTENSION_API_WARNING=1 openclaw gateway run
```

This is a temporary escape hatch, not a permanent solution.
## [​](https://docs.openclaw.ai/plugins/sdk-migration#related)

Related

*   [Getting Started](https://docs.openclaw.ai/plugins/building-plugins) — build your first plugin
*   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview) — full subpath import reference
*   [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) — building channel plugins
*   [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) — building provider plugins
*   [Plugin Internals](https://docs.openclaw.ai/plugins/architecture) — architecture deep dive
*   [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest) — manifest schema reference

[Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins)[SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
