# tools/browser-wsl2-windows-remote-cdp-troubleshooting

Source: https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting

Title: WSL2 + Windows + remote Chrome CDP troubleshooting - OpenClaw

URL Source: https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting

Markdown Content:
# WSL2 + Windows + remote Chrome CDP troubleshooting - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#content-area)

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

Web Browser

WSL2 + Windows + remote Chrome CDP troubleshooting

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
    *   [Browser (OpenClaw-managed)](https://docs.openclaw.ai/tools/browser)
    *   [Browser Login](https://docs.openclaw.ai/tools/browser-login)
    *   [Browser Troubleshooting](https://docs.openclaw.ai/tools/browser-linux-troubleshooting)
    *   [WSL2 + Windows + remote Chrome CDP troubleshooting](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting)

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

*   [WSL2 + Windows + remote Chrome CDP troubleshooting](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#wsl2-%2B-windows-%2B-remote-chrome-cdp-troubleshooting)
*   [Choose the right browser mode first](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#choose-the-right-browser-mode-first)
*   [Option 1: Raw remote CDP from WSL2 to Windows](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#option-1-raw-remote-cdp-from-wsl2-to-windows)
*   [Option 2: Host-local Chrome MCP](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#option-2-host-local-chrome-mcp)
*   [Working architecture](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#working-architecture)
*   [Why this setup is confusing](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#why-this-setup-is-confusing)
*   [Critical rule for the Control UI](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#critical-rule-for-the-control-ui)
*   [Validate in layers](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#validate-in-layers)
*   [Layer 1: Verify Chrome is serving CDP on Windows](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-1-verify-chrome-is-serving-cdp-on-windows)
*   [Layer 2: Verify WSL2 can reach that Windows endpoint](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-2-verify-wsl2-can-reach-that-windows-endpoint)
*   [Layer 3: Configure the correct browser profile](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-3-configure-the-correct-browser-profile)
*   [Layer 4: Verify the Control UI layer separately](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-4-verify-the-control-ui-layer-separately)
*   [Layer 5: Verify end-to-end browser control](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-5-verify-end-to-end-browser-control)
*   [Common misleading errors](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#common-misleading-errors)
*   [Fast triage checklist](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#fast-triage-checklist)
*   [Practical takeaway](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#practical-takeaway)

Web Browser

# WSL2 + Windows + remote Chrome CDP troubleshooting

# [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#wsl2-+-windows-+-remote-chrome-cdp-troubleshooting)

WSL2 + Windows + remote Chrome CDP troubleshooting

This guide covers the common split-host setup where:
*   OpenClaw Gateway runs inside WSL2
*   Chrome runs on Windows
*   browser control must cross the WSL2/Windows boundary

It also covers the layered failure pattern from [issue #39369](https://github.com/openclaw/openclaw/issues/39369): several independent problems can show up at once, which makes the wrong layer look broken first.
## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#choose-the-right-browser-mode-first)

Choose the right browser mode first

You have two valid patterns:
### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#option-1-raw-remote-cdp-from-wsl2-to-windows)

Option 1: Raw remote CDP from WSL2 to Windows

Use a remote browser profile that points from WSL2 to a Windows Chrome CDP endpoint.Choose this when:
*   the Gateway stays inside WSL2
*   Chrome runs on Windows
*   you need browser control to cross the WSL2/Windows boundary

### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#option-2-host-local-chrome-mcp)

Option 2: Host-local Chrome MCP

Use `existing-session` / `user` only when the Gateway itself runs on the same host as Chrome.Choose this when:
*   OpenClaw and Chrome are on the same machine
*   you want the local signed-in browser state
*   you do not need cross-host browser transport

For WSL2 Gateway + Windows Chrome, prefer raw remote CDP. Chrome MCP is host-local, not a WSL2-to-Windows bridge.
## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#working-architecture)

Working architecture

Reference shape:
*   WSL2 runs the Gateway on `127.0.0.1:18789`
*   Windows opens the Control UI in a normal browser at `http://127.0.0.1:18789/`
*   Windows Chrome exposes a CDP endpoint on port `9222`
*   WSL2 can reach that Windows CDP endpoint
*   OpenClaw points a browser profile at the address that is reachable from WSL2

## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#why-this-setup-is-confusing)

Why this setup is confusing

Several failures can overlap:
*   WSL2 cannot reach the Windows CDP endpoint
*   the Control UI is opened from a non-secure origin
*   `gateway.controlUi.allowedOrigins` does not match the page origin
*   token or pairing is missing
*   the browser profile points at the wrong address

Because of that, fixing one layer can still leave a different error visible.
## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#critical-rule-for-the-control-ui)

Critical rule for the Control UI

When the UI is opened from Windows, use Windows localhost unless you have a deliberate HTTPS setup.Use:`http://127.0.0.1:18789/`Do not default to a LAN IP for the Control UI. Plain HTTP on a LAN or tailnet address can trigger insecure-origin/device-auth behavior that is unrelated to CDP itself. See [Control UI](https://docs.openclaw.ai/web/control-ui).
## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#validate-in-layers)

Validate in layers

Work top to bottom. Do not skip ahead.
### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-1-verify-chrome-is-serving-cdp-on-windows)

Layer 1: Verify Chrome is serving CDP on Windows

Start Chrome on Windows with remote debugging enabled:

```
chrome.exe --remote-debugging-port=9222
```

From Windows, verify Chrome itself first:

```
curl http://127.0.0.1:9222/json/version
curl http://127.0.0.1:9222/json/list
```

If this fails on Windows, OpenClaw is not the problem yet.
### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-2-verify-wsl2-can-reach-that-windows-endpoint)

Layer 2: Verify WSL2 can reach that Windows endpoint

From WSL2, test the exact address you plan to use in `cdpUrl`:

```
curl http://WINDOWS_HOST_OR_IP:9222/json/version
curl http://WINDOWS_HOST_OR_IP:9222/json/list
```

Good result:
*   `/json/version` returns JSON with Browser / Protocol-Version metadata
*   `/json/list` returns JSON (empty array is fine if no pages are open)

If this fails:
*   Windows is not exposing the port to WSL2 yet
*   the address is wrong for the WSL2 side
*   firewall / port forwarding / local proxying is still missing

Fix that before touching OpenClaw config.
### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-3-configure-the-correct-browser-profile)

Layer 3: Configure the correct browser profile

For raw remote CDP, point OpenClaw at the address that is reachable from WSL2:

```
{
  browser: {
    enabled: true,
    defaultProfile: "remote",
    profiles: {
      remote: {
        cdpUrl: "http://WINDOWS_HOST_OR_IP:9222",
        attachOnly: true,
        color: "#00AA00",
      },
    },
  },
}
```

Notes:
*   use the WSL2-reachable address, not whatever only works on Windows
*   keep `attachOnly: true` for externally managed browsers
*   test the same URL with `curl` before expecting OpenClaw to succeed

### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-4-verify-the-control-ui-layer-separately)

Layer 4: Verify the Control UI layer separately

Open the UI from Windows:`http://127.0.0.1:18789/`Then verify:
*   the page origin matches what `gateway.controlUi.allowedOrigins` expects
*   token auth or pairing is configured correctly
*   you are not debugging a Control UI auth problem as if it were a browser problem

Helpful page:
*   [Control UI](https://docs.openclaw.ai/web/control-ui)

### [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#layer-5-verify-end-to-end-browser-control)

Layer 5: Verify end-to-end browser control

From WSL2:

```
openclaw browser open https://example.com --browser-profile remote
openclaw browser tabs --browser-profile remote
```

Good result:
*   the tab opens in Windows Chrome
*   `openclaw browser tabs` returns the target
*   later actions (`snapshot`, `screenshot`, `navigate`) work from the same profile

## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#common-misleading-errors)

Common misleading errors

Treat each message as a layer-specific clue:
*   `control-ui-insecure-auth`
    *   UI origin / secure-context problem, not a CDP transport problem

*   `token_missing`
    *   auth configuration problem

*   `pairing required`
    *   device approval problem

*   `Remote CDP for profile "remote" is not reachable`
    *   WSL2 cannot reach the configured `cdpUrl`

*   `gateway timeout after 1500ms`
    *   often still CDP reachability or a slow/unreachable remote endpoint

*   `No Chrome tabs found for profile="user"`
    *   local Chrome MCP profile selected where no host-local tabs are available

## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#fast-triage-checklist)

Fast triage checklist

1.   Windows: does `curl http://127.0.0.1:9222/json/version` work?
2.   WSL2: does `curl http://WINDOWS_HOST_OR_IP:9222/json/version` work?
3.   OpenClaw config: does `browser.profiles.<name>.cdpUrl` use that exact WSL2-reachable address?
4.   Control UI: are you opening `http://127.0.0.1:18789/` instead of a LAN IP?
5.   Are you trying to use `existing-session` across WSL2 and Windows instead of raw remote CDP?

## [​](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting#practical-takeaway)

Practical takeaway

The setup is usually viable. The hard part is that browser transport, Control UI origin security, and token/pairing can each fail independently while looking similar from the user side.When in doubt:
*   verify the Windows Chrome endpoint locally first
*   verify the same endpoint from WSL2 second
*   only then debug OpenClaw config or Control UI auth

[Browser Troubleshooting](https://docs.openclaw.ai/tools/browser-linux-troubleshooting)[Web Fetch](https://docs.openclaw.ai/tools/web-fetch)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
