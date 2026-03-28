# plugins/sdk-provider-plugins

Source: https://docs.openclaw.ai/plugins/sdk-provider-plugins

Title: Building Provider Plugins - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/sdk-provider-plugins

Markdown Content:
# Building Provider Plugins - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/sdk-provider-plugins#content-area)

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

Building Provider Plugins

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

*   [Building Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins#building-provider-plugins)
*   [Walkthrough](https://docs.openclaw.ai/plugins/sdk-provider-plugins#walkthrough)
*   [File structure](https://docs.openclaw.ai/plugins/sdk-provider-plugins#file-structure)
*   [Catalog order reference](https://docs.openclaw.ai/plugins/sdk-provider-plugins#catalog-order-reference)
*   [Next steps](https://docs.openclaw.ai/plugins/sdk-provider-plugins#next-steps)

Building Plugins

# Building Provider Plugins

# [​](https://docs.openclaw.ai/plugins/sdk-provider-plugins#building-provider-plugins)

Building Provider Plugins

This guide walks through building a provider plugin that adds a model provider (LLM) to OpenClaw. By the end you will have a provider with a model catalog, API key auth, and dynamic model resolution.

If you have not built any OpenClaw plugin before, read [Getting Started](https://docs.openclaw.ai/plugins/building-plugins) first for the basic package structure and manifest setup.

## [​](https://docs.openclaw.ai/plugins/sdk-provider-plugins#walkthrough)

Walkthrough

1

[](https://docs.openclaw.ai/plugins/sdk-provider-plugins#)

Package and manifest

package.json

openclaw.plugin.json

```
{
  "name": "@myorg/openclaw-acme-ai",
  "version": "1.0.0",
  "type": "module",
  "openclaw": {
    "extensions": ["./index.ts"],
    "providers": ["acme-ai"]
  }
}
```

The manifest declares `providerAuthEnvVars` so OpenClaw can detect credentials without loading your plugin runtime.

2

[](https://docs.openclaw.ai/plugins/sdk-provider-plugins#)

Register the provider

A minimal provider needs an `id`, `label`, `auth`, and `catalog`:

index.ts

```
import { definePluginEntry } from "openclaw/plugin-sdk/plugin-entry";
import { createProviderApiKeyAuthMethod } from "openclaw/plugin-sdk/provider-auth";

export default definePluginEntry({
  id: "acme-ai",
  name: "Acme AI",
  description: "Acme AI model provider",
  register(api) {
    api.registerProvider({
      id: "acme-ai",
      label: "Acme AI",
      docsPath: "/providers/acme-ai",
      envVars: ["ACME_AI_API_KEY"],

      auth: [
        createProviderApiKeyAuthMethod({
          providerId: "acme-ai",
          methodId: "api-key",
          label: "Acme AI API key",
          hint: "API key from your Acme AI dashboard",
          optionKey: "acmeAiApiKey",
          flagName: "--acme-ai-api-key",
          envVar: "ACME_AI_API_KEY",
          promptMessage: "Enter your Acme AI API key",
          defaultModel: "acme-ai/acme-large",
        }),
      ],

      catalog: {
        order: "simple",
        run: async (ctx) => {
          const apiKey =
            ctx.resolveProviderApiKey("acme-ai").apiKey;
          if (!apiKey) return null;
          return {
            provider: {
              baseUrl: "https://api.acme-ai.com/v1",
              apiKey,
              api: "openai-completions",
              models: [
                {
                  id: "acme-large",
                  name: "Acme Large",
                  reasoning: true,
                  input: ["text", "image"],
                  cost: { input: 3, output: 15, cacheRead: 0.3, cacheWrite: 3.75 },
                  contextWindow: 200000,
                  maxTokens: 32768,
                },
                {
                  id: "acme-small",
                  name: "Acme Small",
                  reasoning: false,
                  input: ["text"],
                  cost: { input: 1, output: 5, cacheRead: 0.1, cacheWrite: 1.25 },
                  contextWindow: 128000,
                  maxTokens: 8192,
                },
              ],
            },
          };
        },
      },
    });
  },
});
```

That is a working provider. Users can now `openclaw onboard --acme-ai-api-key <key>` and select `acme-ai/acme-large` as their model.For bundled providers that only register one text provider with API-key auth plus a single catalog-backed runtime, prefer the narrower `defineSingleProviderPluginEntry(...)` helper:

```
import { defineSingleProviderPluginEntry } from "openclaw/plugin-sdk/provider-entry";

export default defineSingleProviderPluginEntry({
  id: "acme-ai",
  name: "Acme AI",
  description: "Acme AI model provider",
  provider: {
    label: "Acme AI",
    docsPath: "/providers/acme-ai",
    auth: [
      {
        methodId: "api-key",
        label: "Acme AI API key",
        hint: "API key from your Acme AI dashboard",
        optionKey: "acmeAiApiKey",
        flagName: "--acme-ai-api-key",
        envVar: "ACME_AI_API_KEY",
        promptMessage: "Enter your Acme AI API key",
        defaultModel: "acme-ai/acme-large",
      },
    ],
    catalog: {
      buildProvider: () => ({
        api: "openai-completions",
        baseUrl: "https://api.acme-ai.com/v1",
        models: [{ id: "acme-large", name: "Acme Large" }],
      }),
    },
  },
});
```

If your auth flow also needs to patch `models.providers.*`, aliases, and the agent default model during onboarding, use the preset helpers from `openclaw/plugin-sdk/provider-onboard`. The narrowest helpers are `createDefaultModelPresetAppliers(...)`, `createDefaultModelsPresetAppliers(...)`, and `createModelCatalogPresetAppliers(...)`.

3

[](https://docs.openclaw.ai/plugins/sdk-provider-plugins#)

Add dynamic model resolution

If your provider accepts arbitrary model IDs (like a proxy or router), add `resolveDynamicModel`:

```
api.registerProvider({
  // ... id, label, auth, catalog from above

  resolveDynamicModel: (ctx) => ({
    id: ctx.modelId,
    name: ctx.modelId,
    provider: "acme-ai",
    api: "openai-completions",
    baseUrl: "https://api.acme-ai.com/v1",
    reasoning: false,
    input: ["text"],
    cost: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
    contextWindow: 128000,
    maxTokens: 8192,
  }),
});
```

If resolving requires a network call, use `prepareDynamicModel` for async warm-up — `resolveDynamicModel` runs again after it completes.

4

[](https://docs.openclaw.ai/plugins/sdk-provider-plugins#)

Add runtime hooks (as needed)

Most providers only need `catalog` + `resolveDynamicModel`. Add hooks incrementally as your provider requires them.

*   Token exchange 
*   Custom headers 
*   Usage and billing 

For providers that need a token exchange before each inference call:

```
prepareRuntimeAuth: async (ctx) => {
  const exchanged = await exchangeToken(ctx.apiKey);
  return {
    apiKey: exchanged.token,
    baseUrl: exchanged.baseUrl,
    expiresAt: exchanged.expiresAt,
  };
},
```

For providers that need custom request headers or body modifications:

```
// wrapStreamFn returns a StreamFn derived from ctx.streamFn
wrapStreamFn: (ctx) => {
  if (!ctx.streamFn) return undefined;
  const inner = ctx.streamFn;
  return async (params) => {
    params.headers = {
      ...params.headers,
      "X-Acme-Version": "2",
    };
    return inner(params);
  };
},
```

For providers that expose usage/billing data:

```
resolveUsageAuth: async (ctx) => {
  const auth = await ctx.resolveOAuthToken();
  return auth ? { token: auth.token } : null;
},
fetchUsageSnapshot: async (ctx) => {
  return await fetchAcmeUsage(ctx.token, ctx.timeoutMs);
},
```

All available provider hooks

OpenClaw calls hooks in this order. Most providers only use 2-3:

| # | Hook | When to use |
| --- | --- | --- |
| 1 | `catalog` | Model catalog or base URL defaults |
| 2 | `resolveDynamicModel` | Accept arbitrary upstream model IDs |
| 3 | `prepareDynamicModel` | Async metadata fetch before resolving |
| 4 | `normalizeResolvedModel` | Transport rewrites before the runner |
| 5 | `capabilities` | Transcript/tooling metadata (data, not callable) |
| 6 | `prepareExtraParams` | Default request params |
| 7 | `wrapStreamFn` | Custom headers/body wrappers |
| 8 | `formatApiKey` | Custom runtime token shape |
| 9 | `refreshOAuth` | Custom OAuth refresh |
| 10 | `buildAuthDoctorHint` | Auth repair guidance |
| 11 | `isCacheTtlEligible` | Prompt cache TTL gating |
| 12 | `buildMissingAuthMessage` | Custom missing-auth hint |
| 13 | `suppressBuiltInModel` | Hide stale upstream rows |
| 14 | `augmentModelCatalog` | Synthetic forward-compat rows |
| 15 | `isBinaryThinking` | Binary thinking on/off |
| 16 | `supportsXHighThinking` | `xhigh` reasoning support |
| 17 | `resolveDefaultThinkingLevel` | Default `/think` policy |
| 18 | `isModernModelRef` | Live/smoke model matching |
| 19 | `prepareRuntimeAuth` | Token exchange before inference |
| 20 | `resolveUsageAuth` | Custom usage credential parsing |
| 21 | `fetchUsageSnapshot` | Custom usage endpoint |
| 22 | `onModelSelected` | Post-selection callback (e.g. telemetry) |

For detailed descriptions and real-world examples, see [Internals: Provider Runtime Hooks](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks).

5

[](https://docs.openclaw.ai/plugins/sdk-provider-plugins#)

Add extra capabilities (optional)

A provider plugin can register speech, media understanding, image generation, and web search alongside text inference:

```
register(api) {
  api.registerProvider({ id: "acme-ai", /* ... */ });

  api.registerSpeechProvider({
    id: "acme-ai",
    label: "Acme Speech",
    isConfigured: ({ config }) => Boolean(config.messages?.tts),
    synthesize: async (req) => ({
      audioBuffer: Buffer.from(/* PCM data */),
      outputFormat: "mp3",
      fileExtension: ".mp3",
      voiceCompatible: false,
    }),
  });

  api.registerMediaUnderstandingProvider({
    id: "acme-ai",
    capabilities: ["image", "audio"],
    describeImage: async (req) => ({ text: "A photo of..." }),
    transcribeAudio: async (req) => ({ text: "Transcript..." }),
  });

  api.registerImageGenerationProvider({
    id: "acme-ai",
    label: "Acme Images",
    generate: async (req) => ({ /* image result */ }),
  });
}
```

OpenClaw classifies this as a **hybrid-capability** plugin. This is the recommended pattern for company plugins (one plugin per vendor). See [Internals: Capability Ownership](https://docs.openclaw.ai/plugins/architecture#capability-ownership-model).

6

[](https://docs.openclaw.ai/plugins/sdk-provider-plugins#)

Test

src/provider.test.ts

```
import { describe, it, expect } from "vitest";
// Export your provider config object from index.ts or a dedicated file
import { acmeProvider } from "./provider.js";

describe("acme-ai provider", () => {
  it("resolves dynamic models", () => {
    const model = acmeProvider.resolveDynamicModel!({
      modelId: "acme-beta-v3",
    } as any);
    expect(model.id).toBe("acme-beta-v3");
    expect(model.provider).toBe("acme-ai");
  });

  it("returns catalog when key is available", async () => {
    const result = await acmeProvider.catalog!.run({
      resolveProviderApiKey: () => ({ apiKey: "test-key" }),
    } as any);
    expect(result?.provider?.models).toHaveLength(2);
  });

  it("returns null catalog when no key", async () => {
    const result = await acmeProvider.catalog!.run({
      resolveProviderApiKey: () => ({ apiKey: undefined }),
    } as any);
    expect(result).toBeNull();
  });
});
```

## [​](https://docs.openclaw.ai/plugins/sdk-provider-plugins#file-structure)

File structure

```
extensions/acme-ai/
├── package.json              # openclaw.providers metadata
├── openclaw.plugin.json      # Manifest with providerAuthEnvVars
├── index.ts                  # definePluginEntry + registerProvider
└── src/
    ├── provider.test.ts      # Tests
    └── usage.ts              # Usage endpoint (optional)
```

## [​](https://docs.openclaw.ai/plugins/sdk-provider-plugins#catalog-order-reference)

Catalog order reference

`catalog.order` controls when your catalog merges relative to built-in providers:

| Order | When | Use case |
| --- | --- | --- |
| `simple` | First pass | Plain API-key providers |
| `profile` | After simple | Providers gated on auth profiles |
| `paired` | After profile | Synthesize multiple related entries |
| `late` | Last pass | Override existing providers (wins on collision) |

## [​](https://docs.openclaw.ai/plugins/sdk-provider-plugins#next-steps)

Next steps

*   [Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) — if your plugin also provides a channel
*   [SDK Runtime](https://docs.openclaw.ai/plugins/sdk-runtime) — `api.runtime` helpers (TTS, search, subagent)
*   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview) — full subpath import reference
*   [Plugin Internals](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks) — hook details and bundled examples

[Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins)[Migrate to SDK](https://docs.openclaw.ai/plugins/sdk-migration)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
