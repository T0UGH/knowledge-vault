# plugins/sdk-channel-plugins

Source: https://docs.openclaw.ai/plugins/sdk-channel-plugins

Title: Building Channel Plugins - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/sdk-channel-plugins

Markdown Content:
# Building Channel Plugins - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/sdk-channel-plugins#content-area)

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

Building Channel Plugins

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

*   [Building Channel Plugins](https://docs.openclaw.ai/plugins/sdk-channel-plugins#building-channel-plugins)
*   [How channel plugins work](https://docs.openclaw.ai/plugins/sdk-channel-plugins#how-channel-plugins-work)
*   [Walkthrough](https://docs.openclaw.ai/plugins/sdk-channel-plugins#walkthrough)
*   [File structure](https://docs.openclaw.ai/plugins/sdk-channel-plugins#file-structure)
*   [Advanced topics](https://docs.openclaw.ai/plugins/sdk-channel-plugins#advanced-topics)
*   [Next steps](https://docs.openclaw.ai/plugins/sdk-channel-plugins#next-steps)

Building Plugins

# Building Channel Plugins

# [​](https://docs.openclaw.ai/plugins/sdk-channel-plugins#building-channel-plugins)

Building Channel Plugins

This guide walks through building a channel plugin that connects OpenClaw to a messaging platform. By the end you will have a working channel with DM security, pairing, reply threading, and outbound messaging.

If you have not built any OpenClaw plugin before, read [Getting Started](https://docs.openclaw.ai/plugins/building-plugins) first for the basic package structure and manifest setup.

## [​](https://docs.openclaw.ai/plugins/sdk-channel-plugins#how-channel-plugins-work)

How channel plugins work

Channel plugins do not need their own send/edit/react tools. OpenClaw keeps one shared `message` tool in core. Your plugin owns:
*   **Config** — account resolution and setup wizard
*   **Security** — DM policy and allowlists
*   **Pairing** — DM approval flow
*   **Outbound** — sending text, media, and polls to the platform
*   **Threading** — how replies are threaded

Core owns the shared message tool, prompt wiring, session bookkeeping, and dispatch.
## [​](https://docs.openclaw.ai/plugins/sdk-channel-plugins#walkthrough)

Walkthrough

1

[](https://docs.openclaw.ai/plugins/sdk-channel-plugins#)

Package and manifest

Create the standard plugin files. The `channel` field in `package.json` is what makes this a channel plugin:

package.json

openclaw.plugin.json

```
{
  "name": "@myorg/openclaw-acme-chat",
  "version": "1.0.0",
  "type": "module",
  "openclaw": {
    "extensions": ["./index.ts"],
    "setupEntry": "./setup-entry.ts",
    "channel": {
      "id": "acme-chat",
      "label": "Acme Chat",
      "blurb": "Connect OpenClaw to Acme Chat."
    }
  }
}
```

2

[](https://docs.openclaw.ai/plugins/sdk-channel-plugins#)

Build the channel plugin object

The `ChannelPlugin` interface has many optional adapter surfaces. Start with the minimum — `id` and `setup` — and add adapters as you need them.Create `src/channel.ts`:

src/channel.ts

```
import {
  createChatChannelPlugin,
  createChannelPluginBase,
} from "openclaw/plugin-sdk/core";
import type { OpenClawConfig } from "openclaw/plugin-sdk/core";
import { acmeChatApi } from "./client.js"; // your platform API client

type ResolvedAccount = {
  accountId: string | null;
  token: string;
  allowFrom: string[];
  dmPolicy: string | undefined;
};

function resolveAccount(
  cfg: OpenClawConfig,
  accountId?: string | null,
): ResolvedAccount {
  const section = (cfg.channels as Record<string, any>)?.["acme-chat"];
  const token = section?.token;
  if (!token) throw new Error("acme-chat: token is required");
  return {
    accountId: accountId ?? null,
    token,
    allowFrom: section?.allowFrom ?? [],
    dmPolicy: section?.dmSecurity,
  };
}

export const acmeChatPlugin = createChatChannelPlugin<ResolvedAccount>({
  base: createChannelPluginBase({
    id: "acme-chat",
    setup: {
      resolveAccount,
      inspectAccount(cfg, accountId) {
        const section =
          (cfg.channels as Record<string, any>)?.["acme-chat"];
        return {
          enabled: Boolean(section?.token),
          configured: Boolean(section?.token),
          tokenStatus: section?.token ? "available" : "missing",
        };
      },
    },
  }),

  // DM security: who can message the bot
  security: {
    dm: {
      channelKey: "acme-chat",
      resolvePolicy: (account) => account.dmPolicy,
      resolveAllowFrom: (account) => account.allowFrom,
      defaultPolicy: "allowlist",
    },
  },

  // Pairing: approval flow for new DM contacts
  pairing: {
    text: {
      idLabel: "Acme Chat username",
      message: "Send this code to verify your identity:",
      notify: async ({ target, code }) => {
        await acmeChatApi.sendDm(target, `Pairing code: ${code}`);
      },
    },
  },

  // Threading: how replies are delivered
  threading: { topLevelReplyToMode: "reply" },

  // Outbound: send messages to the platform
  outbound: {
    attachedResults: {
      sendText: async (params) => {
        const result = await acmeChatApi.sendMessage(
          params.to,
          params.text,
        );
        return { messageId: result.id };
      },
    },
    base: {
      sendMedia: async (params) => {
        await acmeChatApi.sendFile(params.to, params.filePath);
      },
    },
  },
});
```

What createChatChannelPlugin does for you

Instead of implementing low-level adapter interfaces manually, you pass declarative options and the builder composes them:

| Option | What it wires |
| --- | --- |
| `security.dm` | Scoped DM security resolver from config fields |
| `pairing.text` | Text-based DM pairing flow with code exchange |
| `threading` | Reply-to-mode resolver (fixed, account-scoped, or custom) |
| `outbound.attachedResults` | Send functions that return result metadata (message IDs) |

You can also pass raw adapter objects instead of the declarative options if you need full control.

3

[](https://docs.openclaw.ai/plugins/sdk-channel-plugins#)

Wire the entry point

Create `index.ts`:

index.ts

```
import { defineChannelPluginEntry } from "openclaw/plugin-sdk/core";
import { acmeChatPlugin } from "./src/channel.js";

export default defineChannelPluginEntry({
  id: "acme-chat",
  name: "Acme Chat",
  description: "Acme Chat channel plugin",
  plugin: acmeChatPlugin,
  registerFull(api) {
    api.registerCli(
      ({ program }) => {
        program
          .command("acme-chat")
          .description("Acme Chat management");
      },
      { commands: ["acme-chat"] },
    );
  },
});
```

`defineChannelPluginEntry` handles the setup/full registration split automatically. See [Entry Points](https://docs.openclaw.ai/plugins/sdk-entrypoints#definechannelpluginentry) for all options.

4

[](https://docs.openclaw.ai/plugins/sdk-channel-plugins#)

Add a setup entry

Create `setup-entry.ts` for lightweight loading during onboarding:

setup-entry.ts

```
import { defineSetupPluginEntry } from "openclaw/plugin-sdk/core";
import { acmeChatPlugin } from "./src/channel.js";

export default defineSetupPluginEntry(acmeChatPlugin);
```

OpenClaw loads this instead of the full entry when the channel is disabled or unconfigured. It avoids pulling in heavy runtime code during setup flows. See [Setup and Config](https://docs.openclaw.ai/plugins/sdk-setup#setup-entry) for details.

5

[](https://docs.openclaw.ai/plugins/sdk-channel-plugins#)

Handle inbound messages

Your plugin needs to receive messages from the platform and forward them to OpenClaw. The typical pattern is a webhook that verifies the request and dispatches it through your channel’s inbound handler:

```
registerFull(api) {
  api.registerHttpRoute({
    path: "/acme-chat/webhook",
    auth: "plugin", // plugin-managed auth (verify signatures yourself)
    handler: async (req, res) => {
      const event = parseWebhookPayload(req);

      // Your inbound handler dispatches the message to OpenClaw.
      // The exact wiring depends on your platform SDK —
      // see a real example in extensions/msteams or extensions/googlechat.
      await handleAcmeChatInbound(api, event);

      res.statusCode = 200;
      res.end("ok");
      return true;
    },
  });
}
```

Inbound message handling is channel-specific. Each channel plugin owns its own inbound pipeline. Look at bundled channel plugins (e.g. `extensions/msteams`, `extensions/googlechat`) for real patterns.

6

[](https://docs.openclaw.ai/plugins/sdk-channel-plugins#)

Test

Write colocated tests in `src/channel.test.ts`:

src/channel.test.ts

```
import { describe, it, expect } from "vitest";
import { acmeChatPlugin } from "./channel.js";

describe("acme-chat plugin", () => {
  it("resolves account from config", () => {
    const cfg = {
      channels: {
        "acme-chat": { token: "test-token", allowFrom: ["user1"] },
      },
    } as any;
    const account = acmeChatPlugin.setup!.resolveAccount(cfg, undefined);
    expect(account.token).toBe("test-token");
  });

  it("inspects account without materializing secrets", () => {
    const cfg = {
      channels: { "acme-chat": { token: "test-token" } },
    } as any;
    const result = acmeChatPlugin.setup!.inspectAccount!(cfg, undefined);
    expect(result.configured).toBe(true);
    expect(result.tokenStatus).toBe("available");
  });

  it("reports missing config", () => {
    const cfg = { channels: {} } as any;
    const result = acmeChatPlugin.setup!.inspectAccount!(cfg, undefined);
    expect(result.configured).toBe(false);
  });
});
```

```
pnpm test -- extensions/acme-chat/
```

For shared test helpers, see [Testing](https://docs.openclaw.ai/plugins/sdk-testing).

## [​](https://docs.openclaw.ai/plugins/sdk-channel-plugins#file-structure)

File structure

```
extensions/acme-chat/
├── package.json              # openclaw.channel metadata
├── openclaw.plugin.json      # Manifest with config schema
├── index.ts                  # defineChannelPluginEntry
├── setup-entry.ts            # defineSetupPluginEntry
├── api.ts                    # Public exports (optional)
├── runtime-api.ts            # Internal runtime exports (optional)
└── src/
    ├── channel.ts            # ChannelPlugin via createChatChannelPlugin
    ├── channel.test.ts       # Tests
    ├── client.ts             # Platform API client
    └── runtime.ts            # Runtime store (if needed)
```

## [​](https://docs.openclaw.ai/plugins/sdk-channel-plugins#advanced-topics)

Advanced topics

## Threading options

Fixed, account-scoped, or custom reply modes

## Message tool integration

describeMessageTool and action discovery

## Target resolution

inferTargetChatType, looksLikeId, resolveTarget

## Runtime helpers

TTS, STT, media, subagent via api.runtime

## [​](https://docs.openclaw.ai/plugins/sdk-channel-plugins#next-steps)

Next steps

*   [Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins) — if your plugin also provides models
*   [SDK Overview](https://docs.openclaw.ai/plugins/sdk-overview) — full subpath import reference
*   [SDK Testing](https://docs.openclaw.ai/plugins/sdk-testing) — test utilities and contract tests
*   [Plugin Manifest](https://docs.openclaw.ai/plugins/manifest) — full manifest schema

[Getting Started](https://docs.openclaw.ai/plugins/building-plugins)[Provider Plugins](https://docs.openclaw.ai/plugins/sdk-provider-plugins)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
