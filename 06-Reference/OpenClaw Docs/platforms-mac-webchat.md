# platforms/mac/webchat

Source: https://docs.openclaw.ai/platforms/mac/webchat

Title: WebChat (macOS) - OpenClaw

URL Source: https://docs.openclaw.ai/platforms/mac/webchat

Markdown Content:
# WebChat (macOS) - OpenClaw

[Skip to main content](https://docs.openclaw.ai/platforms/mac/webchat#content-area)

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

WebChat (macOS)

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

*   [WebChat (macOS app)](https://docs.openclaw.ai/platforms/mac/webchat#webchat-macos-app)
*   [Launch & debugging](https://docs.openclaw.ai/platforms/mac/webchat#launch-%26-debugging)
*   [How it is wired](https://docs.openclaw.ai/platforms/mac/webchat#how-it-is-wired)
*   [Security surface](https://docs.openclaw.ai/platforms/mac/webchat#security-surface)
*   [Known limitations](https://docs.openclaw.ai/platforms/mac/webchat#known-limitations)

macOS companion app

# WebChat (macOS)

# [​](https://docs.openclaw.ai/platforms/mac/webchat#webchat-macos-app)

WebChat (macOS app)

The macOS menu bar app embeds the WebChat UI as a native SwiftUI view. It connects to the Gateway and defaults to the **main session** for the selected agent (with a session switcher for other sessions).
*   **Local mode**: connects directly to the local Gateway WebSocket.
*   **Remote mode**: forwards the Gateway control port over SSH and uses that tunnel as the data plane.

## [​](https://docs.openclaw.ai/platforms/mac/webchat#launch-&-debugging)

Launch & debugging

*   Manual: Lobster menu → “Open Chat”.
*   Auto‑open for testing:  ```
dist/OpenClaw.app/Contents/MacOS/OpenClaw --webchat
```    
*   Logs: `./scripts/clawlog.sh` (subsystem `ai.openclaw`, category `WebChatSwiftUI`).

## [​](https://docs.openclaw.ai/platforms/mac/webchat#how-it-is-wired)

How it is wired

*   Data plane: Gateway WS methods `chat.history`, `chat.send`, `chat.abort`, `chat.inject` and events `chat`, `agent`, `presence`, `tick`, `health`.
*   Session: defaults to the primary session (`main`, or `global` when scope is global). The UI can switch between sessions.
*   Onboarding uses a dedicated session to keep first‑run setup separate.

## [​](https://docs.openclaw.ai/platforms/mac/webchat#security-surface)

Security surface

*   Remote mode forwards only the Gateway WebSocket control port over SSH.

## [​](https://docs.openclaw.ai/platforms/mac/webchat#known-limitations)

Known limitations

*   The UI is optimized for chat sessions (not a full browser sandbox).

[Voice Overlay](https://docs.openclaw.ai/platforms/mac/voice-overlay)[Canvas](https://docs.openclaw.ai/platforms/mac/canvas)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
