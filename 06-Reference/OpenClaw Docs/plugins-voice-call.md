# plugins/voice-call

Source: https://docs.openclaw.ai/plugins/voice-call

Title: Voice Call Plugin - OpenClaw

URL Source: https://docs.openclaw.ai/plugins/voice-call

Markdown Content:
# Voice Call Plugin - OpenClaw

[Skip to main content](https://docs.openclaw.ai/plugins/voice-call#content-area)

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

Messaging platforms

Voice Call Plugin

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Chat Channels](https://docs.openclaw.ai/channels)

##### Messaging platforms

*   [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles)
*   [Discord](https://docs.openclaw.ai/channels/discord)
*   [Feishu](https://docs.openclaw.ai/channels/feishu)
*   [Google Chat](https://docs.openclaw.ai/channels/googlechat)
*   [iMessage](https://docs.openclaw.ai/channels/imessage)
*   [IRC](https://docs.openclaw.ai/channels/irc)
*   [LINE](https://docs.openclaw.ai/channels/line)
*   [Matrix](https://docs.openclaw.ai/channels/matrix)
*   [Mattermost](https://docs.openclaw.ai/channels/mattermost)
*   [Microsoft Teams](https://docs.openclaw.ai/channels/msteams)
*   [Nextcloud Talk](https://docs.openclaw.ai/channels/nextcloud-talk)
*   [Nostr](https://docs.openclaw.ai/channels/nostr)
*   [Signal](https://docs.openclaw.ai/channels/signal)
*   [Slack](https://docs.openclaw.ai/channels/slack)
*   [Synology Chat](https://docs.openclaw.ai/channels/synology-chat)
*   [Telegram](https://docs.openclaw.ai/channels/telegram)
*   [Tlon](https://docs.openclaw.ai/channels/tlon)
*   [Twitch](https://docs.openclaw.ai/channels/twitch)
*   [Voice Call Plugin](https://docs.openclaw.ai/plugins/voice-call)
*   [WhatsApp](https://docs.openclaw.ai/channels/whatsapp)
*   [Zalo](https://docs.openclaw.ai/channels/zalo)
*   [Zalo Personal](https://docs.openclaw.ai/channels/zalouser)

##### Configuration

*   [Pairing](https://docs.openclaw.ai/channels/pairing)
*   [Group Messages](https://docs.openclaw.ai/channels/group-messages)
*   [Groups](https://docs.openclaw.ai/channels/groups)
*   [Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups)
*   [Channel Routing](https://docs.openclaw.ai/channels/channel-routing)
*   [Channel Location Parsing](https://docs.openclaw.ai/channels/location)
*   [Channel Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)

On this page

*   [Voice Call (plugin)](https://docs.openclaw.ai/plugins/voice-call#voice-call-plugin)
*   [Where it runs (local vs remote)](https://docs.openclaw.ai/plugins/voice-call#where-it-runs-local-vs-remote)
*   [Install](https://docs.openclaw.ai/plugins/voice-call#install)
*   [Option A: install from npm (recommended)](https://docs.openclaw.ai/plugins/voice-call#option-a-install-from-npm-recommended)
*   [Option B: install from a local folder (dev, no copying)](https://docs.openclaw.ai/plugins/voice-call#option-b-install-from-a-local-folder-dev-no-copying)
*   [Config](https://docs.openclaw.ai/plugins/voice-call#config)
*   [Stale call reaper](https://docs.openclaw.ai/plugins/voice-call#stale-call-reaper)
*   [Webhook Security](https://docs.openclaw.ai/plugins/voice-call#webhook-security)
*   [TTS for calls](https://docs.openclaw.ai/plugins/voice-call#tts-for-calls)
*   [More examples](https://docs.openclaw.ai/plugins/voice-call#more-examples)
*   [Inbound calls](https://docs.openclaw.ai/plugins/voice-call#inbound-calls)
*   [Spoken output contract](https://docs.openclaw.ai/plugins/voice-call#spoken-output-contract)
*   [Conversation startup behavior](https://docs.openclaw.ai/plugins/voice-call#conversation-startup-behavior)
*   [Twilio stream disconnect grace](https://docs.openclaw.ai/plugins/voice-call#twilio-stream-disconnect-grace)
*   [CLI](https://docs.openclaw.ai/plugins/voice-call#cli)
*   [Agent tool](https://docs.openclaw.ai/plugins/voice-call#agent-tool)
*   [Gateway RPC](https://docs.openclaw.ai/plugins/voice-call#gateway-rpc)

Messaging platforms

# Voice Call Plugin

# [​](https://docs.openclaw.ai/plugins/voice-call#voice-call-plugin)

Voice Call (plugin)

Voice calls for OpenClaw via a plugin. Supports outbound notifications and multi-turn conversations with inbound policies.Current providers:
*   `twilio` (Programmable Voice + Media Streams)
*   `telnyx` (Call Control v2)
*   `plivo` (Voice API + XML transfer + GetInput speech)
*   `mock` (dev/no network)

Quick mental model:
*   Install plugin
*   Restart Gateway
*   Configure under `plugins.entries.voice-call.config`
*   Use `openclaw voicecall ...` or the `voice_call` tool

## [​](https://docs.openclaw.ai/plugins/voice-call#where-it-runs-local-vs-remote)

Where it runs (local vs remote)

The Voice Call plugin runs **inside the Gateway process**.If you use a remote Gateway, install/configure the plugin on the **machine running the Gateway**, then restart the Gateway to load it.
## [​](https://docs.openclaw.ai/plugins/voice-call#install)

Install

### [​](https://docs.openclaw.ai/plugins/voice-call#option-a-install-from-npm-recommended)

Option A: install from npm (recommended)

```
openclaw plugins install @openclaw/voice-call
```

Restart the Gateway afterwards.
### [​](https://docs.openclaw.ai/plugins/voice-call#option-b-install-from-a-local-folder-dev-no-copying)

Option B: install from a local folder (dev, no copying)

```
openclaw plugins install ./extensions/voice-call
cd ./extensions/voice-call && pnpm install
```

Restart the Gateway afterwards.
## [​](https://docs.openclaw.ai/plugins/voice-call#config)

Config

Set config under `plugins.entries.voice-call.config`:

```
{
  plugins: {
    entries: {
      "voice-call": {
        enabled: true,
        config: {
          provider: "twilio", // or "telnyx" | "plivo" | "mock"
          fromNumber: "+15550001234",
          toNumber: "+15550005678",

          twilio: {
            accountSid: "ACxxxxxxxx",
            authToken: "...",
          },

          telnyx: {
            apiKey: "...",
            connectionId: "...",
            // Telnyx webhook public key from the Telnyx Mission Control Portal
            // (Base64 string; can also be set via TELNYX_PUBLIC_KEY).
            publicKey: "...",
          },

          plivo: {
            authId: "MAxxxxxxxxxxxxxxxxxxxx",
            authToken: "...",
          },

          // Webhook server
          serve: {
            port: 3334,
            path: "/voice/webhook",
          },

          // Webhook security (recommended for tunnels/proxies)
          webhookSecurity: {
            allowedHosts: ["voice.example.com"],
            trustedProxyIPs: ["100.64.0.1"],
          },

          // Public exposure (pick one)
          // publicUrl: "https://example.ngrok.app/voice/webhook",
          // tunnel: { provider: "ngrok" },
          // tailscale: { mode: "funnel", path: "/voice/webhook" }

          outbound: {
            defaultMode: "notify", // notify | conversation
          },

          streaming: {
            enabled: true,
            streamPath: "/voice/stream",
            preStartTimeoutMs: 5000,
            maxPendingConnections: 32,
            maxPendingConnectionsPerIp: 4,
            maxConnections: 128,
          },
        },
      },
    },
  },
}
```

Notes:
*   Twilio/Telnyx require a **publicly reachable** webhook URL.
*   Plivo requires a **publicly reachable** webhook URL.
*   `mock` is a local dev provider (no network calls).
*   Telnyx requires `telnyx.publicKey` (or `TELNYX_PUBLIC_KEY`) unless `skipSignatureVerification` is true.
*   `skipSignatureVerification` is for local testing only.
*   If you use ngrok free tier, set `publicUrl` to the exact ngrok URL; signature verification is always enforced.
*   `tunnel.allowNgrokFreeTierLoopbackBypass: true` allows Twilio webhooks with invalid signatures **only** when `tunnel.provider="ngrok"` and `serve.bind` is loopback (ngrok local agent). Use for local dev only.
*   Ngrok free tier URLs can change or add interstitial behavior; if `publicUrl` drifts, Twilio signatures will fail. For production, prefer a stable domain or Tailscale funnel.
*   Streaming security defaults:
    *   `streaming.preStartTimeoutMs` closes sockets that never send a valid `start` frame.
    *   `streaming.maxPendingConnections` caps total unauthenticated pre-start sockets.
    *   `streaming.maxPendingConnectionsPerIp` caps unauthenticated pre-start sockets per source IP.
    *   `streaming.maxConnections` caps total open media stream sockets (pending + active).

## [​](https://docs.openclaw.ai/plugins/voice-call#stale-call-reaper)

Stale call reaper

Use `staleCallReaperSeconds` to end calls that never receive a terminal webhook (for example, notify-mode calls that never complete). The default is `0` (disabled).Recommended ranges:
*   **Production:**`120`–`300` seconds for notify-style flows.
*   Keep this value **higher than `maxDurationSeconds`** so normal calls can finish. A good starting point is `maxDurationSeconds + 30–60` seconds.

Example:

```
{
  plugins: {
    entries: {
      "voice-call": {
        config: {
          maxDurationSeconds: 300,
          staleCallReaperSeconds: 360,
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/plugins/voice-call#webhook-security)

Webhook Security

When a proxy or tunnel sits in front of the Gateway, the plugin reconstructs the public URL for signature verification. These options control which forwarded headers are trusted.`webhookSecurity.allowedHosts` allowlists hosts from forwarding headers.`webhookSecurity.trustForwardingHeaders` trusts forwarded headers without an allowlist.`webhookSecurity.trustedProxyIPs` only trusts forwarded headers when the request remote IP matches the list.Webhook replay protection is enabled for Twilio and Plivo. Replayed valid webhook requests are acknowledged but skipped for side effects.Twilio conversation turns include a per-turn token in `<Gather>` callbacks, so stale/replayed speech callbacks cannot satisfy a newer pending transcript turn.Unauthenticated webhook requests are rejected before body reads when the provider’s required signature headers are missing.The voice-call webhook uses the shared pre-auth body profile (64 KB / 5 seconds) plus a per-IP in-flight cap before signature verification.Example with a stable public host:

```
{
  plugins: {
    entries: {
      "voice-call": {
        config: {
          publicUrl: "https://voice.example.com/voice/webhook",
          webhookSecurity: {
            allowedHosts: ["voice.example.com"],
          },
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/plugins/voice-call#tts-for-calls)

TTS for calls

Voice Call uses the core `messages.tts` configuration for streaming speech on calls. You can override it under the plugin config with the **same shape** — it deep‑merges with `messages.tts`.

```
{
  tts: {
    provider: "elevenlabs",
    elevenlabs: {
      voiceId: "pMsXgVXv3BLzUgSXRplE",
      modelId: "eleven_multilingual_v2",
    },
  },
}
```

Notes:
*   **Microsoft speech is ignored for voice calls** (telephony audio needs PCM; the current Microsoft transport does not expose telephony PCM output).
*   Core TTS is used when Twilio media streaming is enabled; otherwise calls fall back to provider native voices.
*   If a Twilio media stream is already active, Voice Call does not fall back to TwiML `<Say>`. If telephony TTS is unavailable in that state, the playback request fails instead of mixing two playback paths.

### [​](https://docs.openclaw.ai/plugins/voice-call#more-examples)

More examples

Use core TTS only (no override):

```
{
  messages: {
    tts: {
      provider: "openai",
      openai: { voice: "alloy" },
    },
  },
}
```

Override to ElevenLabs just for calls (keep core default elsewhere):

```
{
  plugins: {
    entries: {
      "voice-call": {
        config: {
          tts: {
            provider: "elevenlabs",
            elevenlabs: {
              apiKey: "elevenlabs_key",
              voiceId: "pMsXgVXv3BLzUgSXRplE",
              modelId: "eleven_multilingual_v2",
            },
          },
        },
      },
    },
  },
}
```

Override only the OpenAI model for calls (deep‑merge example):

```
{
  plugins: {
    entries: {
      "voice-call": {
        config: {
          tts: {
            openai: {
              model: "gpt-4o-mini-tts",
              voice: "marin",
            },
          },
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/plugins/voice-call#inbound-calls)

Inbound calls

Inbound policy defaults to `disabled`. To enable inbound calls, set:

```
{
  inboundPolicy: "allowlist",
  allowFrom: ["+15550001234"],
  inboundGreeting: "Hello! How can I help?",
}
```

`inboundPolicy: "allowlist"` is a low-assurance caller-ID screen. The plugin normalizes the provider-supplied `From` value and compares it to `allowFrom`. Webhook verification authenticates provider delivery and payload integrity, but it does not prove PSTN/VoIP caller-number ownership. Treat `allowFrom` as caller-ID filtering, not strong caller identity.Auto-responses use the agent system. Tune with:
*   `responseModel`
*   `responseSystemPrompt`
*   `responseTimeoutMs`

### [​](https://docs.openclaw.ai/plugins/voice-call#spoken-output-contract)

Spoken output contract

For auto-responses, Voice Call appends a strict spoken-output contract to the system prompt:
*   `{"spoken":"..."}`

Voice Call then extracts speech text defensively:
*   Ignores payloads marked as reasoning/error content.
*   Parses direct JSON, fenced JSON, or inline `"spoken"` keys.
*   Falls back to plain text and removes likely planning/meta lead-in paragraphs.

This keeps spoken playback focused on caller-facing text and avoids leaking planning text into audio.
### [​](https://docs.openclaw.ai/plugins/voice-call#conversation-startup-behavior)

Conversation startup behavior

For outbound `conversation` calls, first-message handling is tied to live playback state:
*   Barge-in queue clear and auto-response are suppressed only while the initial greeting is actively speaking.
*   If initial playback fails, the call returns to `listening` and the initial message remains queued for retry.
*   Initial playback for Twilio streaming starts on stream connect without extra delay.

### [​](https://docs.openclaw.ai/plugins/voice-call#twilio-stream-disconnect-grace)

Twilio stream disconnect grace

When a Twilio media stream disconnects, Voice Call waits `2000ms` before auto-ending the call:
*   If the stream reconnects during that window, auto-end is canceled.
*   If no stream is re-registered after the grace period, the call is ended to prevent stuck active calls.

## [​](https://docs.openclaw.ai/plugins/voice-call#cli)

CLI

```
openclaw voicecall call --to "+15555550123" --message "Hello from OpenClaw"
openclaw voicecall start --to "+15555550123"   # alias for call
openclaw voicecall continue --call-id <id> --message "Any questions?"
openclaw voicecall speak --call-id <id> --message "One moment"
openclaw voicecall end --call-id <id>
openclaw voicecall status --call-id <id>
openclaw voicecall tail
openclaw voicecall latency                     # summarize turn latency from logs
openclaw voicecall expose --mode funnel
```

`latency` reads `calls.jsonl` from the default voice-call storage path. Use `--file <path>` to point at a different log and `--last <n>` to limit analysis to the last N records (default 200). Output includes p50/p90/p99 for turn latency and listen-wait times.
## [​](https://docs.openclaw.ai/plugins/voice-call#agent-tool)

Agent tool

Tool name: `voice_call`Actions:
*   `initiate_call` (message, to?, mode?)
*   `continue_call` (callId, message)
*   `speak_to_user` (callId, message)
*   `end_call` (callId)
*   `get_status` (callId)

This repo ships a matching skill doc at `skills/voice-call/SKILL.md`.
## [​](https://docs.openclaw.ai/plugins/voice-call#gateway-rpc)

Gateway RPC

*   `voicecall.initiate` (`to?`, `message`, `mode?`)
*   `voicecall.continue` (`callId`, `message`)
*   `voicecall.speak` (`callId`, `message`)
*   `voicecall.end` (`callId`)
*   `voicecall.status` (`callId`)

[Twitch](https://docs.openclaw.ai/channels/twitch)[WhatsApp](https://docs.openclaw.ai/channels/whatsapp)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
