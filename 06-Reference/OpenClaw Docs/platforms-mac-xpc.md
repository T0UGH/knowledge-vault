# platforms/mac/xpc

Source: https://docs.openclaw.ai/platforms/mac/xpc

Title: macOS IPC - OpenClaw

URL Source: https://docs.openclaw.ai/platforms/mac/xpc

Markdown Content:
# macOS IPC - OpenClaw

[Skip to main content](https://docs.openclaw.ai/platforms/mac/xpc#content-area)

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

macOS IPC

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

*   [OpenClaw macOS IPC architecture](https://docs.openclaw.ai/platforms/mac/xpc#openclaw-macos-ipc-architecture)
*   [Goals](https://docs.openclaw.ai/platforms/mac/xpc#goals)
*   [How it works](https://docs.openclaw.ai/platforms/mac/xpc#how-it-works)
*   [Gateway + node transport](https://docs.openclaw.ai/platforms/mac/xpc#gateway-%2B-node-transport)
*   [Node service + app IPC](https://docs.openclaw.ai/platforms/mac/xpc#node-service-%2B-app-ipc)
*   [PeekabooBridge (UI automation)](https://docs.openclaw.ai/platforms/mac/xpc#peekaboobridge-ui-automation)
*   [Operational flows](https://docs.openclaw.ai/platforms/mac/xpc#operational-flows)
*   [Hardening notes](https://docs.openclaw.ai/platforms/mac/xpc#hardening-notes)

macOS companion app

# macOS IPC

# [​](https://docs.openclaw.ai/platforms/mac/xpc#openclaw-macos-ipc-architecture)

OpenClaw macOS IPC architecture

**Current model:** a local Unix socket connects the **node host service** to the **macOS app** for exec approvals + `system.run`. A `openclaw-mac` debug CLI exists for discovery/connect checks; agent actions still flow through the Gateway WebSocket and `node.invoke`. UI automation uses PeekabooBridge.
## [​](https://docs.openclaw.ai/platforms/mac/xpc#goals)

Goals

*   Single GUI app instance that owns all TCC-facing work (notifications, screen recording, mic, speech, AppleScript).
*   A small surface for automation: Gateway + node commands, plus PeekabooBridge for UI automation.
*   Predictable permissions: always the same signed bundle ID, launched by launchd, so TCC grants stick.

## [​](https://docs.openclaw.ai/platforms/mac/xpc#how-it-works)

How it works

### [​](https://docs.openclaw.ai/platforms/mac/xpc#gateway-+-node-transport)

Gateway + node transport

*   The app runs the Gateway (local mode) and connects to it as a node.
*   Agent actions are performed via `node.invoke` (e.g. `system.run`, `system.notify`, `canvas.*`).

### [​](https://docs.openclaw.ai/platforms/mac/xpc#node-service-+-app-ipc)

Node service + app IPC

*   A headless node host service connects to the Gateway WebSocket.
*   `system.run` requests are forwarded to the macOS app over a local Unix socket.
*   The app performs the exec in UI context, prompts if needed, and returns output.

Diagram (SCI):

```
Agent -> Gateway -> Node Service (WS)
                      |  IPC (UDS + token + HMAC + TTL)
                      v
                  Mac App (UI + TCC + system.run)
```

### [​](https://docs.openclaw.ai/platforms/mac/xpc#peekaboobridge-ui-automation)

PeekabooBridge (UI automation)

*   UI automation uses a separate UNIX socket named `bridge.sock` and the PeekabooBridge JSON protocol.
*   Host preference order (client-side): Peekaboo.app → Claude.app → OpenClaw.app → local execution.
*   Security: bridge hosts require an allowed TeamID; DEBUG-only same-UID escape hatch is guarded by `PEEKABOO_ALLOW_UNSIGNED_SOCKET_CLIENTS=1` (Peekaboo convention).
*   See: [PeekabooBridge usage](https://docs.openclaw.ai/platforms/mac/peekaboo) for details.

## [​](https://docs.openclaw.ai/platforms/mac/xpc#operational-flows)

Operational flows

*   Restart/rebuild: `SIGN_IDENTITY="Apple Development: <Developer Name> (<TEAMID>)" scripts/restart-mac.sh`
    *   Kills existing instances
    *   Swift build + package
    *   Writes/bootstraps/kickstarts the LaunchAgent

*   Single instance: app exits early if another instance with the same bundle ID is running.

## [​](https://docs.openclaw.ai/platforms/mac/xpc#hardening-notes)

Hardening notes

*   Prefer requiring a TeamID match for all privileged surfaces.
*   PeekabooBridge: `PEEKABOO_ALLOW_UNSIGNED_SOCKET_CLIENTS=1` (DEBUG-only) may allow same-UID callers for local development.
*   All communication remains local-only; no network sockets are exposed.
*   TCC prompts originate only from the GUI app bundle; keep the signed bundle ID stable across rebuilds.
*   IPC hardening: socket mode `0600`, token, peer-UID checks, HMAC challenge/response, short TTL.

[Gateway on macOS](https://docs.openclaw.ai/platforms/mac/bundled-gateway)[Skills (macOS)](https://docs.openclaw.ai/platforms/mac/skills)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
