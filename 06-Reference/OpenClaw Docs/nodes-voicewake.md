# nodes/voicewake

Source: https://docs.openclaw.ai/nodes/voicewake

Title: Voice Wake - OpenClaw

URL Source: https://docs.openclaw.ai/nodes/voicewake

Markdown Content:
# Voice Wake - OpenClaw

[Skip to main content](https://docs.openclaw.ai/nodes/voicewake#content-area)

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

Nodes and devices

Voice Wake

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Gateway

*   [Gateway Runbook](https://docs.openclaw.ai/gateway)
*   Configuration and operations 
*   Security and sandboxing 
*   Protocols and APIs 
*   Networking and discovery 

##### Remote access

*   [Remote Access](https://docs.openclaw.ai/gateway/remote)
*   [Remote Gateway Setup](https://docs.openclaw.ai/gateway/remote-gateway-readme)
*   [Tailscale](https://docs.openclaw.ai/gateway/tailscale)

##### Security

*   [Formal Verification (Security Models)](https://docs.openclaw.ai/security/formal-verification)
*   [Threat Model (MITRE ATLAS)](https://docs.openclaw.ai/security/THREAT-MODEL-ATLAS)
*   [Contributing to the Threat Model](https://docs.openclaw.ai/security/CONTRIBUTING-THREAT-MODEL)

##### Nodes and devices

*   [Nodes](https://docs.openclaw.ai/nodes)
*   [Node Troubleshooting](https://docs.openclaw.ai/nodes/troubleshooting)
*   [Media Understanding](https://docs.openclaw.ai/nodes/media-understanding)
*   [Image and Media Support](https://docs.openclaw.ai/nodes/images)
*   [Audio and Voice Notes](https://docs.openclaw.ai/nodes/audio)
*   [Camera Capture](https://docs.openclaw.ai/nodes/camera)
*   [Talk Mode](https://docs.openclaw.ai/nodes/talk)
*   [Voice Wake](https://docs.openclaw.ai/nodes/voicewake)
*   [Location Command](https://docs.openclaw.ai/nodes/location-command)
*   [Text-to-Speech](https://docs.openclaw.ai/tools/tts)

##### Web interfaces

*   [Web](https://docs.openclaw.ai/web)
*   [Control UI](https://docs.openclaw.ai/web/control-ui)
*   [Dashboard](https://docs.openclaw.ai/web/dashboard)
*   [WebChat](https://docs.openclaw.ai/web/webchat)
*   [TUI](https://docs.openclaw.ai/web/tui)

On this page

*   [Voice Wake (Global Wake Words)](https://docs.openclaw.ai/nodes/voicewake#voice-wake-global-wake-words)
*   [Storage (Gateway host)](https://docs.openclaw.ai/nodes/voicewake#storage-gateway-host)
*   [Protocol](https://docs.openclaw.ai/nodes/voicewake#protocol)
*   [Methods](https://docs.openclaw.ai/nodes/voicewake#methods)
*   [Events](https://docs.openclaw.ai/nodes/voicewake#events)
*   [Client behavior](https://docs.openclaw.ai/nodes/voicewake#client-behavior)
*   [macOS app](https://docs.openclaw.ai/nodes/voicewake#macos-app)
*   [iOS node](https://docs.openclaw.ai/nodes/voicewake#ios-node)
*   [Android node](https://docs.openclaw.ai/nodes/voicewake#android-node)

Nodes and devices

# Voice Wake

# [​](https://docs.openclaw.ai/nodes/voicewake#voice-wake-global-wake-words)

Voice Wake (Global Wake Words)

OpenClaw treats **wake words as a single global list** owned by the **Gateway**.
*   There are **no per-node custom wake words**.
*   **Any node/app UI may edit** the list; changes are persisted by the Gateway and broadcast to everyone.
*   macOS and iOS keep local **Voice Wake enabled/disabled** toggles (local UX + permissions differ).
*   Android currently keeps Voice Wake off and uses a manual mic flow in the Voice tab.

## [​](https://docs.openclaw.ai/nodes/voicewake#storage-gateway-host)

Storage (Gateway host)

Wake words are stored on the gateway machine at:
*   `~/.openclaw/settings/voicewake.json`

Shape:

```
{ "triggers": ["openclaw", "claude", "computer"], "updatedAtMs": 1730000000000 }
```

## [​](https://docs.openclaw.ai/nodes/voicewake#protocol)

Protocol

### [​](https://docs.openclaw.ai/nodes/voicewake#methods)

Methods

*   `voicewake.get` → `{ triggers: string[] }`
*   `voicewake.set` with params `{ triggers: string[] }` → `{ triggers: string[] }`

Notes:
*   Triggers are normalized (trimmed, empties dropped). Empty lists fall back to defaults.
*   Limits are enforced for safety (count/length caps).

### [​](https://docs.openclaw.ai/nodes/voicewake#events)

Events

*   `voicewake.changed` payload `{ triggers: string[] }`

Who receives it:
*   All WebSocket clients (macOS app, WebChat, etc.)
*   All connected nodes (iOS/Android), and also on node connect as an initial “current state” push.

## [​](https://docs.openclaw.ai/nodes/voicewake#client-behavior)

Client behavior

### [​](https://docs.openclaw.ai/nodes/voicewake#macos-app)

macOS app

*   Uses the global list to gate `VoiceWakeRuntime` triggers.
*   Editing “Trigger words” in Voice Wake settings calls `voicewake.set` and then relies on the broadcast to keep other clients in sync.

### [​](https://docs.openclaw.ai/nodes/voicewake#ios-node)

iOS node

*   Uses the global list for `VoiceWakeManager` trigger detection.
*   Editing Wake Words in Settings calls `voicewake.set` (over the Gateway WS) and also keeps local wake-word detection responsive.

### [​](https://docs.openclaw.ai/nodes/voicewake#android-node)

Android node

*   Voice Wake is currently disabled in Android runtime/Settings.
*   Android voice uses manual mic capture in the Voice tab instead of wake-word triggers.

[Talk Mode](https://docs.openclaw.ai/nodes/talk)[Location Command](https://docs.openclaw.ai/nodes/location-command)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
