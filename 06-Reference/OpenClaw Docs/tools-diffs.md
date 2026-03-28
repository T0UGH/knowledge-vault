# tools/diffs

Source: https://docs.openclaw.ai/tools/diffs

Title: Diffs - OpenClaw

URL Source: https://docs.openclaw.ai/tools/diffs

Markdown Content:
# Diffs - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/diffs#content-area)

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

Tools

Diffs

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

*   [Diffs](https://docs.openclaw.ai/tools/diffs#diffs)
*   [Quick start](https://docs.openclaw.ai/tools/diffs#quick-start)
*   [Enable the plugin](https://docs.openclaw.ai/tools/diffs#enable-the-plugin)
*   [Disable built-in system guidance](https://docs.openclaw.ai/tools/diffs#disable-built-in-system-guidance)
*   [Typical agent workflow](https://docs.openclaw.ai/tools/diffs#typical-agent-workflow)
*   [Input examples](https://docs.openclaw.ai/tools/diffs#input-examples)
*   [Tool input reference](https://docs.openclaw.ai/tools/diffs#tool-input-reference)
*   [Output details contract](https://docs.openclaw.ai/tools/diffs#output-details-contract)
*   [Collapsed unchanged sections](https://docs.openclaw.ai/tools/diffs#collapsed-unchanged-sections)
*   [Plugin defaults](https://docs.openclaw.ai/tools/diffs#plugin-defaults)
*   [Security config](https://docs.openclaw.ai/tools/diffs#security-config)
*   [Artifact lifecycle and storage](https://docs.openclaw.ai/tools/diffs#artifact-lifecycle-and-storage)
*   [Viewer URL and network behavior](https://docs.openclaw.ai/tools/diffs#viewer-url-and-network-behavior)
*   [Security model](https://docs.openclaw.ai/tools/diffs#security-model)
*   [Browser requirements for file mode](https://docs.openclaw.ai/tools/diffs#browser-requirements-for-file-mode)
*   [Troubleshooting](https://docs.openclaw.ai/tools/diffs#troubleshooting)
*   [Operational guidance](https://docs.openclaw.ai/tools/diffs#operational-guidance)
*   [Related docs](https://docs.openclaw.ai/tools/diffs#related-docs)

Tools

# Diffs

# [​](https://docs.openclaw.ai/tools/diffs#diffs)

Diffs

`diffs` is an optional plugin tool with short built-in system guidance and a companion skill that turns change content into a read-only diff artifact for agents.It accepts either:
*   `before` and `after` text
*   a unified `patch`

It can return:
*   a gateway viewer URL for canvas presentation
*   a rendered file path (PNG or PDF) for message delivery
*   both outputs in one call

When enabled, the plugin prepends concise usage guidance into system-prompt space and also exposes a detailed skill for cases where the agent needs fuller instructions.
## [​](https://docs.openclaw.ai/tools/diffs#quick-start)

Quick start

1.   Enable the plugin.
2.   Call `diffs` with `mode: "view"` for canvas-first flows.
3.   Call `diffs` with `mode: "file"` for chat file delivery flows.
4.   Call `diffs` with `mode: "both"` when you need both artifacts.

## [​](https://docs.openclaw.ai/tools/diffs#enable-the-plugin)

Enable the plugin

```
{
  plugins: {
    entries: {
      diffs: {
        enabled: true,
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/tools/diffs#disable-built-in-system-guidance)

Disable built-in system guidance

If you want to keep the `diffs` tool enabled but disable its built-in system-prompt guidance, set `plugins.entries.diffs.hooks.allowPromptInjection` to `false`:

```
{
  plugins: {
    entries: {
      diffs: {
        enabled: true,
        hooks: {
          allowPromptInjection: false,
        },
      },
    },
  },
}
```

This blocks the diffs plugin’s `before_prompt_build` hook while keeping the plugin, tool, and companion skill available.If you want to disable both the guidance and the tool, disable the plugin instead.
## [​](https://docs.openclaw.ai/tools/diffs#typical-agent-workflow)

Typical agent workflow

1.   Agent calls `diffs`.
2.   Agent reads `details` fields.
3.   Agent either:
    *   opens `details.viewerUrl` with `canvas present`
    *   sends `details.filePath` with `message` using `path` or `filePath`
    *   does both

## [​](https://docs.openclaw.ai/tools/diffs#input-examples)

Input examples

Before and after:

```
{
  "before": "# Hello\n\nOne",
  "after": "# Hello\n\nTwo",
  "path": "docs/example.md",
  "mode": "view"
}
```

Patch:

```
{
  "patch": "diff --git a/src/example.ts b/src/example.ts\n--- a/src/example.ts\n+++ b/src/example.ts\n@@ -1 +1 @@\n-const x = 1;\n+const x = 2;\n",
  "mode": "both"
}
```

## [​](https://docs.openclaw.ai/tools/diffs#tool-input-reference)

Tool input reference

All fields are optional unless noted:
*   `before` (`string`): original text. Required with `after` when `patch` is omitted.
*   `after` (`string`): updated text. Required with `before` when `patch` is omitted.
*   `patch` (`string`): unified diff text. Mutually exclusive with `before` and `after`.
*   `path` (`string`): display filename for before and after mode.
*   `lang` (`string`): language override hint for before and after mode.
*   `title` (`string`): viewer title override.
*   `mode` (`"view" | "file" | "both"`): output mode. Defaults to plugin default `defaults.mode`. Deprecated alias: `"image"` behaves like `"file"` and is still accepted for backward compatibility.
*   `theme` (`"light" | "dark"`): viewer theme. Defaults to plugin default `defaults.theme`.
*   `layout` (`"unified" | "split"`): diff layout. Defaults to plugin default `defaults.layout`.
*   `expandUnchanged` (`boolean`): expand unchanged sections when full context is available. Per-call option only (not a plugin default key).
*   `fileFormat` (`"png" | "pdf"`): rendered file format. Defaults to plugin default `defaults.fileFormat`.
*   `fileQuality` (`"standard" | "hq" | "print"`): quality preset for PNG or PDF rendering.
*   `fileScale` (`number`): device scale override (`1`-`4`).
*   `fileMaxWidth` (`number`): max render width in CSS pixels (`640`-`2400`).
*   `ttlSeconds` (`number`): viewer artifact TTL in seconds. Default 1800, max 21600.
*   `baseUrl` (`string`): viewer URL origin override. Must be `http` or `https`, no query/hash.

Validation and limits:
*   `before` and `after` each max 512 KiB.
*   `patch` max 2 MiB.
*   `path` max 2048 bytes.
*   `lang` max 128 bytes.
*   `title` max 1024 bytes.
*   Patch complexity cap: max 128 files and 120000 total lines.
*   `patch` and `before` or `after` together are rejected.
*   Rendered file safety limits (apply to PNG and PDF):
    *   `fileQuality: "standard"`: max 8 MP (8,000,000 rendered pixels).
    *   `fileQuality: "hq"`: max 14 MP (14,000,000 rendered pixels).
    *   `fileQuality: "print"`: max 24 MP (24,000,000 rendered pixels).
    *   PDF also has a max of 50 pages.

## [​](https://docs.openclaw.ai/tools/diffs#output-details-contract)

Output details contract

The tool returns structured metadata under `details`.Shared fields for modes that create a viewer:
*   `artifactId`
*   `viewerUrl`
*   `viewerPath`
*   `title`
*   `expiresAt`
*   `inputKind`
*   `fileCount`
*   `mode`
*   `context` (`agentId`, `sessionId`, `messageChannel`, `agentAccountId` when available)

File fields when PNG or PDF is rendered:
*   `artifactId`
*   `expiresAt`
*   `filePath`
*   `path` (same value as `filePath`, for message tool compatibility)
*   `fileBytes`
*   `fileFormat`
*   `fileQuality`
*   `fileScale`
*   `fileMaxWidth`

Mode behavior summary:
*   `mode: "view"`: viewer fields only.
*   `mode: "file"`: file fields only, no viewer artifact.
*   `mode: "both"`: viewer fields plus file fields. If file rendering fails, viewer still returns with `fileError`.

## [​](https://docs.openclaw.ai/tools/diffs#collapsed-unchanged-sections)

Collapsed unchanged sections

*   The viewer can show rows like `N unmodified lines`.
*   Expand controls on those rows are conditional and not guaranteed for every input kind.
*   Expand controls appear when the rendered diff has expandable context data, which is typical for before and after input.
*   For many unified patch inputs, omitted context bodies are not available in the parsed patch hunks, so the row can appear without expand controls. This is expected behavior.
*   `expandUnchanged` applies only when expandable context exists.

## [​](https://docs.openclaw.ai/tools/diffs#plugin-defaults)

Plugin defaults

Set plugin-wide defaults in `~/.openclaw/openclaw.json`:

```
{
  plugins: {
    entries: {
      diffs: {
        enabled: true,
        config: {
          defaults: {
            fontFamily: "Fira Code",
            fontSize: 15,
            lineSpacing: 1.6,
            layout: "unified",
            showLineNumbers: true,
            diffIndicators: "bars",
            wordWrap: true,
            background: true,
            theme: "dark",
            fileFormat: "png",
            fileQuality: "standard",
            fileScale: 2,
            fileMaxWidth: 960,
            mode: "both",
          },
        },
      },
    },
  },
}
```

Supported defaults:
*   `fontFamily`
*   `fontSize`
*   `lineSpacing`
*   `layout`
*   `showLineNumbers`
*   `diffIndicators`
*   `wordWrap`
*   `background`
*   `theme`
*   `fileFormat`
*   `fileQuality`
*   `fileScale`
*   `fileMaxWidth`
*   `mode`

Explicit tool parameters override these defaults.
## [​](https://docs.openclaw.ai/tools/diffs#security-config)

Security config

*   `security.allowRemoteViewer` (`boolean`, default `false`)
    *   `false`: non-loopback requests to viewer routes are denied.
    *   `true`: remote viewers are allowed if tokenized path is valid.

Example:

```
{
  plugins: {
    entries: {
      diffs: {
        enabled: true,
        config: {
          security: {
            allowRemoteViewer: false,
          },
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/tools/diffs#artifact-lifecycle-and-storage)

Artifact lifecycle and storage

*   Artifacts are stored under the temp subfolder: `$TMPDIR/openclaw-diffs`.
*   Viewer artifact metadata contains:
    *   random artifact ID (20 hex chars)
    *   random token (48 hex chars)
    *   `createdAt` and `expiresAt`
    *   stored `viewer.html` path

*   Default viewer TTL is 30 minutes when not specified.
*   Maximum accepted viewer TTL is 6 hours.
*   Cleanup runs opportunistically after artifact creation.
*   Expired artifacts are deleted.
*   Fallback cleanup removes stale folders older than 24 hours when metadata is missing.

## [​](https://docs.openclaw.ai/tools/diffs#viewer-url-and-network-behavior)

Viewer URL and network behavior

Viewer route:
*   `/plugins/diffs/view/{artifactId}/{token}`

Viewer assets:
*   `/plugins/diffs/assets/viewer.js`
*   `/plugins/diffs/assets/viewer-runtime.js`

URL construction behavior:
*   If `baseUrl` is provided, it is used after strict validation.
*   Without `baseUrl`, viewer URL defaults to loopback `127.0.0.1`.
*   If gateway bind mode is `custom` and `gateway.customBindHost` is set, that host is used.

`baseUrl` rules:
*   Must be `http://` or `https://`.
*   Query and hash are rejected.
*   Origin plus optional base path is allowed.

## [​](https://docs.openclaw.ai/tools/diffs#security-model)

Security model

Viewer hardening:
*   Loopback-only by default.
*   Tokenized viewer paths with strict ID and token validation.
*   Viewer response CSP:
    *   `default-src 'none'`
    *   scripts and assets only from self
    *   no outbound `connect-src`

*   Remote miss throttling when remote access is enabled:
    *   40 failures per 60 seconds
    *   60 second lockout (`429 Too Many Requests`)

File rendering hardening:
*   Screenshot browser request routing is deny-by-default.
*   Only local viewer assets from `http://127.0.0.1/plugins/diffs/assets/*` are allowed.
*   External network requests are blocked.

## [​](https://docs.openclaw.ai/tools/diffs#browser-requirements-for-file-mode)

Browser requirements for file mode

`mode: "file"` and `mode: "both"` need a Chromium-compatible browser.Resolution order:
1.   `browser.executablePath` in OpenClaw config.
2.   Environment variables:
    *   `OPENCLAW_BROWSER_EXECUTABLE_PATH`
    *   `BROWSER_EXECUTABLE_PATH`
    *   `PLAYWRIGHT_CHROMIUM_EXECUTABLE_PATH`

3.   Platform command/path discovery fallback.

Common failure text:
*   `Diff PNG/PDF rendering requires a Chromium-compatible browser...`

Fix by installing Chrome, Chromium, Edge, or Brave, or setting one of the executable path options above.
## [​](https://docs.openclaw.ai/tools/diffs#troubleshooting)

Troubleshooting

Input validation errors:
*   `Provide patch or both before and after text.`
    *   Include both `before` and `after`, or provide `patch`.

*   `Provide either patch or before/after input, not both.`
    *   Do not mix input modes.

*   `Invalid baseUrl: ...`
    *   Use `http(s)` origin with optional path, no query/hash.

*   `{field} exceeds maximum size (...)`
    *   Reduce payload size.

*   Large patch rejection
    *   Reduce patch file count or total lines.

Viewer accessibility issues:
*   Viewer URL resolves to `127.0.0.1` by default.
*   For remote access scenarios, either:
    *   pass `baseUrl` per tool call, or
    *   use `gateway.bind=custom` and `gateway.customBindHost`

*   Enable `security.allowRemoteViewer` only when you intend external viewer access.

Unmodified-lines row has no expand button:
*   This can happen for patch input when the patch does not carry expandable context.
*   This is expected and does not indicate a viewer failure.

Artifact not found:
*   Artifact expired due TTL.
*   Token or path changed.
*   Cleanup removed stale data.

## [​](https://docs.openclaw.ai/tools/diffs#operational-guidance)

Operational guidance

*   Prefer `mode: "view"` for local interactive reviews in canvas.
*   Prefer `mode: "file"` for outbound chat channels that need an attachment.
*   Keep `allowRemoteViewer` disabled unless your deployment requires remote viewer URLs.
*   Set explicit short `ttlSeconds` for sensitive diffs.
*   Avoid sending secrets in diff input when not required.
*   If your channel compresses images aggressively (for example Telegram or WhatsApp), prefer PDF output (`fileFormat: "pdf"`).

Diff rendering engine:
*   Powered by [Diffs](https://diffs.com/).

## [​](https://docs.openclaw.ai/tools/diffs#related-docs)

Related docs

*   [Tools overview](https://docs.openclaw.ai/tools)
*   [Plugins](https://docs.openclaw.ai/tools/plugin)
*   [Browser](https://docs.openclaw.ai/tools/browser)

[BTW Side Questions](https://docs.openclaw.ai/tools/btw)[Elevated Mode](https://docs.openclaw.ai/tools/elevated)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
