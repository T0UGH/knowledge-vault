# platforms/mac/peekaboo

Source: https://docs.openclaw.ai/platforms/mac/peekaboo

Title: Peekaboo Bridge - OpenClaw

URL Source: https://docs.openclaw.ai/platforms/mac/peekaboo

Markdown Content:
# Peekaboo Bridge - OpenClaw

[Skip to main content](https://docs.openclaw.ai/platforms/mac/peekaboo#content-area)

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

macOS companion app

Peekaboo Bridge

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Platforms overview

*   [Platforms](https://docs.openclaw.ai/platforms)
*   [macOS App](https://docs.openclaw.ai/platforms/macos)
*   [Linux App](https://docs.openclaw.ai/platforms/linux)
*   [Windows](https://docs.openclaw.ai/platforms/windows)
*   [Android App](https://docs.openclaw.ai/platforms/android)
*   [iOS App](https://docs.openclaw.ai/platforms/ios)

##### macOS companion app

*   [macOS Dev Setup](https://docs.openclaw.ai/platforms/mac/dev-setup)
*   [Menu Bar](https://docs.openclaw.ai/platforms/mac/menu-bar)
*   [Voice Wake (macOS)](https://docs.openclaw.ai/platforms/mac/voicewake)
*   [Voice Overlay](https://docs.openclaw.ai/platforms/mac/voice-overlay)
*   [WebChat (macOS)](https://docs.openclaw.ai/platforms/mac/webchat)
*   [Canvas](https://docs.openclaw.ai/platforms/mac/canvas)
*   [Gateway Lifecycle](https://docs.openclaw.ai/platforms/mac/child-process)
*   [Health Checks (macOS)](https://docs.openclaw.ai/platforms/mac/health)
*   [Menu Bar Icon](https://docs.openclaw.ai/platforms/mac/icon)
*   [macOS Logging](https://docs.openclaw.ai/platforms/mac/logging)
*   [macOS Permissions](https://docs.openclaw.ai/platforms/mac/permissions)
*   [Remote Control](https://docs.openclaw.ai/platforms/mac/remote)
*   [macOS Signing](https://docs.openclaw.ai/platforms/mac/signing)
*   [Gateway on macOS](https://docs.openclaw.ai/platforms/mac/bundled-gateway)
*   [macOS IPC](https://docs.openclaw.ai/platforms/mac/xpc)
*   [Skills (macOS)](https://docs.openclaw.ai/platforms/mac/skills)
*   [Peekaboo Bridge](https://docs.openclaw.ai/platforms/mac/peekaboo)

On this page

*   [Peekaboo Bridge (macOS UI automation)](https://docs.openclaw.ai/platforms/mac/peekaboo#peekaboo-bridge-macos-ui-automation)
*   [What this is (and is not)](https://docs.openclaw.ai/platforms/mac/peekaboo#what-this-is-and-is-not)
*   [Enable the bridge](https://docs.openclaw.ai/platforms/mac/peekaboo#enable-the-bridge)
*   [Client discovery order](https://docs.openclaw.ai/platforms/mac/peekaboo#client-discovery-order)
*   [Security & permissions](https://docs.openclaw.ai/platforms/mac/peekaboo#security-%26-permissions)
*   [Snapshot behavior (automation)](https://docs.openclaw.ai/platforms/mac/peekaboo#snapshot-behavior-automation)
*   [Troubleshooting](https://docs.openclaw.ai/platforms/mac/peekaboo#troubleshooting)

macOS companion app

# Peekaboo Bridge

# [​](https://docs.openclaw.ai/platforms/mac/peekaboo#peekaboo-bridge-macos-ui-automation)

Peekaboo Bridge (macOS UI automation)

OpenClaw can host **PeekabooBridge** as a local, permission‑aware UI automation broker. This lets the `peekaboo` CLI drive UI automation while reusing the macOS app’s TCC permissions.
## [​](https://docs.openclaw.ai/platforms/mac/peekaboo#what-this-is-and-is-not)

What this is (and is not)

*   **Host**: OpenClaw.app can act as a PeekabooBridge host.
*   **Client**: use the `peekaboo` CLI (no separate `openclaw ui ...` surface).
*   **UI**: visual overlays stay in Peekaboo.app; OpenClaw is a thin broker host.

## [​](https://docs.openclaw.ai/platforms/mac/peekaboo#enable-the-bridge)

Enable the bridge

In the macOS app:
*   Settings → **Enable Peekaboo Bridge**

When enabled, OpenClaw starts a local UNIX socket server. If disabled, the host is stopped and `peekaboo` will fall back to other available hosts.
## [​](https://docs.openclaw.ai/platforms/mac/peekaboo#client-discovery-order)

Client discovery order

Peekaboo clients typically try hosts in this order:
1.   Peekaboo.app (full UX)
2.   Claude.app (if installed)
3.   OpenClaw.app (thin broker)

Use `peekaboo bridge status --verbose` to see which host is active and which socket path is in use. You can override with:

```
export PEEKABOO_BRIDGE_SOCKET=/path/to/bridge.sock
```

## [​](https://docs.openclaw.ai/platforms/mac/peekaboo#security-&-permissions)

Security & permissions

*   The bridge validates **caller code signatures**; an allowlist of TeamIDs is enforced (Peekaboo host TeamID + OpenClaw app TeamID).
*   Requests time out after ~10 seconds.
*   If required permissions are missing, the bridge returns a clear error message rather than launching System Settings.

## [​](https://docs.openclaw.ai/platforms/mac/peekaboo#snapshot-behavior-automation)

Snapshot behavior (automation)

Snapshots are stored in memory and expire automatically after a short window. If you need longer retention, re‑capture from the client.
## [​](https://docs.openclaw.ai/platforms/mac/peekaboo#troubleshooting)

Troubleshooting

*   If `peekaboo` reports “bridge client is not authorized”, ensure the client is properly signed or run the host with `PEEKABOO_ALLOW_UNSIGNED_SOCKET_CLIENTS=1` in **debug** mode only.
*   If no hosts are found, open one of the host apps (Peekaboo.app or OpenClaw.app) and confirm permissions are granted.

[Skills (macOS)](https://docs.openclaw.ai/platforms/mac/skills)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
