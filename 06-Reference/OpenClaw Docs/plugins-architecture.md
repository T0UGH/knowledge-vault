# plugins/architecture

Source: https://docs.openclaw.ai/plugins/architecture

Title: Plugin Internals - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/architecture

Markdown Content:
# Plugin Internals - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/architecture#content-area)

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

Plugin Internals

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

*   [Plugin Internals](https://docs.openclaw.ai/plugins/architecture#plugin-internals)
*   [Public capability model](https://docs.openclaw.ai/plugins/architecture#public-capability-model)
*   [External compatibility stance](https://docs.openclaw.ai/plugins/architecture#external-compatibility-stance)
*   [Plugin shapes](https://docs.openclaw.ai/plugins/architecture#plugin-shapes)
*   [Legacy hooks](https://docs.openclaw.ai/plugins/architecture#legacy-hooks)
*   [Compatibility signals](https://docs.openclaw.ai/plugins/architecture#compatibility-signals)
*   [Architecture overview](https://docs.openclaw.ai/plugins/architecture#architecture-overview)
*   [Channel plugins and the shared message tool](https://docs.openclaw.ai/plugins/architecture#channel-plugins-and-the-shared-message-tool)
*   [Capability ownership model](https://docs.openclaw.ai/plugins/architecture#capability-ownership-model)
*   [Capability layering](https://docs.openclaw.ai/plugins/architecture#capability-layering)
*   [Multi-capability company plugin example](https://docs.openclaw.ai/plugins/architecture#multi-capability-company-plugin-example)
*   [Capability example: video understanding](https://docs.openclaw.ai/plugins/architecture#capability-example-video-understanding)
*   [Contracts and enforcement](https://docs.openclaw.ai/plugins/architecture#contracts-and-enforcement)
*   [What belongs in a contract](https://docs.openclaw.ai/plugins/architecture#what-belongs-in-a-contract)
*   [Execution model](https://docs.openclaw.ai/plugins/architecture#execution-model)
*   [Export boundary](https://docs.openclaw.ai/plugins/architecture#export-boundary)
*   [Load pipeline](https://docs.openclaw.ai/plugins/architecture#load-pipeline)
*   [Manifest-first behavior](https://docs.openclaw.ai/plugins/architecture#manifest-first-behavior)
*   [What the loader caches](https://docs.openclaw.ai/plugins/architecture#what-the-loader-caches)
*   [Registry model](https://docs.openclaw.ai/plugins/architecture#registry-model)
*   [Conversation binding callbacks](https://docs.openclaw.ai/plugins/architecture#conversation-binding-callbacks)
*   [Provider runtime hooks](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks)
*   [Hook order and usage](https://docs.openclaw.ai/plugins/architecture#hook-order-and-usage)
*   [Provider example](https://docs.openclaw.ai/plugins/architecture#provider-example)
*   [Built-in examples](https://docs.openclaw.ai/plugins/architecture#built-in-examples)
*   [Runtime helpers](https://docs.openclaw.ai/plugins/architecture#runtime-helpers)
*   [Gateway HTTP routes](https://docs.openclaw.ai/plugins/architecture#gateway-http-routes)
*   [Plugin SDK import paths](https://docs.openclaw.ai/plugins/architecture#plugin-sdk-import-paths)
*   [Message tool schemas](https://docs.openclaw.ai/plugins/architecture#message-tool-schemas)
*   [Channel target resolution](https://docs.openclaw.ai/plugins/architecture#channel-target-resolution)
*   [Config-backed directories](https://docs.openclaw.ai/plugins/architecture#config-backed-directories)
*   [Provider catalogs](https://docs.openclaw.ai/plugins/architecture#provider-catalogs)
*   [Read-only channel inspection](https://docs.openclaw.ai/plugins/architecture#read-only-channel-inspection)
*   [Package packs](https://docs.openclaw.ai/plugins/architecture#package-packs)
*   [Channel catalog metadata](https://docs.openclaw.ai/plugins/architecture#channel-catalog-metadata)
*   [Context engine plugins](https://docs.openclaw.ai/plugins/architecture#context-engine-plugins)
*   [Adding a new capability](https://docs.openclaw.ai/plugins/architecture#adding-a-new-capability)
*   [Capability checklist](https://docs.openclaw.ai/plugins/architecture#capability-checklist)
*   [Capability template](https://docs.openclaw.ai/plugins/architecture#capability-template)

SDK Reference

# Plugin Internals

# [​](https://docs.openclaw.ai/plugins/architecture#plugin-internals)

Plugin Internals

This is the **deep architecture reference**. For practical guides, see:
*   [Install and use plugins](https://docs.openclaw.ai/tools/plugin) — user guide
*   [Getting Started](https://docs.openclaw.ai/plugins/building-plugins) — first plugin tutorial
*   [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) — build a messaging channel
*   [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) — build a model provider
*   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview) — import map and registration API

This page covers the internal architecture of the OpenClaw plugin system.
## [​](https://docs.openclaw.ai/plugins/architecture#public-capability-model)

Public capability model

Capabilities are the public **native plugin** model inside OpenClaw. Every native OpenClaw plugin registers against one or more capability types:

| Capability | Registration method | Example plugins |
| --- | --- | --- |
| Text inference | `api.registerProvider(...)` | `openai`, `anthropic` |
| CLI inference backend | `api.registerCliBackend(...)` | `openai`, `anthropic` |
| Speech | `api.registerSpeechProvider(...)` | `elevenlabs`, `microsoft` |
| Media understanding | `api.registerMediaUnderstandingProvider(...)` | `openai`, `google` |
| Image generation | `api.registerImageGenerationProvider(...)` | `openai`, `google` |
| Web search | `api.registerWebSearchProvider(...)` | `google` |
| Channel / messaging | `api.registerChannel(...)` | `msteams`, `matrix` |

A plugin that registers zero capabilities but provides hooks, tools, or services is a **legacy hook-only** plugin. That pattern is still fully supported.
### [​](https://docs.openclaw.ai/plugins/architecture#external-compatibility-stance)

External compatibility stance

The capability model is landed in core and used by bundled/native plugins today, but external plugin compatibility still needs a tighter bar than “it is exported, therefore it is frozen.”Current guidance:
*   **existing external plugins:** keep hook-based integrations working; treat this as the compatibility baseline
*   **new bundled/native plugins:** prefer explicit capability registration over vendor-specific reach-ins or new hook-only designs
*   **external plugins adopting capability registration:** allowed, but treat the capability-specific helper surfaces as evolving unless docs explicitly mark a contract as stable

Practical rule:
*   capability registration APIs are the intended direction
*   legacy hooks remain the safest no-breakage path for external plugins during the transition
*   exported helper subpaths are not all equal; prefer the narrow documented contract, not incidental helper exports

### [​](https://docs.openclaw.ai/plugins/architecture#plugin-shapes)

Plugin shapes

OpenClaw classifies every loaded plugin into a shape based on its actual registration behavior (not just static metadata):
*   **plain-capability** — registers exactly one capability type (for example a provider-only plugin like `mistral`)
*   **hybrid-capability** — registers multiple capability types (for example `openai` owns text inference, speech, media understanding, and image generation)
*   **hook-only** — registers only hooks (typed or custom), no capabilities, tools, commands, or services
*   **non-capability** — registers tools, commands, services, or routes but no capabilities

Use `openclaw plugins inspect <id>` to see a plugin’s shape and capability breakdown. See [CLI reference](https://docs.openclaw.ai/cli/plugins#inspect) for details.
### [​](https://docs.openclaw.ai/plugins/architecture#legacy-hooks)

Legacy hooks

The `before_agent_start` hook remains supported as a compatibility path for hook-only plugins. Legacy real-world plugins still depend on it.Direction:
*   keep it working
*   document it as legacy
*   prefer `before_model_resolve` for model/provider override work
*   prefer `before_prompt_build` for prompt mutation work
*   remove only after real usage drops and fixture coverage proves migration safety

### [​](https://docs.openclaw.ai/plugins/architecture#compatibility-signals)

Compatibility signals

When you run `openclaw doctor` or `openclaw plugins inspect <id>`, you may see one of these labels:

| Signal | Meaning |
| --- | --- |
| **config valid** | Config parses fine and plugins resolve |
| **compatibility advisory** | Plugin uses a supported-but-older pattern (e.g. `hook-only`) |
| **legacy warning** | Plugin uses `before_agent_start`, which is deprecated |
| **hard error** | Config is invalid or plugin failed to load |

Neither `hook-only` nor `before_agent_start` will break your plugin today — `hook-only` is advisory, and `before_agent_start` only triggers a warning. These signals also appear in `openclaw status --all` and `openclaw plugins doctor`.
## [​](https://docs.openclaw.ai/plugins/architecture#architecture-overview)

Architecture overview

OpenClaw’s plugin system has four layers:
1.   **Manifest + discovery** OpenClaw finds candidate plugins from configured paths, workspace roots, global extension roots, and bundled extensions. Discovery reads native `openclaw.plugin.json` manifests plus supported bundle manifests first.
2.   **Enablement + validation** Core decides whether a discovered plugin is enabled, disabled, blocked, or selected for an exclusive slot such as memory.
3.   **Runtime loading** Native OpenClaw plugins are loaded in-process via jiti and register capabilities into a central registry. Compatible bundles are normalized into registry records without importing runtime code.
4.   **Surface consumption** The rest of OpenClaw reads the registry to expose tools, channels, provider setup, hooks, HTTP routes, CLI commands, and services.

The important design boundary:
*   discovery + config validation should work from **manifest/schema metadata** without executing plugin code
*   native runtime behavior comes from the plugin module’s `register(api)` path

That split lets OpenClaw validate config, explain missing/disabled plugins, and build UI/schema hints before the full runtime is active.
### [​](https://docs.openclaw.ai/plugins/architecture#channel-plugins-and-the-shared-message-tool)

Channel plugins and the shared message tool

Channel plugins do not need to register a separate send/edit/react tool for normal chat actions. OpenClaw keeps one shared `message` tool in core, and channel plugins own the channel-specific discovery and execution behind it.The current boundary is:
*   core owns the shared `message` tool host, prompt wiring, session/thread bookkeeping, and execution dispatch
*   channel plugins own scoped action discovery, capability discovery, and any channel-specific schema fragments
*   channel plugins execute the final action through their action adapter

For channel plugins, the SDK surface is `ChannelMessageActionAdapter.describeMessageTool(...)`. That unified discovery call lets a plugin return its visible actions, capabilities, and schema contributions together so those pieces do not drift apart.Core passes runtime scope into that discovery step. Important fields include:
*   `accountId`
*   `currentChannelId`
*   `currentThreadTs`
*   `currentMessageId`
*   `sessionKey`
*   `sessionId`
*   `agentId`
*   trusted inbound `requesterSenderId`

That matters for context-sensitive plugins. A channel can hide or expose message actions based on the active account, current room/thread/message, or trusted requester identity without hardcoding channel-specific branches in the core `message` tool.This is why embedded-runner routing changes are still plugin work: the runner is responsible for forwarding the current chat/session identity into the plugin discovery boundary so the shared `message` tool exposes the right channel-owned surface for the current turn.For channel-owned execution helpers, bundled plugins should keep the execution runtime inside their own extension modules. Core no longer owns the Discord, Slack, Telegram, or WhatsApp message-action runtimes under `src/agents/tools`. We do not publish separate `plugin-sdk/*-action-runtime` subpaths, and bundled plugins should import their own local runtime code directly from their extension-owned modules.For polls specifically, there are two execution paths:
*   `outbound.sendPoll` is the shared baseline for channels that fit the common poll model
*   `actions.handleAction("poll")` is the preferred path for channel-specific poll semantics or extra poll parameters

Core now defers shared poll parsing until after plugin poll dispatch declines the action, so plugin-owned poll handlers can accept channel-specific poll fields without being blocked by the generic poll parser first.See [Load pipeline](https://docs.openclaw.ai/plugins/architecture#load-pipeline) for the full startup sequence.
## [​](https://docs.openclaw.ai/plugins/architecture#capability-ownership-model)

Capability ownership model

OpenClaw treats a native plugin as the ownership boundary for a **company** or a **feature**, not as a grab bag of unrelated integrations.That means:
*   a company plugin should usually own all of that company’s OpenClaw-facing surfaces
*   a feature plugin should usually own the full feature surface it introduces
*   channels should consume shared core capabilities instead of re-implementing provider behavior ad hoc

Examples:
*   the bundled `openai` plugin owns OpenAI model-provider behavior and OpenAI speech + media-understanding + image-generation behavior
*   the bundled `elevenlabs` plugin owns ElevenLabs speech behavior
*   the bundled `microsoft` plugin owns Microsoft speech behavior
*   the bundled `google` plugin owns Google model-provider behavior plus Google media-understanding + image-generation + web-search behavior
*   the bundled `minimax`, `mistral`, `moonshot`, and `zai` plugins own their media-understanding backends
*   the `voice-call` plugin is a feature plugin: it owns call transport, tools, CLI, routes, and runtime, but it consumes core TTS/STT capability instead of inventing a second speech stack

The intended end state is:
*   OpenAI lives in one plugin even if it spans text models, speech, images, and future video
*   another vendor can do the same for its own surface area
*   channels do not care which vendor plugin owns the provider; they consume the shared capability contract exposed by core

This is the key distinction:
*   **plugin** = ownership boundary
*   **capability** = core contract that multiple plugins can implement or consume

So if OpenClaw adds a new domain such as video, the first question is not “which provider should hardcode video handling?” The first question is “what is the core video capability contract?” Once that contract exists, vendor plugins can register against it and channel/feature plugins can consume it.If the capability does not exist yet, the right move is usually:
1.   define the missing capability in core
2.   expose it through the plugin API/runtime in a typed way
3.   wire channels/features against that capability
4.   let vendor plugins register implementations

This keeps ownership explicit while avoiding core behavior that depends on a single vendor or a one-off plugin-specific code path.
### [​](https://docs.openclaw.ai/plugins/architecture#capability-layering)

Capability layering

Use this mental model when deciding where code belongs:
*   **core capability layer**: shared orchestration, policy, fallback, config merge rules, delivery semantics, and typed contracts
*   **vendor plugin layer**: vendor-specific APIs, auth, model catalogs, speech synthesis, image generation, future video backends, usage endpoints
*   **channel/feature plugin layer**: Slack/Discord/voice-call/etc. integration that consumes core capabilities and presents them on a surface

For example, TTS follows this shape:
*   core owns reply-time TTS policy, fallback order, prefs, and channel delivery
*   `openai`, `elevenlabs`, and `microsoft` own synthesis implementations
*   `voice-call` consumes the telephony TTS runtime helper

That same pattern should be preferred for future capabilities.
### [​](https://docs.openclaw.ai/plugins/architecture#multi-capability-company-plugin-example)

Multi-capability company plugin example

A company plugin should feel cohesive from the outside. If OpenClaw has shared contracts for models, speech, media understanding, and web search, a vendor can own all of its surfaces in one place:

```
import type { OpenClawPluginDefinition } from "openclaw/plugin-sdk";
import {
  buildOpenAISpeechProvider,
  createPluginBackedWebSearchProvider,
  describeImageWithModel,
  transcribeOpenAiCompatibleAudio,
} from "openclaw/plugin-sdk";

const plugin: OpenClawPluginDefinition = {
  id: "exampleai",
  name: "ExampleAI",
  register(api) {
    api.registerProvider({
      id: "exampleai",
      // auth/model catalog/runtime hooks
    });

    api.registerSpeechProvider(
      buildOpenAISpeechProvider({
        id: "exampleai",
        // vendor speech config
      }),
    );

    api.registerMediaUnderstandingProvider({
      id: "exampleai",
      capabilities: ["image", "audio", "video"],
      async describeImage(req) {
        return describeImageWithModel({
          provider: "exampleai",
          model: req.model,
          input: req.input,
        });
      },
      async transcribeAudio(req) {
        return transcribeOpenAiCompatibleAudio({
          provider: "exampleai",
          model: req.model,
          input: req.input,
        });
      },
    });

    api.registerWebSearchProvider(
      createPluginBackedWebSearchProvider({
        id: "exampleai-search",
        // credential + fetch logic
      }),
    );
  },
};

export default plugin;
```

What matters is not the exact helper names. The shape matters:
*   one plugin owns the vendor surface
*   core still owns the capability contracts
*   channels and feature plugins consume `api.runtime.*` helpers, not vendor code
*   contract tests can assert that the plugin registered the capabilities it claims to own

### [​](https://docs.openclaw.ai/plugins/architecture#capability-example-video-understanding)

Capability example: video understanding

OpenClaw already treats image/audio/video understanding as one shared capability. The same ownership model applies there:
1.   core defines the media-understanding contract
2.   vendor plugins register `describeImage`, `transcribeAudio`, and `describeVideo` as applicable
3.   channels and feature plugins consume the shared core behavior instead of wiring directly to vendor code

That avoids baking one provider’s video assumptions into core. The plugin owns the vendor surface; core owns the capability contract and fallback behavior.If OpenClaw adds a new domain later, such as video generation, use the same sequence again: define the core capability first, then let vendor plugins register implementations against it.Need a concrete rollout checklist? See [Capability Cookbook](https://docs.openclaw.ai/tools/capability-cookbook).
## [​](https://docs.openclaw.ai/plugins/architecture#contracts-and-enforcement)

Contracts and enforcement

The plugin API surface is intentionally typed and centralized in `OpenClawPluginApi`. That contract defines the supported registration points and the runtime helpers a plugin may rely on.Why this matters:
*   plugin authors get one stable internal standard
*   core can reject duplicate ownership such as two plugins registering the same provider id
*   startup can surface actionable diagnostics for malformed registration
*   contract tests can enforce bundled-plugin ownership and prevent silent drift

There are two layers of enforcement:
1.   **runtime registration enforcement** The plugin registry validates registrations as plugins load. Examples: duplicate provider ids, duplicate speech provider ids, and malformed registrations produce plugin diagnostics instead of undefined behavior.
2.   **contract tests** Bundled plugins are captured in contract registries during test runs so OpenClaw can assert ownership explicitly. Today this is used for model providers, speech providers, web search providers, and bundled registration ownership.

The practical effect is that OpenClaw knows, up front, which plugin owns which surface. That lets core and channels compose seamlessly because ownership is declared, typed, and testable rather than implicit.
### [​](https://docs.openclaw.ai/plugins/architecture#what-belongs-in-a-contract)

What belongs in a contract

Good plugin contracts are:
*   typed
*   small
*   capability-specific
*   owned by core
*   reusable by multiple plugins
*   consumable by channels/features without vendor knowledge

Bad plugin contracts are:
*   vendor-specific policy hidden in core
*   one-off plugin escape hatches that bypass the registry
*   channel code reaching straight into a vendor implementation
*   ad hoc runtime objects that are not part of `OpenClawPluginApi` or `api.runtime`

When in doubt, raise the abstraction level: define the capability first, then let plugins plug into it.
## [​](https://docs.openclaw.ai/plugins/architecture#execution-model)

Execution model

Native OpenClaw plugins run **in-process** with the Gateway. They are not sandboxed. A loaded native plugin has the same process-level trust boundary as core code.Implications:
*   a native plugin can register tools, network handlers, hooks, and services
*   a native plugin bug can crash or destabilize the gateway
*   a malicious native plugin is equivalent to arbitrary code execution inside the OpenClaw process

Compatible bundles are safer by default because OpenClaw currently treats them as metadata/content packs. In current releases, that mostly means bundled skills.Use allowlists and explicit install/load paths for non-bundled plugins. Treat workspace plugins as development-time code, not production defaults.For bundled workspace package names, keep the plugin id anchored in the npm name: `@openclaw/<id>` by default, or an approved typed suffix such as `-provider`, `-plugin`, `-speech`, `-sandbox`, or `-media-understanding` when the package intentionally exposes a narrower plugin role.Important trust note:
*   `plugins.allow` trusts **plugin ids**, not source provenance.
*   A workspace plugin with the same id as a bundled plugin intentionally shadows the bundled copy when that workspace plugin is enabled/allowlisted.
*   This is normal and useful for local development, patch testing, and hotfixes.

## [​](https://docs.openclaw.ai/plugins/architecture#export-boundary)

Export boundary

OpenClaw exports capabilities, not implementation convenience.Keep capability registration public. Trim non-contract helper exports:
*   bundled-plugin-specific helper subpaths
*   runtime plumbing subpaths not intended as public API
*   vendor-specific convenience helpers
*   setup/onboarding helpers that are implementation details

## [​](https://docs.openclaw.ai/plugins/architecture#load-pipeline)

Load pipeline

At startup, OpenClaw does roughly this:
1.   discover candidate plugin roots
2.   read native or compatible bundle manifests and package metadata
3.   reject unsafe candidates
4.   normalize plugin config (`plugins.enabled`, `allow`, `deny`, `entries`, `slots`, `load.paths`)
5.   decide enablement for each candidate
6.   load enabled native modules via jiti
7.   call native `register(api)` hooks and collect registrations into the plugin registry
8.   expose the registry to commands/runtime surfaces

The safety gates happen **before** runtime execution. Candidates are blocked when the entry escapes the plugin root, the path is world-writable, or path ownership looks suspicious for non-bundled plugins.
### [​](https://docs.openclaw.ai/plugins/architecture#manifest-first-behavior)

Manifest-first behavior

The manifest is the control-plane source of truth. OpenClaw uses it to:
*   identify the plugin
*   discover declared channels/skills/config schema or bundle capabilities
*   validate `plugins.entries.<id>.config`
*   augment Control UI labels/placeholders
*   show install/catalog metadata

For native plugins, the runtime module is the data-plane part. It registers actual behavior such as hooks, tools, commands, or provider flows.
### [​](https://docs.openclaw.ai/plugins/architecture#what-the-loader-caches)

What the loader caches

OpenClaw keeps short in-process caches for:
*   discovery results
*   manifest registry data
*   loaded plugin registries

These caches reduce bursty startup and repeated command overhead. They are safe to think of as short-lived performance caches, not persistence.Performance note:
*   Set `OPENCLAW_DISABLE_PLUGIN_DISCOVERY_CACHE=1` or `OPENCLAW_DISABLE_PLUGIN_MANIFEST_CACHE=1` to disable these caches.
*   Tune cache windows with `OPENCLAW_PLUGIN_DISCOVERY_CACHE_MS` and `OPENCLAW_PLUGIN_MANIFEST_CACHE_MS`.

## [​](https://docs.openclaw.ai/plugins/architecture#registry-model)

Registry model

Loaded plugins do not directly mutate random core globals. They register into a central plugin registry.The registry tracks:
*   plugin records (identity, source, origin, status, diagnostics)
*   tools
*   legacy hooks and typed hooks
*   channels
*   providers
*   gateway RPC handlers
*   HTTP routes
*   CLI registrars
*   background services
*   plugin-owned commands

Core features then read from that registry instead of talking to plugin modules directly. This keeps loading one-way:
*   plugin module -> registry registration
*   core runtime -> registry consumption

That separation matters for maintainability. It means most core surfaces only need one integration point: “read the registry”, not “special-case every plugin module”.
## [​](https://docs.openclaw.ai/plugins/architecture#conversation-binding-callbacks)

Conversation binding callbacks

Plugins that bind a conversation can react when an approval is resolved.Use `api.onConversationBindingResolved(...)` to receive a callback after a bind request is approved or denied:

```
export default {
  id: "my-plugin",
  register(api) {
    api.onConversationBindingResolved(async (event) => {
      if (event.status === "approved") {
        // A binding now exists for this plugin + conversation.
        console.log(event.binding?.conversationId);
        return;
      }

      // The request was denied; clear any local pending state.
      console.log(event.request.conversation.conversationId);
    });
  },
};
```

Callback payload fields:
*   `status`: `"approved"` or `"denied"`
*   `decision`: `"allow-once"`, `"allow-always"`, or `"deny"`
*   `binding`: the resolved binding for approved requests
*   `request`: the original request summary, detach hint, sender id, and conversation metadata

This callback is notification-only. It does not change who is allowed to bind a conversation, and it runs after core approval handling finishes.
## [​](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks)

Provider runtime hooks

Provider plugins now have two layers:
*   manifest metadata: `providerAuthEnvVars` for cheap env-auth lookup before runtime load, plus `providerAuthChoices` for cheap onboarding/auth-choice labels and CLI flag metadata before runtime load
*   config-time hooks: `catalog` / legacy `discovery`
*   runtime hooks: `resolveDynamicModel`, `prepareDynamicModel`, `normalizeResolvedModel`, `capabilities`, `prepareExtraParams`, `wrapStreamFn`, `formatApiKey`, `refreshOAuth`, `buildAuthDoctorHint`, `isCacheTtlEligible`, `buildMissingAuthMessage`, `suppressBuiltInModel`, `augmentModelCatalog`, `isBinaryThinking`, `supportsXHighThinking`, `resolveDefaultThinkingLevel`, `isModernModelRef`, `prepareRuntimeAuth`, `resolveUsageAuth`, `fetchUsageSnapshot`

OpenClaw still owns the generic agent loop, failover, transcript handling, and tool policy. These hooks are the extension surface for provider-specific behavior without needing a whole custom inference transport.Use manifest `providerAuthEnvVars` when the provider has env-based credentials that generic auth/status/model-picker paths should see without loading plugin runtime. Use manifest `providerAuthChoices` when onboarding/auth-choice CLI surfaces should know the provider’s choice id, group labels, and simple one-flag auth wiring without loading provider runtime. Keep provider runtime `envVars` for operator-facing hints such as onboarding labels or OAuth client-id/client-secret setup vars.
### [​](https://docs.openclaw.ai/plugins/architecture#hook-order-and-usage)

Hook order and usage

For model/provider plugins, OpenClaw calls hooks in this rough order. The “When to use” column is the quick decision guide.

| # | Hook | What it does | When to use |
| --- | --- | --- | --- |
| 1 | `catalog` | Publish provider config into `models.providers` during `models.json` generation | Provider owns a catalog or base URL defaults |
| — | _(built-in model lookup)_ | OpenClaw tries the normal registry/catalog path first | _(not a plugin hook)_ |
| 2 | `resolveDynamicModel` | Sync fallback for provider-owned model ids not in the local registry yet | Provider accepts arbitrary upstream model ids |
| 3 | `prepareDynamicModel` | Async warm-up, then `resolveDynamicModel` runs again | Provider needs network metadata before resolving unknown ids |
| 4 | `normalizeResolvedModel` | Final rewrite before the embedded runner uses the resolved model | Provider needs transport rewrites but still uses a core transport |
| 5 | `capabilities` | Provider-owned transcript/tooling metadata used by shared core logic | Provider needs transcript/provider-family quirks |
| 6 | `prepareExtraParams` | Request-param normalization before generic stream option wrappers | Provider needs default request params or per-provider param cleanup |
| 7 | `wrapStreamFn` | Stream wrapper after generic wrappers are applied | Provider needs request headers/body/model compat wrappers without a custom transport |
| 8 | `formatApiKey` | Auth-profile formatter: stored profile becomes the runtime `apiKey` string | Provider stores extra auth metadata and needs a custom runtime token shape |
| 9 | `refreshOAuth` | OAuth refresh override for custom refresh endpoints or refresh-failure policy | Provider does not fit the shared `pi-ai` refreshers |
| 10 | `buildAuthDoctorHint` | Repair hint appended when OAuth refresh fails | Provider needs provider-owned auth repair guidance after refresh failure |
| 11 | `isCacheTtlEligible` | Prompt-cache policy for proxy/backhaul providers | Provider needs proxy-specific cache TTL gating |
| 12 | `buildMissingAuthMessage` | Replacement for the generic missing-auth recovery message | Provider needs a provider-specific missing-auth recovery hint |
| 13 | `suppressBuiltInModel` | Stale upstream model suppression plus optional user-facing error hint | Provider needs to hide stale upstream rows or replace them with a vendor hint |
| 14 | `augmentModelCatalog` | Synthetic/final catalog rows appended after discovery | Provider needs synthetic forward-compat rows in `models list` and pickers |
| 15 | `isBinaryThinking` | On/off reasoning toggle for binary-thinking providers | Provider exposes only binary thinking on/off |
| 16 | `supportsXHighThinking` | `xhigh` reasoning support for selected models | Provider wants `xhigh` on only a subset of models |
| 17 | `resolveDefaultThinkingLevel` | Default `/think` level for a specific model family | Provider owns default `/think` policy for a model family |
| 18 | `isModernModelRef` | Modern-model matcher for live profile filters and smoke selection | Provider owns live/smoke preferred-model matching |
| 19 | `prepareRuntimeAuth` | Exchange a configured credential into the actual runtime token/key just before inference | Provider needs a token exchange or short-lived request credential |
| 20 | `resolveUsageAuth` | Resolve usage/billing credentials for `/usage` and related status surfaces | Provider needs custom usage/quota token parsing or a different usage credential |
| 21 | `fetchUsageSnapshot` | Fetch and normalize provider-specific usage/quota snapshots after auth is resolved | Provider needs a provider-specific usage endpoint or payload parser |

If the provider needs a fully custom wire protocol or custom request executor, that is a different class of extension. These hooks are for provider behavior that still runs on OpenClaw’s normal inference loop.
### [​](https://docs.openclaw.ai/plugins/architecture#provider-example)

Provider example

```
api.registerProvider({
  id: "example-proxy",
  label: "Example Proxy",
  auth: [],
  catalog: {
    order: "simple",
    run: async (ctx) => {
      const apiKey = ctx.resolveProviderApiKey("example-proxy").apiKey;
      if (!apiKey) {
        return null;
      }
      return {
        provider: {
          baseUrl: "https://proxy.example.com/v1",
          apiKey,
          api: "openai-completions",
          models: [{ id: "auto", name: "Auto" }],
        },
      };
    },
  },
  resolveDynamicModel: (ctx) => ({
    id: ctx.modelId,
    name: ctx.modelId,
    provider: "example-proxy",
    api: "openai-completions",
    baseUrl: "https://proxy.example.com/v1",
    reasoning: false,
    input: ["text"],
    cost: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
    contextWindow: 128000,
    maxTokens: 8192,
  }),
  prepareRuntimeAuth: async (ctx) => {
    const exchanged = await exchangeToken(ctx.apiKey);
    return {
      apiKey: exchanged.token,
      baseUrl: exchanged.baseUrl,
      expiresAt: exchanged.expiresAt,
    };
  },
  resolveUsageAuth: async (ctx) => {
    const auth = await ctx.resolveOAuthToken();
    return auth ? { token: auth.token } : null;
  },
  fetchUsageSnapshot: async (ctx) => {
    return await fetchExampleProxyUsage(ctx.token, ctx.timeoutMs, ctx.fetchFn);
  },
});
```

### [​](https://docs.openclaw.ai/plugins/architecture#built-in-examples)

Built-in examples

*   Anthropic uses `resolveDynamicModel`, `capabilities`, `buildAuthDoctorHint`, `resolveUsageAuth`, `fetchUsageSnapshot`, `isCacheTtlEligible`, `resolveDefaultThinkingLevel`, and `isModernModelRef` because it owns Claude 4.6 forward-compat, provider-family hints, auth repair guidance, usage endpoint integration, prompt-cache eligibility, and Claude default/adaptive thinking policy.
*   OpenAI uses `resolveDynamicModel`, `normalizeResolvedModel`, and `capabilities` plus `buildMissingAuthMessage`, `suppressBuiltInModel`, `augmentModelCatalog`, `supportsXHighThinking`, and `isModernModelRef` because it owns GPT-5.4 forward-compat, the direct OpenAI `openai-completions` ->`openai-responses` normalization, Codex-aware auth hints, Spark suppression, synthetic OpenAI list rows, and GPT-5 thinking / live-model policy.
*   OpenRouter uses `catalog` plus `resolveDynamicModel` and `prepareDynamicModel` because the provider is pass-through and may expose new model ids before OpenClaw’s static catalog updates; it also uses `capabilities`, `wrapStreamFn`, and `isCacheTtlEligible` to keep provider-specific request headers, routing metadata, reasoning patches, and prompt-cache policy out of core.
*   GitHub Copilot uses `catalog`, `auth`, `resolveDynamicModel`, and `capabilities` plus `prepareRuntimeAuth` and `fetchUsageSnapshot` because it needs provider-owned device login, model fallback behavior, Claude transcript quirks, a GitHub token -> Copilot token exchange, and a provider-owned usage endpoint.
*   OpenAI Codex uses `catalog`, `resolveDynamicModel`, `normalizeResolvedModel`, `refreshOAuth`, and `augmentModelCatalog` plus `prepareExtraParams`, `resolveUsageAuth`, and `fetchUsageSnapshot` because it still runs on core OpenAI transports but owns its transport/base URL normalization, OAuth refresh fallback policy, default transport choice, synthetic Codex catalog rows, and ChatGPT usage endpoint integration.
*   Google AI Studio and Gemini CLI OAuth use `resolveDynamicModel` and `isModernModelRef` because they own Gemini 3.1 forward-compat fallback and modern-model matching; Gemini CLI OAuth also uses `formatApiKey`, `resolveUsageAuth`, and `fetchUsageSnapshot` for token formatting, token parsing, and quota endpoint wiring.
*   Moonshot uses `catalog` plus `wrapStreamFn` because it still uses the shared OpenAI transport but needs provider-owned thinking payload normalization.
*   Kilocode uses `catalog`, `capabilities`, `wrapStreamFn`, and `isCacheTtlEligible` because it needs provider-owned request headers, reasoning payload normalization, Gemini transcript hints, and Anthropic cache-TTL gating.
*   Z.AI uses `resolveDynamicModel`, `prepareExtraParams`, `wrapStreamFn`, `isCacheTtlEligible`, `isBinaryThinking`, `isModernModelRef`, `resolveUsageAuth`, and `fetchUsageSnapshot` because it owns GLM-5 fallback, `tool_stream` defaults, binary thinking UX, modern-model matching, and both usage auth + quota fetching.
*   Mistral, OpenCode Zen, and OpenCode Go use `capabilities` only to keep transcript/tooling quirks out of core.
*   Catalog-only bundled providers such as `byteplus`, `cloudflare-ai-gateway`, `huggingface`, `kimi-coding`, `modelstudio`, `nvidia`, `qianfan`, `synthetic`, `together`, `venice`, `vercel-ai-gateway`, and `volcengine` use `catalog` only.
*   Qwen portal uses `catalog`, `auth`, and `refreshOAuth`.
*   MiniMax and Xiaomi use `catalog` plus usage hooks because their `/usage` behavior is plugin-owned even though inference still runs through the shared transports.

## [​](https://docs.openclaw.ai/plugins/architecture#runtime-helpers)

Runtime helpers

Plugins can access selected core helpers via `api.runtime`. For TTS:

```
const clip = await api.runtime.tts.textToSpeech({
  text: "Hello from OpenClaw",
  cfg: api.config,
});

const result = await api.runtime.tts.textToSpeechTelephony({
  text: "Hello from OpenClaw",
  cfg: api.config,
});

const voices = await api.runtime.tts.listVoices({
  provider: "elevenlabs",
  cfg: api.config,
});
```

Notes:
*   `textToSpeech` returns the normal core TTS output payload for file/voice-note surfaces.
*   Uses core `messages.tts` configuration and provider selection.
*   Returns PCM audio buffer + sample rate. Plugins must resample/encode for providers.
*   `listVoices` is optional per provider. Use it for vendor-owned voice pickers or setup flows.
*   Voice listings can include richer metadata such as locale, gender, and personality tags for provider-aware pickers.
*   OpenAI and ElevenLabs support telephony today. Microsoft does not.

Plugins can also register speech providers via `api.registerSpeechProvider(...)`.

```
api.registerSpeechProvider({
  id: "acme-speech",
  label: "Acme Speech",
  isConfigured: ({ config }) => Boolean(config.messages?.tts),
  synthesize: async (req) => {
    return {
      audioBuffer: Buffer.from([]),
      outputFormat: "mp3",
      fileExtension: ".mp3",
      voiceCompatible: false,
    };
  },
});
```

Notes:
*   Keep TTS policy, fallback, and reply delivery in core.
*   Use speech providers for vendor-owned synthesis behavior.
*   Legacy Microsoft `edge` input is normalized to the `microsoft` provider id.
*   The preferred ownership model is company-oriented: one vendor plugin can own text, speech, image, and future media providers as OpenClaw adds those capability contracts.

For image/audio/video understanding, plugins register one typed media-understanding provider instead of a generic key/value bag:

```
api.registerMediaUnderstandingProvider({
  id: "google",
  capabilities: ["image", "audio", "video"],
  describeImage: async (req) => ({ text: "..." }),
  transcribeAudio: async (req) => ({ text: "..." }),
  describeVideo: async (req) => ({ text: "..." }),
});
```

Notes:
*   Keep orchestration, fallback, config, and channel wiring in core.
*   Keep vendor behavior in the provider plugin.
*   Additive expansion should stay typed: new optional methods, new optional result fields, new optional capabilities.
*   If OpenClaw adds a new capability such as video generation later, define the core capability contract first, then let vendor plugins register against it.

For media-understanding runtime helpers, plugins can call:

```
const image = await api.runtime.mediaUnderstanding.describeImageFile({
  filePath: "/tmp/inbound-photo.jpg",
  cfg: api.config,
  agentDir: "/tmp/agent",
});

const video = await api.runtime.mediaUnderstanding.describeVideoFile({
  filePath: "/tmp/inbound-video.mp4",
  cfg: api.config,
});
```

For audio transcription, plugins can use either the media-understanding runtime or the older STT alias:

```
const { text } = await api.runtime.mediaUnderstanding.transcribeAudioFile({
  filePath: "/tmp/inbound-audio.ogg",
  cfg: api.config,
  // Optional when MIME cannot be inferred reliably:
  mime: "audio/ogg",
});
```

Notes:
*   `api.runtime.mediaUnderstanding.*` is the preferred shared surface for image/audio/video understanding.
*   Uses core media-understanding audio configuration (`tools.media.audio`) and provider fallback order.
*   Returns `{ text: undefined }` when no transcription output is produced (for example skipped/unsupported input).
*   `api.runtime.stt.transcribeAudioFile(...)` remains as a compatibility alias.

Plugins can also launch background subagent runs through `api.runtime.subagent`:

```
const result = await api.runtime.subagent.run({
  sessionKey: "agent:main:subagent:search-helper",
  message: "Expand this query into focused follow-up searches.",
  provider: "openai",
  model: "gpt-4.1-mini",
  deliver: false,
});
```

Notes:
*   `provider` and `model` are optional per-run overrides, not persistent session changes.
*   OpenClaw only honors those override fields for trusted callers.
*   For plugin-owned fallback runs, operators must opt in with `plugins.entries.<id>.subagent.allowModelOverride: true`.
*   Use `plugins.entries.<id>.subagent.allowedModels` to restrict trusted plugins to specific canonical `provider/model` targets, or `"*"` to allow any target explicitly.
*   Untrusted plugin subagent runs still work, but override requests are rejected instead of silently falling back.

For web search, plugins can consume the shared runtime helper instead of reaching into the agent tool wiring:

```
const providers = api.runtime.webSearch.listProviders({
  config: api.config,
});

const result = await api.runtime.webSearch.search({
  config: api.config,
  args: {
    query: "OpenClaw plugin runtime helpers",
    count: 5,
  },
});
```

Plugins can also register web-search providers via `api.registerWebSearchProvider(...)`.Notes:
*   Keep provider selection, credential resolution, and shared request semantics in core.
*   Use web-search providers for vendor-specific search transports.
*   `api.runtime.webSearch.*` is the preferred shared surface for feature/channel plugins that need search behavior without depending on the agent tool wrapper.

## [​](https://docs.openclaw.ai/plugins/architecture#gateway-http-routes)

Gateway HTTP routes

Plugins can expose HTTP endpoints with `api.registerHttpRoute(...)`.

```
api.registerHttpRoute({
  path: "/acme/webhook",
  auth: "plugin",
  match: "exact",
  handler: async (_req, res) => {
    res.statusCode = 200;
    res.end("ok");
    return true;
  },
});
```

Route fields:
*   `path`: route path under the gateway HTTP server.
*   `auth`: required. Use `"gateway"` to require normal gateway auth, or `"plugin"` for plugin-managed auth/webhook verification.
*   `match`: optional. `"exact"` (default) or `"prefix"`.
*   `replaceExisting`: optional. Allows the same plugin to replace its own existing route registration.
*   `handler`: return `true` when the route handled the request.

Notes:
*   `api.registerHttpHandler(...)` is obsolete. Use `api.registerHttpRoute(...)`.
*   Plugin routes must declare `auth` explicitly.
*   Exact `path + match` conflicts are rejected unless `replaceExisting: true`, and one plugin cannot replace another plugin’s route.
*   Overlapping routes with different `auth` levels are rejected. Keep `exact`/`prefix` fallthrough chains on the same auth level only.

## [​](https://docs.openclaw.ai/plugins/architecture#plugin-sdk-import-paths)

Plugin SDK import paths

Use SDK subpaths instead of the monolithic `openclaw/plugin-sdk` import when authoring plugins:
*   `openclaw/plugin-sdk/plugin-entry` for plugin registration primitives.
*   `openclaw/plugin-sdk/core` for the generic shared plugin-facing contract.
*   Stable channel primitives such as `openclaw/plugin-sdk/channel-setup`, `openclaw/plugin-sdk/channel-pairing`, `openclaw/plugin-sdk/channel-contract`, `openclaw/plugin-sdk/channel-feedback`, `openclaw/plugin-sdk/channel-inbound`, `openclaw/plugin-sdk/channel-lifecycle`, `openclaw/plugin-sdk/channel-reply-pipeline`, `openclaw/plugin-sdk/command-auth`, `openclaw/plugin-sdk/secret-input`, and `openclaw/plugin-sdk/webhook-ingress` for shared setup/auth/reply/webhook wiring. `channel-inbound` is the shared home for debounce, mention matching, envelope formatting, and inbound envelope context helpers.
*   Domain subpaths such as `openclaw/plugin-sdk/channel-config-helpers`, `openclaw/plugin-sdk/allow-from`, `openclaw/plugin-sdk/channel-config-schema`, `openclaw/plugin-sdk/channel-policy`, `openclaw/plugin-sdk/config-runtime`, `openclaw/plugin-sdk/infra-runtime`, `openclaw/plugin-sdk/agent-runtime`, `openclaw/plugin-sdk/lazy-runtime`, `openclaw/plugin-sdk/reply-history`, `openclaw/plugin-sdk/routing`, `openclaw/plugin-sdk/status-helpers`, `openclaw/plugin-sdk/runtime-store`, and `openclaw/plugin-sdk/directory-runtime` for shared runtime/config helpers.
*   `openclaw/plugin-sdk/channel-runtime` remains only as a compatibility shim. New code shoul
