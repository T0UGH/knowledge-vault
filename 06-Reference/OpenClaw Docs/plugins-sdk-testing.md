# plugins/sdk-testing

Source: https://docs.openclaw.ai/plugins/sdk-testing

Title: Plugin Testing - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/sdk-testing

Markdown Content:
# Plugin Testing - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/sdk-testing#content-area)

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

Plugin Testing

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

*   [Plugin Testing](https://docs.openclaw.ai/plugins/sdk-testing#plugin-testing)
*   [Test utilities](https://docs.openclaw.ai/plugins/sdk-testing#test-utilities)
*   [Available exports](https://docs.openclaw.ai/plugins/sdk-testing#available-exports)
*   [Types](https://docs.openclaw.ai/plugins/sdk-testing#types)
*   [Testing target resolution](https://docs.openclaw.ai/plugins/sdk-testing#testing-target-resolution)
*   [Testing patterns](https://docs.openclaw.ai/plugins/sdk-testing#testing-patterns)
*   [Unit testing a channel plugin](https://docs.openclaw.ai/plugins/sdk-testing#unit-testing-a-channel-plugin)
*   [Unit testing a provider plugin](https://docs.openclaw.ai/plugins/sdk-testing#unit-testing-a-provider-plugin)
*   [Mocking the plugin runtime](https://docs.openclaw.ai/plugins/sdk-testing#mocking-the-plugin-runtime)
*   [Testing with per-instance stubs](https://docs.openclaw.ai/plugins/sdk-testing#testing-with-per-instance-stubs)
*   [Contract tests (in-repo plugins)](https://docs.openclaw.ai/plugins/sdk-testing#contract-tests-in-repo-plugins)
*   [Running scoped tests](https://docs.openclaw.ai/plugins/sdk-testing#running-scoped-tests)
*   [Lint enforcement (in-repo plugins)](https://docs.openclaw.ai/plugins/sdk-testing#lint-enforcement-in-repo-plugins)
*   [Test configuration](https://docs.openclaw.ai/plugins/sdk-testing#test-configuration)
*   [Related](https://docs.openclaw.ai/plugins/sdk-testing#related)

SDK Reference

# Plugin Testing

# [​](https://docs.openclaw.ai/plugins/sdk-testing#plugin-testing)

Plugin Testing

Reference for test utilities, patterns, and lint enforcement for OpenClaw plugins.

**Looking for test examples?** The how-to guides include worked test examples: [Channel plugin tests](https://docs.openclaw.ai/plugins/sdk-channel-plugins#step-6-test) and [Provider plugin tests](https://docs.openclaw.ai/plugins/sdk-provider-plugins#step-6-test).

## [​](https://docs.openclaw.ai/plugins/sdk-testing#test-utilities)

Test utilities

**Import:**`openclaw/plugin-sdk/testing`The testing subpath exports a narrow set of helpers for plugin authors:

```
import {
  installCommonResolveTargetErrorCases,
  shouldAckReaction,
  removeAckReactionAfterReply,
} from "openclaw/plugin-sdk/testing";
```

### [​](https://docs.openclaw.ai/plugins/sdk-testing#available-exports)

Available exports

| Export | Purpose |
| --- | --- |
| `installCommonResolveTargetErrorCases` | Shared test cases for target resolution error handling |
| `shouldAckReaction` | Check whether a channel should add an ack reaction |
| `removeAckReactionAfterReply` | Remove ack reaction after reply delivery |

### [​](https://docs.openclaw.ai/plugins/sdk-testing#types)

Types

The testing subpath also re-exports types useful in test files:

```
import type {
  ChannelAccountSnapshot,
  ChannelGatewayContext,
  OpenClawConfig,
  PluginRuntime,
  RuntimeEnv,
  MockFn,
} from "openclaw/plugin-sdk/testing";
```

## [​](https://docs.openclaw.ai/plugins/sdk-testing#testing-target-resolution)

Testing target resolution

Use `installCommonResolveTargetErrorCases` to add standard error cases for channel target resolution:

```
import { describe } from "vitest";
import { installCommonResolveTargetErrorCases } from "openclaw/plugin-sdk/testing";

describe("my-channel target resolution", () => {
  installCommonResolveTargetErrorCases({
    resolveTarget: ({ to, mode, allowFrom }) => {
      // Your channel's target resolution logic
      return myChannelResolveTarget({ to, mode, allowFrom });
    },
    implicitAllowFrom: ["user1", "user2"],
  });

  // Add channel-specific test cases
  it("should resolve @username targets", () => {
    // ...
  });
});
```

## [​](https://docs.openclaw.ai/plugins/sdk-testing#testing-patterns)

Testing patterns

### [​](https://docs.openclaw.ai/plugins/sdk-testing#unit-testing-a-channel-plugin)

Unit testing a channel plugin

```
import { describe, it, expect, vi } from "vitest";

describe("my-channel plugin", () => {
  it("should resolve account from config", () => {
    const cfg = {
      channels: {
        "my-channel": {
          token: "test-token",
          allowFrom: ["user1"],
        },
      },
    };

    const account = myPlugin.setup.resolveAccount(cfg, undefined);
    expect(account.token).toBe("test-token");
  });

  it("should inspect account without materializing secrets", () => {
    const cfg = {
      channels: {
        "my-channel": { token: "test-token" },
      },
    };

    const inspection = myPlugin.setup.inspectAccount(cfg, undefined);
    expect(inspection.configured).toBe(true);
    expect(inspection.tokenStatus).toBe("available");
    // No token value exposed
    expect(inspection).not.toHaveProperty("token");
  });
});
```

### [​](https://docs.openclaw.ai/plugins/sdk-testing#unit-testing-a-provider-plugin)

Unit testing a provider plugin

```
import { describe, it, expect } from "vitest";

describe("my-provider plugin", () => {
  it("should resolve dynamic models", () => {
    const model = myProvider.resolveDynamicModel({
      modelId: "custom-model-v2",
      // ... context
    });

    expect(model.id).toBe("custom-model-v2");
    expect(model.provider).toBe("my-provider");
    expect(model.api).toBe("openai-completions");
  });

  it("should return catalog when API key is available", async () => {
    const result = await myProvider.catalog.run({
      resolveProviderApiKey: () => ({ apiKey: "test-key" }),
      // ... context
    });

    expect(result?.provider?.models).toHaveLength(2);
  });
});
```

### [​](https://docs.openclaw.ai/plugins/sdk-testing#mocking-the-plugin-runtime)

Mocking the plugin runtime

For code that uses `createPluginRuntimeStore`, mock the runtime in tests:

```
import { createPluginRuntimeStore } from "openclaw/plugin-sdk/runtime-store";
import type { PluginRuntime } from "openclaw/plugin-sdk/runtime-store";

const store = createPluginRuntimeStore<PluginRuntime>("test runtime not set");

// In test setup
const mockRuntime = {
  agent: {
    resolveAgentDir: vi.fn().mockReturnValue("/tmp/agent"),
    // ... other mocks
  },
  config: {
    loadConfig: vi.fn(),
    writeConfigFile: vi.fn(),
  },
  // ... other namespaces
} as unknown as PluginRuntime;

store.setRuntime(mockRuntime);

// After tests
store.clearRuntime();
```

### [​](https://docs.openclaw.ai/plugins/sdk-testing#testing-with-per-instance-stubs)

Testing with per-instance stubs

Prefer per-instance stubs over prototype mutation:

```
// Preferred: per-instance stub
const client = new MyChannelClient();
client.sendMessage = vi.fn().mockResolvedValue({ id: "msg-1" });

// Avoid: prototype mutation
// MyChannelClient.prototype.sendMessage = vi.fn();
```

## [​](https://docs.openclaw.ai/plugins/sdk-testing#contract-tests-in-repo-plugins)

Contract tests (in-repo plugins)

Bundled plugins have contract tests that verify registration ownership:

```
pnpm test -- src/plugins/contracts/
```

These tests assert:
*   Which plugins register which providers
*   Which plugins register which speech providers
*   Registration shape correctness
*   Runtime contract compliance

### [​](https://docs.openclaw.ai/plugins/sdk-testing#running-scoped-tests)

Running scoped tests

For a specific plugin:

```
pnpm test -- extensions/my-channel/
```

For contract tests only:

```
pnpm test -- src/plugins/contracts/shape.contract.test.ts
pnpm test -- src/plugins/contracts/auth.contract.test.ts
pnpm test -- src/plugins/contracts/runtime.contract.test.ts
```

## [​](https://docs.openclaw.ai/plugins/sdk-testing#lint-enforcement-in-repo-plugins)

Lint enforcement (in-repo plugins)

Three rules are enforced by `pnpm check` for in-repo plugins:
1.   **No monolithic root imports** — `openclaw/plugin-sdk` root barrel is rejected
2.   **No direct `src/` imports** — plugins cannot import `../../src/` directly
3.   **No self-imports** — plugins cannot import their own `plugin-sdk/<name>` subpath

External plugins are not subject to these lint rules, but following the same patterns is recommended.
## [​](https://docs.openclaw.ai/plugins/sdk-testing#test-configuration)

Test configuration

OpenClaw uses Vitest with V8 coverage thresholds. For plugin tests:

```
# Run all tests
pnpm test

# Run specific plugin tests
pnpm test -- extensions/my-channel/src/channel.test.ts

# Run with a specific test name filter
pnpm test -- extensions/my-channel/ -t "resolves account"

# Run with coverage
pnpm test:coverage
```

If local runs cause memory pressure:

```
OPENCLAW_TEST_PROFILE=low OPENCLAW_TEST_SERIAL_GATEWAY=1 pnpm test
```

## [​](https://docs.openclaw.ai/plugins/sdk-testing#related)

Related

*   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview) — import conventions
*   [SDK Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins) — channel plugin interface
*   [SDK Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) — provider plugin hooks
*   [Building Plugins](https://docs.openclaw.ai/plugins/building-plugins) — getting started guide

[Setup and Config](https://docs.openclaw.ai/plugins/sdk-setup)[Plugin Manifest](https://docs.openclaw.ai/plugins/manifest)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
