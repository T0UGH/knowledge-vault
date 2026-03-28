# cli/browser

Source: https://docs.openclaw.ai/cli/browser

Title: browser - OpenClaw

URL Source: https://docs.openclaw.ai/cli/browser

Markdown Content:
# browser - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/browser#content-area)

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

Tools and execution

browser

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
*   Channels and messaging 
*   Tools and execution 
    *   [approvals](https://docs.openclaw.ai/cli/approvals)
    *   [browser](https://docs.openclaw.ai/cli/browser)
    *   [cron](https://docs.openclaw.ai/cli/cron)
    *   [node](https://docs.openclaw.ai/cli/node)
    *   [nodes](https://docs.openclaw.ai/cli/nodes)
    *   [Sandbox CLI](https://docs.openclaw.ai/cli/sandbox)

*   Configuration 
*   Plugins and skills 
*   Interfaces 
*   Utility 

##### RPC and API

*   [RPC Adapters](https://docs.openclaw.ai/reference/rpc)
*   [Device Model Database](https://docs.openclaw.ai/reference/device-models)

##### Templates

*   [Default AGENTS.md](https://docs.openclaw.ai/reference/AGENTS.default)
*   [AGENTS.md Template](https://docs.openclaw.ai/reference/templates/AGENTS)
*   [BOOT.md Template](https://docs.openclaw.ai/reference/templates/BOOT)
*   [BOOTSTRAP.md Template](https://docs.openclaw.ai/reference/templates/BOOTSTRAP)
*   [HEARTBEAT.md Template](https://docs.openclaw.ai/reference/templates/HEARTBEAT)
*   [IDENTITY Template](https://docs.openclaw.ai/reference/templates/IDENTITY)
*   [SOUL.md Template](https://docs.openclaw.ai/reference/templates/SOUL)
*   [TOOLS.md Template](https://docs.openclaw.ai/reference/templates/TOOLS)
*   [USER Template](https://docs.openclaw.ai/reference/templates/USER)

##### Technical reference

*   [Pi Integration Architecture](https://docs.openclaw.ai/pi)
*   [Onboarding Reference](https://docs.openclaw.ai/reference/wizard)
*   [Token Use and Costs](https://docs.openclaw.ai/reference/token-use)
*   [SecretRef Credential Surface](https://docs.openclaw.ai/reference/secretref-credential-surface)
*   [Prompt Caching](https://docs.openclaw.ai/reference/prompt-caching)
*   [API Usage and Costs](https://docs.openclaw.ai/reference/api-usage-costs)
*   [Transcript Hygiene](https://docs.openclaw.ai/reference/transcript-hygiene)
*   [Memory configuration reference](https://docs.openclaw.ai/reference/memory-config)
*   [Date and Time](https://docs.openclaw.ai/date-time)

##### Concept internals

*   [TypeBox](https://docs.openclaw.ai/concepts/typebox)
*   [Markdown Formatting](https://docs.openclaw.ai/concepts/markdown-formatting)
*   [Typing Indicators](https://docs.openclaw.ai/concepts/typing-indicators)
*   [Usage Tracking](https://docs.openclaw.ai/concepts/usage-tracking)
*   [Timezones](https://docs.openclaw.ai/concepts/timezone)

##### Project

*   [Credits](https://docs.openclaw.ai/reference/credits)

##### Release policy

*   [Release Policy](https://docs.openclaw.ai/reference/RELEASING)
*   [Tests](https://docs.openclaw.ai/reference/test)

On this page

*   [openclaw browser](https://docs.openclaw.ai/cli/browser#openclaw-browser)
*   [Common flags](https://docs.openclaw.ai/cli/browser#common-flags)
*   [Quick start (local)](https://docs.openclaw.ai/cli/browser#quick-start-local)
*   [Profiles](https://docs.openclaw.ai/cli/browser#profiles)
*   [Tabs](https://docs.openclaw.ai/cli/browser#tabs)
*   [Snapshot / screenshot / actions](https://docs.openclaw.ai/cli/browser#snapshot-%2F-screenshot-%2F-actions)
*   [Existing Chrome via MCP](https://docs.openclaw.ai/cli/browser#existing-chrome-via-mcp)
*   [Remote browser control (node host proxy)](https://docs.openclaw.ai/cli/browser#remote-browser-control-node-host-proxy)

Tools and execution

# browser

# [​](https://docs.openclaw.ai/cli/browser#openclaw-browser)

`openclaw browser`

Manage OpenClaw’s browser control server and run browser actions (tabs, snapshots, screenshots, navigation, clicks, typing).Related:
*   Browser tool + API: [Browser tool](https://docs.openclaw.ai/tools/browser)

## [​](https://docs.openclaw.ai/cli/browser#common-flags)

Common flags

*   `--url <gatewayWsUrl>`: Gateway WebSocket URL (defaults to config).
*   `--token <token>`: Gateway token (if required).
*   `--timeout <ms>`: request timeout (ms).
*   `--browser-profile <name>`: choose a browser profile (default from config).
*   `--json`: machine-readable output (where supported).

## [​](https://docs.openclaw.ai/cli/browser#quick-start-local)

Quick start (local)

```
openclaw browser profiles
openclaw browser --browser-profile openclaw start
openclaw browser --browser-profile openclaw open https://example.com
openclaw browser --browser-profile openclaw snapshot
```

## [​](https://docs.openclaw.ai/cli/browser#profiles)

Profiles

Profiles are named browser routing configs. In practice:
*   `openclaw`: launches or attaches to a dedicated OpenClaw-managed Chrome instance (isolated user data dir).
*   `user`: controls your existing signed-in Chrome session via Chrome DevTools MCP.
*   custom CDP profiles: point at a local or remote CDP endpoint.

```
openclaw browser profiles
openclaw browser create-profile --name work --color "#FF5A36"
openclaw browser create-profile --name chrome-live --driver existing-session
openclaw browser delete-profile --name work
```

Use a specific profile:

```
openclaw browser --browser-profile work tabs
```

## [​](https://docs.openclaw.ai/cli/browser#tabs)

Tabs

```
openclaw browser tabs
openclaw browser open https://docs.openclaw.ai
openclaw browser focus <targetId>
openclaw browser close <targetId>
```

## [​](https://docs.openclaw.ai/cli/browser#snapshot-/-screenshot-/-actions)

Snapshot / screenshot / actions

Snapshot:

```
openclaw browser snapshot
```

Screenshot:

```
openclaw browser screenshot
```

Navigate/click/type (ref-based UI automation):

```
openclaw browser navigate https://example.com
openclaw browser click <ref>
openclaw browser type <ref> "hello"
```

## [​](https://docs.openclaw.ai/cli/browser#existing-chrome-via-mcp)

Existing Chrome via MCP

Use the built-in `user` profile, or create your own `existing-session` profile:

```
openclaw browser --browser-profile user tabs
openclaw browser create-profile --name chrome-live --driver existing-session
openclaw browser create-profile --name brave-live --driver existing-session --user-data-dir "~/Library/Application Support/BraveSoftware/Brave-Browser"
openclaw browser --browser-profile chrome-live tabs
```

This path is host-only. For Docker, headless servers, Browserless, or other remote setups, use a CDP profile instead.
## [​](https://docs.openclaw.ai/cli/browser#remote-browser-control-node-host-proxy)

Remote browser control (node host proxy)

If the Gateway runs on a different machine than the browser, run a **node host** on the machine that has Chrome/Brave/Edge/Chromium. The Gateway will proxy browser actions to that node (no separate browser control server required).Use `gateway.nodes.browser.mode` to control auto-routing and `gateway.nodes.browser.node` to pin a specific node if multiple are connected.Security + remote setup: [Browser tool](https://docs.openclaw.ai/tools/browser), [Remote access](https://docs.openclaw.ai/gateway/remote), [Tailscale](https://docs.openclaw.ai/gateway/tailscale), [Security](https://docs.openclaw.ai/gateway/security)

[approvals](https://docs.openclaw.ai/cli/approvals)[cron](https://docs.openclaw.ai/cli/cron)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
