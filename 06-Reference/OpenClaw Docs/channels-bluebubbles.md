# channels/bluebubbles

Source: https://docs.openclaw.ai/channels/bluebubbles

Title: BlueBubbles - OpenClaw

URL Source: https://docs.openclaw.ai/channels/bluebubbles

Markdown Content:
# BlueBubbles - OpenClaw

[Skip to main content](https://docs.openclaw.ai/channels/bluebubbles#content-area)

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

BlueBubbles

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

*   [BlueBubbles (macOS REST)](https://docs.openclaw.ai/channels/bluebubbles#bluebubbles-macos-rest)
*   [Overview](https://docs.openclaw.ai/channels/bluebubbles#overview)
*   [Quick start](https://docs.openclaw.ai/channels/bluebubbles#quick-start)
*   [Keeping Messages.app alive (VM / headless setups)](https://docs.openclaw.ai/channels/bluebubbles#keeping-messages-app-alive-vm-%2F-headless-setups)
*   [1) Save the AppleScript](https://docs.openclaw.ai/channels/bluebubbles#1-save-the-applescript)
*   [2) Install a LaunchAgent](https://docs.openclaw.ai/channels/bluebubbles#2-install-a-launchagent)
*   [Onboarding](https://docs.openclaw.ai/channels/bluebubbles#onboarding)
*   [Access control (DMs + groups)](https://docs.openclaw.ai/channels/bluebubbles#access-control-dms-%2B-groups)
*   [Contact name enrichment (macOS, optional)](https://docs.openclaw.ai/channels/bluebubbles#contact-name-enrichment-macos-optional)
*   [Mention gating (groups)](https://docs.openclaw.ai/channels/bluebubbles#mention-gating-groups)
*   [Command gating](https://docs.openclaw.ai/channels/bluebubbles#command-gating)
*   [ACP conversation bindings](https://docs.openclaw.ai/channels/bluebubbles#acp-conversation-bindings)
*   [Typing + read receipts](https://docs.openclaw.ai/channels/bluebubbles#typing-%2B-read-receipts)
*   [Advanced actions](https://docs.openclaw.ai/channels/bluebubbles#advanced-actions)
*   [Message IDs (short vs full)](https://docs.openclaw.ai/channels/bluebubbles#message-ids-short-vs-full)
*   [Block streaming](https://docs.openclaw.ai/channels/bluebubbles#block-streaming)
*   [Media + limits](https://docs.openclaw.ai/channels/bluebubbles#media-%2B-limits)
*   [Configuration reference](https://docs.openclaw.ai/channels/bluebubbles#configuration-reference)
*   [Addressing / delivery targets](https://docs.openclaw.ai/channels/bluebubbles#addressing-%2F-delivery-targets)
*   [Security](https://docs.openclaw.ai/channels/bluebubbles#security)
*   [Troubleshooting](https://docs.openclaw.ai/channels/bluebubbles#troubleshooting)

Messaging platforms

# BlueBubbles

# [​](https://docs.openclaw.ai/channels/bluebubbles#bluebubbles-macos-rest)

BlueBubbles (macOS REST)

Status: bundled plugin that talks to the BlueBubbles macOS server over HTTP. **Recommended for iMessage integration** due to its richer API and easier setup compared to the legacy imsg channel.
## [​](https://docs.openclaw.ai/channels/bluebubbles#overview)

Overview

*   Runs on macOS via the BlueBubbles helper app ([bluebubbles.app](https://bluebubbles.app/)).
*   Recommended/tested: macOS Sequoia (15). macOS Tahoe (26) works; edit is currently broken on Tahoe, and group icon updates may report success but not sync.
*   OpenClaw talks to it through its REST API (`GET /api/v1/ping`, `POST /message/text`, `POST /chat/:id/*`).
*   Incoming messages arrive via webhooks; outgoing replies, typing indicators, read receipts, and tapbacks are REST calls.
*   Attachments and stickers are ingested as inbound media (and surfaced to the agent when possible).
*   Pairing/allowlist works the same way as other channels (`/channels/pairing` etc) with `channels.bluebubbles.allowFrom` + pairing codes.
*   Reactions are surfaced as system events just like Slack/Telegram so agents can “mention” them before replying.
*   Advanced features: edit, unsend, reply threading, message effects, group management.

## [​](https://docs.openclaw.ai/channels/bluebubbles#quick-start)

Quick start

1.   Install the BlueBubbles server on your Mac (follow the instructions at [bluebubbles.app/install](https://bluebubbles.app/install)).
2.   In the BlueBubbles config, enable the web API and set a password.
3.   Run `openclaw onboard` and select BlueBubbles, or configure manually:  ```
{
  channels: {
    bluebubbles: {
      enabled: true,
      serverUrl: "http://192.168.1.100:1234",
      password: "example-password",
      webhookPath: "/bluebubbles-webhook",
    },
  },
}
```    
4.   Point BlueBubbles webhooks to your gateway (example: `https://your-gateway-host:3000/bluebubbles-webhook?password=<password>`).
5.   Start the gateway; it will register the webhook handler and start pairing.

Security note:
*   Always set a webhook password.
*   Webhook authentication is always required. OpenClaw rejects BlueBubbles webhook requests unless they include a password/guid that matches `channels.bluebubbles.password` (for example `?password=<password>` or `x-password`), regardless of loopback/proxy topology.
*   Password authentication is checked before reading/parsing full webhook bodies.

## [​](https://docs.openclaw.ai/channels/bluebubbles#keeping-messages-app-alive-vm-/-headless-setups)

Keeping Messages.app alive (VM / headless setups)

Some macOS VM / always-on setups can end up with Messages.app going “idle” (incoming events stop until the app is opened/foregrounded). A simple workaround is to **poke Messages every 5 minutes** using an AppleScript + LaunchAgent.
### [​](https://docs.openclaw.ai/channels/bluebubbles#1-save-the-applescript)

1) Save the AppleScript

Save this as:
*   `~/Scripts/poke-messages.scpt`

Example script (non-interactive; does not steal focus):

```
try
  tell application "Messages"
    if not running then
      launch
    end if

    -- Touch the scripting interface to keep the process responsive.
    set _chatCount to (count of chats)
  end tell
on error
  -- Ignore transient failures (first-run prompts, locked session, etc).
end try
```

### [​](https://docs.openclaw.ai/channels/bluebubbles#2-install-a-launchagent)

2) Install a LaunchAgent

Save this as:
*   `~/Library/LaunchAgents/com.user.poke-messages.plist`

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>com.user.poke-messages</string>

    <key>ProgramArguments</key>
    <array>
      <string>/bin/bash</string>
      <string>-lc</string>
      <string>/usr/bin/osascript &quot;$HOME/Scripts/poke-messages.scpt&quot;</string>
    </array>

    <key>RunAtLoad</key>
    <true/>

    <key>StartInterval</key>
    <integer>300</integer>

    <key>StandardOutPath</key>
    <string>/tmp/poke-messages.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/poke-messages.err</string>
  </dict>
</plist>
```

Notes:
*   This runs **every 300 seconds** and **on login**.
*   The first run may trigger macOS **Automation** prompts (`osascript` → Messages). Approve them in the same user session that runs the LaunchAgent.

Load it:

```
launchctl unload ~/Library/LaunchAgents/com.user.poke-messages.plist 2>/dev/null || true
launchctl load ~/Library/LaunchAgents/com.user.poke-messages.plist
```

## [​](https://docs.openclaw.ai/channels/bluebubbles#onboarding)

Onboarding

BlueBubbles is available in interactive onboarding:

```
openclaw onboard
```

The wizard prompts for:
*   **Server URL** (required): BlueBubbles server address (e.g., `http://192.168.1.100:1234`)
*   **Password** (required): API password from BlueBubbles Server settings
*   **Webhook path** (optional): Defaults to `/bluebubbles-webhook`
*   **DM policy**: pairing, allowlist, open, or disabled
*   **Allow list**: Phone numbers, emails, or chat targets

You can also add BlueBubbles via CLI:

```
openclaw channels add bluebubbles --http-url http://192.168.1.100:1234 --password <password>
```

## [​](https://docs.openclaw.ai/channels/bluebubbles#access-control-dms-+-groups)

Access control (DMs + groups)

DMs:
*   Default: `channels.bluebubbles.dmPolicy = "pairing"`.
*   Unknown senders receive a pairing code; messages are ignored until approved (codes expire after 1 hour).
*   Approve via:
    *   `openclaw pairing list bluebubbles`
    *   `openclaw pairing approve bluebubbles <CODE>`

*   Pairing is the default token exchange. Details: [Pairing](https://docs.openclaw.ai/channels/pairing)

Groups:
*   `channels.bluebubbles.groupPolicy = open | allowlist | disabled` (default: `allowlist`).
*   `channels.bluebubbles.groupAllowFrom` controls who can trigger in groups when `allowlist` is set.

### [​](https://docs.openclaw.ai/channels/bluebubbles#contact-name-enrichment-macos-optional)

Contact name enrichment (macOS, optional)

BlueBubbles group webhooks often only include raw participant addresses. If you want `GroupMembers` context to show local contact names instead, you can opt in to local Contacts enrichment on macOS:
*   `channels.bluebubbles.enrichGroupParticipantsFromContacts = true` enables the lookup. Default: `false`.
*   Lookups run only after group access, command authorization, and mention gating have allowed the message through.
*   Only unnamed phone participants are enriched.
*   Raw phone numbers remain as the fallback when no local match is found.

```
{
  channels: {
    bluebubbles: {
      enrichGroupParticipantsFromContacts: true,
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/bluebubbles#mention-gating-groups)

Mention gating (groups)

BlueBubbles supports mention gating for group chats, matching iMessage/WhatsApp behavior:
*   Uses `agents.list[].groupChat.mentionPatterns` (or `messages.groupChat.mentionPatterns`) to detect mentions.
*   When `requireMention` is enabled for a group, the agent only responds when mentioned.
*   Control commands from authorized senders bypass mention gating.

Per-group configuration:

```
{
  channels: {
    bluebubbles: {
      groupPolicy: "allowlist",
      groupAllowFrom: ["+15555550123"],
      groups: {
        "*": { requireMention: true }, // default for all groups
        "iMessage;-;chat123": { requireMention: false }, // override for specific group
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/channels/bluebubbles#command-gating)

Command gating

*   Control commands (e.g., `/config`, `/model`) require authorization.
*   Uses `allowFrom` and `groupAllowFrom` to determine command authorization.
*   Authorized senders can run control commands even without mentioning in groups.

## [​](https://docs.openclaw.ai/channels/bluebubbles#acp-conversation-bindings)

ACP conversation bindings

BlueBubbles chats can be turned into durable ACP workspaces without changing the transport layer.Fast operator flow:
*   Run `/acp spawn codex --bind here` inside the DM or allowed group chat.
*   Future messages in that same BlueBubbles conversation route to the spawned ACP session.
*   `/new` and `/reset` reset the same bound ACP session in place.
*   `/acp close` closes the ACP session and removes the binding.

Configured persistent bindings are also supported through top-level `bindings[]` entries with `type: "acp"` and `match.channel: "bluebubbles"`.`match.peer.id` can use any supported BlueBubbles target form:
*   normalized DM handle such as `+15555550123` or `user@example.com`
*   `chat_id:<id>`
*   `chat_guid:<guid>`
*   `chat_identifier:<identifier>`

For stable group bindings, prefer `chat_id:*` or `chat_identifier:*`.Example:

```
{
  agents: {
    list: [
      {
        id: "codex",
        runtime: {
          type: "acp",
          acp: { agent: "codex", backend: "acpx", mode: "persistent" },
        },
      },
    ],
  },
  bindings: [
    {
      type: "acp",
      agentId: "codex",
      match: {
        channel: "bluebubbles",
        accountId: "default",
        peer: { kind: "dm", id: "+15555550123" },
      },
      acp: { label: "codex-imessage" },
    },
  ],
}
```

See [ACP Agents](https://docs.openclaw.ai/tools/acp-agents) for shared ACP binding behavior.
## [​](https://docs.openclaw.ai/channels/bluebubbles#typing-+-read-receipts)

Typing + read receipts

*   **Typing indicators**: Sent automatically before and during response generation.
*   **Read receipts**: Controlled by `channels.bluebubbles.sendReadReceipts` (default: `true`).
*   **Typing indicators**: OpenClaw sends typing start events; BlueBubbles clears typing automatically on send or timeout (manual stop via DELETE is unreliable).

```
{
  channels: {
    bluebubbles: {
      sendReadReceipts: false, // disable read receipts
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/bluebubbles#advanced-actions)

Advanced actions

BlueBubbles supports advanced message actions when enabled in config:

```
{
  channels: {
    bluebubbles: {
      actions: {
        reactions: true, // tapbacks (default: true)
        edit: true, // edit sent messages (macOS 13+, broken on macOS 26 Tahoe)
        unsend: true, // unsend messages (macOS 13+)
        reply: true, // reply threading by message GUID
        sendWithEffect: true, // message effects (slam, loud, etc.)
        renameGroup: true, // rename group chats
        setGroupIcon: true, // set group chat icon/photo (flaky on macOS 26 Tahoe)
        addParticipant: true, // add participants to groups
        removeParticipant: true, // remove participants from groups
        leaveGroup: true, // leave group chats
        sendAttachment: true, // send attachments/media
      },
    },
  },
}
```

Available actions:
*   **react**: Add/remove tapback reactions (`messageId`, `emoji`, `remove`)
*   **edit**: Edit a sent message (`messageId`, `text`)
*   **unsend**: Unsend a message (`messageId`)
*   **reply**: Reply to a specific message (`messageId`, `text`, `to`)
*   **sendWithEffect**: Send with iMessage effect (`text`, `to`, `effectId`)
*   **renameGroup**: Rename a group chat (`chatGuid`, `displayName`)
*   **setGroupIcon**: Set a group chat’s icon/photo (`chatGuid`, `media`) — flaky on macOS 26 Tahoe (API may return success but the icon does not sync).
*   **addParticipant**: Add someone to a group (`chatGuid`, `address`)
*   **removeParticipant**: Remove someone from a group (`chatGuid`, `address`)
*   **leaveGroup**: Leave a group chat (`chatGuid`)
*   **upload-file**: Send media/files (`to`, `buffer`, `filename`, `asVoice`)
    *   Voice memos: set `asVoice: true` with **MP3** or **CAF** audio to send as an iMessage voice message. BlueBubbles converts MP3 → CAF when sending voice memos.

*   Legacy alias: `sendAttachment` still works, but `upload-file` is the canonical action name.

### [​](https://docs.openclaw.ai/channels/bluebubbles#message-ids-short-vs-full)

Message IDs (short vs full)

OpenClaw may surface _short_ message IDs (e.g., `1`, `2`) to save tokens.
*   `MessageSid` / `ReplyToId` can be short IDs.
*   `MessageSidFull` / `ReplyToIdFull` contain the provider full IDs.
*   Short IDs are in-memory; they can expire on restart or cache eviction.
*   Actions accept short or full `messageId`, but short IDs will error if no longer available.

Use full IDs for durable automations and storage:
*   Templates: `{{MessageSidFull}}`, `{{ReplyToIdFull}}`
*   Context: `MessageSidFull` / `ReplyToIdFull` in inbound payloads

See [Configuration](https://docs.openclaw.ai/gateway/configuration) for template variables.
## [​](https://docs.openclaw.ai/channels/bluebubbles#block-streaming)

Block streaming

Control whether responses are sent as a single message or streamed in blocks:

```
{
  channels: {
    bluebubbles: {
      blockStreaming: true, // enable block streaming (off by default)
    },
  },
}
```

## [​](https://docs.openclaw.ai/channels/bluebubbles#media-+-limits)

Media + limits

*   Inbound attachments are downloaded and stored in the media cache.
*   Media cap via `channels.bluebubbles.mediaMaxMb` for inbound and outbound media (default: 8 MB).
*   Outbound text is chunked to `channels.bluebubbles.textChunkLimit` (default: 4000 chars).

## [​](https://docs.openclaw.ai/channels/bluebubbles#configuration-reference)

Configuration reference

Full configuration: [Configuration](https://docs.openclaw.ai/gateway/configuration)Provider options:
*   `channels.bluebubbles.enabled`: Enable/disable the channel.
*   `channels.bluebubbles.serverUrl`: BlueBubbles REST API base URL.
*   `channels.bluebubbles.password`: API password.
*   `channels.bluebubbles.webhookPath`: Webhook endpoint path (default: `/bluebubbles-webhook`).
*   `channels.bluebubbles.dmPolicy`: `pairing | allowlist | open | disabled` (default: `pairing`).
*   `channels.bluebubbles.allowFrom`: DM allowlist (handles, emails, E.164 numbers, `chat_id:*`, `chat_guid:*`).
*   `channels.bluebubbles.groupPolicy`: `open | allowlist | disabled` (default: `allowlist`).
*   `channels.bluebubbles.groupAllowFrom`: Group sender allowlist.
*   `channels.bluebubbles.enrichGroupParticipantsFromContacts`: On macOS, optionally enrich unnamed group participants from local Contacts after gating passes. Default: `false`.
*   `channels.bluebubbles.groups`: Per-group config (`requireMention`, etc.).
*   `channels.bluebubbles.sendReadReceipts`: Send read receipts (default: `true`).
*   `channels.bluebubbles.blockStreaming`: Enable block streaming (default: `false`; required for streaming replies).
*   `channels.bluebubbles.textChunkLimit`: Outbound chunk size in chars (default: 4000).
*   `channels.bluebubbles.chunkMode`: `length` (default) splits only when exceeding `textChunkLimit`; `newline` splits on blank lines (paragraph boundaries) before length chunking.
*   `channels.bluebubbles.mediaMaxMb`: Inbound/outbound media cap in MB (default: 8).
*   `channels.bluebubbles.mediaLocalRoots`: Explicit allowlist of absolute local directories permitted for outbound local media paths. Local path sends are denied by default unless this is configured. Per-account override: `channels.bluebubbles.accounts.<accountId>.mediaLocalRoots`.
*   `channels.bluebubbles.historyLimit`: Max group messages for context (0 disables).
*   `channels.bluebubbles.dmHistoryLimit`: DM history limit.
*   `channels.bluebubbles.actions`: Enable/disable specific actions.
*   `channels.bluebubbles.accounts`: Multi-account configuration.

Related global options:
*   `agents.list[].groupChat.mentionPatterns` (or `messages.groupChat.mentionPatterns`).
*   `messages.responsePrefix`.

## [​](https://docs.openclaw.ai/channels/bluebubbles#addressing-/-delivery-targets)

Addressing / delivery targets

Prefer `chat_guid` for stable routing:
*   `chat_guid:iMessage;-;+15555550123` (preferred for groups)
*   `chat_id:123`
*   `chat_identifier:...`
*   Direct handles: `+15555550123`, `user@example.com`
    *   If a direct handle does not have an existing DM chat, OpenClaw will create one via `POST /api/v1/chat/new`. This requires the BlueBubbles Private API to be enabled.

## [​](https://docs.openclaw.ai/channels/bluebubbles#security)

Security

*   Webhook requests are authenticated by comparing `guid`/`password` query params or headers against `channels.bluebubbles.password`. Requests from `localhost` are also accepted.
*   Keep the API password and webhook endpoint secret (treat them like credentials).
*   Localhost trust means a same-host reverse proxy can unintentionally bypass the password. If you proxy the gateway, require auth at the proxy and configure `gateway.trustedProxies`. See [Gateway security](https://docs.openclaw.ai/gateway/security#reverse-proxy-configuration).
*   Enable HTTPS + firewall rules on the BlueBubbles server if exposing it outside your LAN.

## [​](https://docs.openclaw.ai/channels/bluebubbles#troubleshooting)

Troubleshooting

*   If typing/read events stop working, check the BlueBubbles webhook logs and verify the gateway path matches `channels.bluebubbles.webhookPath`.
*   Pairing codes expire after one hour; use `openclaw pairing list bluebubbles` and `openclaw pairing approve bluebubbles <code>`.
*   Reactions require the BlueBubbles private API (`POST /api/v1/message/react`); ensure the server version exposes it.
*   Edit/unsend require macOS 13+ and a compatible BlueBubbles server version. On macOS 26 (Tahoe), edit is currently broken due to private API changes.
*   Group icon updates can be flaky on macOS 26 (Tahoe): the API may return success but the new icon does not sync.
*   OpenClaw auto-hides known-broken actions based on the BlueBubbles server’s macOS version. If edit still appears on macOS 26 (Tahoe), disable it manually with `channels.bluebubbles.actions.edit=false`.
*   For status/health info: `openclaw status --all` or `openclaw status --deep`.

For general channel workflow reference, see [Channels](https://docs.openclaw.ai/channels) and the [Plugins](https://docs.openclaw.ai/tools/plugin) guide.

[Chat Channels](https://docs.openclaw.ai/channels)[Discord](https://docs.openclaw.ai/channels/discord)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
