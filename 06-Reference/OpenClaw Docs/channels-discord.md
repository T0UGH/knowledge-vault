# channels/discord

Source: https://docs.openclaw.ai/channels/discord

Title: Discord - OpenClaw

URL Source: https://docs.openclaw.ai/channels/discord

Markdown Content:
## Discord (Bot API)

Status: ready for DMs and guild channels via the official Discord gateway.

## Quick setup

You will need to create a new application with a bot, add the bot to your server, and pair it to OpenClaw. We recommend adding your bot to your own private server. If you don’t have one yet, [create one first](https://support.discord.com/hc/en-us/articles/204849977-How-do-I-create-a-server) (choose **Create My Own > For me and my friends**).

1

2

3

4

5

6

7

8

9

## Recommended: Set up a guild workspace

Once DMs are working, you can set up your Discord server as a full workspace where each channel gets its own agent session with its own context. This is recommended for private servers where it’s just you and your bot.

1

2

3

Now create some channels on your Discord server and start chatting. Your agent can see the channel name, and each channel gets its own isolated session — so you can set up `#coding`, `#home`, `#research`, or whatever fits your workflow.

## Runtime model

*   Gateway owns the Discord connection.
*   Reply routing is deterministic: Discord inbound replies back to Discord.
*   By default (`session.dmScope=main`), direct chats share the agent main session (`agent:main:main`).
*   Guild channels are isolated session keys (`agent:<agentId>:discord:channel:<channelId>`).
*   Group DMs are ignored by default (`channels.discord.dm.groupEnabled=false`).
*   Native slash commands run in isolated command sessions (`agent:<agentId>:discord:slash:<userId>`), while still carrying `CommandTargetSessionKey` to the routed conversation session.

## Forum channels

Discord forum and media channels only accept thread posts. OpenClaw supports two ways to create them:

*   Send a message to the forum parent (`channel:<forumId>`) to auto-create a thread. The thread title uses the first non-empty line of your message.
*   Use `openclaw message thread create` to create a thread directly. Do not pass `--message-id` for forum channels.

Example: send to forum parent to create a thread

```
openclaw message send --channel discord --target channel:<forumId> \
  --message "Topic title\nBody of the post"
```

Example: create a forum thread explicitly

```
openclaw message thread create --channel discord --target channel:<forumId> \
  --thread-name "Topic title" --message "Body of the post"
```

Forum parents do not accept Discord components. If you need components, send to the thread itself (`channel:<threadId>`).

## Interactive components

OpenClaw supports Discord components v2 containers for agent messages. Use the message tool with a `components` payload. Interaction results are routed back to the agent as normal inbound messages and follow the existing Discord `replyToMode` settings.Supported blocks:

*   `text`, `section`, `separator`, `actions`, `media-gallery`, `file`
*   Action rows allow up to 5 buttons or a single select menu
*   Select types: `string`, `user`, `role`, `mentionable`, `channel`

By default, components are single use. Set `components.reusable=true` to allow buttons, selects, and forms to be used multiple times until they expire.To restrict who can click a button, set `allowedUsers` on that button (Discord user IDs, tags, or `*`). When configured, unmatched users receive an ephemeral denial.The `/model` and `/models` slash commands open an interactive model picker with provider and model dropdowns plus a Submit step. The picker reply is ephemeral and only the invoking user can use it.File attachments:

*   `file` blocks must point to an attachment reference (`attachment://<filename>`)
*   Provide the attachment via `media`/`path`/`filePath` (single file); use `media-gallery` for multiple files
*   Use `filename` to override the upload name when it should match the attachment reference

Modal forms:

*   Add `components.modal` with up to 5 fields
*   Field types: `text`, `checkbox`, `radio`, `select`, `role-select`, `user-select`
*   OpenClaw adds a trigger button automatically

Example:

```
{
  channel: "discord",
  action: "send",
  to: "channel:123456789012345678",
  message: "Optional fallback text",
  components: {
    reusable: true,
    text: "Choose a path",
    blocks: [
      {
        type: "actions",
        buttons: [
          {
            label: "Approve",
            style: "success",
            allowedUsers: ["123456789012345678"],
          },
          { label: "Decline", style: "danger" },
        ],
      },
      {
        type: "actions",
        select: {
          type: "string",
          placeholder: "Pick an option",
          options: [
            { label: "Option A", value: "a" },
            { label: "Option B", value: "b" },
          ],
        },
      },
    ],
    modal: {
      title: "Details",
      triggerLabel: "Open form",
      fields: [
        { type: "text", label: "Requester" },
        {
          type: "select",
          label: "Priority",
          options: [
            { label: "Low", value: "low" },
            { label: "High", value: "high" },
          ],
        },
      ],
    },
  },
}
```

## Access control and routing

*   DM policy

*   Guild policy

*   Mentions and group DMs

`channels.discord.dmPolicy` controls DM access (legacy: `channels.discord.dm.policy`):

*   `pairing` (default)
*   `allowlist`
*   `open` (requires `channels.discord.allowFrom` to include `"*"`; legacy: `channels.discord.dm.allowFrom`)
*   `disabled`

If DM policy is not open, unknown users are blocked (or prompted for pairing in `pairing` mode).Multi-account precedence:

*   `channels.discord.accounts.default.allowFrom` applies only to the `default` account.
*   Named accounts inherit `channels.discord.allowFrom` when their own `allowFrom` is unset.
*   Named accounts do not inherit `channels.discord.accounts.default.allowFrom`.

DM target format for delivery:

*   `user:<id>`
*   `<@id>` mention

Bare numeric IDs are ambiguous and rejected unless an explicit user/channel target kind is provided.

Guild handling is controlled by `channels.discord.groupPolicy`:

*   `open`
*   `allowlist`
*   `disabled`

Secure baseline when `channels.discord` exists is `allowlist`.`allowlist` behavior:

*   guild must match `channels.discord.guilds` (`id` preferred, slug accepted)
*   optional sender allowlists: `users` (stable IDs recommended) and `roles` (role IDs only); if either is configured, senders are allowed when they match `users` OR `roles`
*   direct name/tag matching is disabled by default; enable `channels.discord.dangerouslyAllowNameMatching: true` only as break-glass compatibility mode
*   names/tags are supported for `users`, but IDs are safer; `openclaw security audit` warns when name/tag entries are used
*   if a guild has `channels` configured, non-listed channels are denied
*   if a guild has no `channels` block, all channels in that allowlisted guild are allowed

Example:

```
{
  channels: {
    discord: {
      groupPolicy: "allowlist",
      guilds: {
        "123456789012345678": {
          requireMention: true,
          ignoreOtherMentions: true,
          users: ["987654321098765432"],
          roles: ["123456789012345678"],
          channels: {
            general: { allow: true },
            help: { allow: true, requireMention: true },
          },
        },
      },
    },
  },
}
```

If you only set `DISCORD_BOT_TOKEN` and do not create a `channels.discord` block, runtime fallback is `groupPolicy="allowlist"` (with a warning in logs), even if `channels.defaults.groupPolicy` is `open`.

Guild messages are mention-gated by default.Mention detection includes:

*   explicit bot mention
*   configured mention patterns (`agents.list[].groupChat.mentionPatterns`, fallback `messages.groupChat.mentionPatterns`)
*   implicit reply-to-bot behavior in supported cases

`requireMention` is configured per guild/channel (`channels.discord.guilds...`). `ignoreOtherMentions` optionally drops messages that mention another user/role but not the bot (excluding @everyone/@here).Group DMs:

*   default: ignored (`dm.groupEnabled=false`)
*   optional allowlist via `dm.groupChannels` (channel IDs or slugs)

### Role-based agent routing

Use `bindings[].match.roles` to route Discord guild members to different agents by role ID. Role-based bindings accept role IDs only and are evaluated after peer or parent-peer bindings and before guild-only bindings. If a binding also sets other match fields (for example `peer` + `guildId` + `roles`), all configured fields must match.

```
{
  bindings: [
    {
      agentId: "opus",
      match: {
        channel: "discord",
        guildId: "123456789012345678",
        roles: ["111111111111111111"],
      },
    },
    {
      agentId: "sonnet",
      match: {
        channel: "discord",
        guildId: "123456789012345678",
      },
    },
  ],
}
```

## Developer Portal setup

## Native commands and command auth

*   `commands.native` defaults to `"auto"` and is enabled for Discord.
*   Per-channel override: `channels.discord.commands.native`.
*   `commands.native=false` explicitly clears previously registered Discord native commands.
*   Native command auth uses the same Discord allowlists/policies as normal message handling.
*   Commands may still be visible in Discord UI for users who are not authorized; execution still enforces OpenClaw auth and returns “not authorized”.

See [Slash commands](https://docs.openclaw.ai/tools/slash-commands) for command catalog and behavior.Default slash command settings:

*   `ephemeral: true`

## Feature details

## Tools and action gates

Discord message actions include messaging, channel admin, moderation, presence, and metadata actions.Core examples:

*   messaging: `sendMessage`, `readMessages`, `editMessage`, `deleteMessage`, `threadReply`
*   reactions: `react`, `reactions`, `emojiList`
*   moderation: `timeout`, `kick`, `ban`
*   presence: `setPresence`

Action gates live under `channels.discord.actions.*`.Default gate behavior:

| Action group | Default |
| --- | --- |
| reactions, messages, threads, pins, polls, search, memberInfo, roleInfo, channelInfo, channels, voiceStatus, events, stickers, emojiUploads, stickerUploads, permissions | enabled |
| roles | disabled |
| moderation | disabled |
| presence | disabled |

## Components v2 UI

OpenClaw uses Discord components v2 for exec approvals and cross-context markers. Discord message actions can also accept `components` for custom UI (advanced; requires Carbon component instances), while legacy `embeds` remain available but are not recommended.

*   `channels.discord.ui.components.accentColor` sets the accent color used by Discord component containers (hex).
*   Set per account with `channels.discord.accounts.<id>.ui.components.accentColor`.
*   `embeds` are ignored when components v2 are present.

Example:

```
{
  channels: {
    discord: {
      ui: {
        components: {
          accentColor: "#5865F2",
        },
      },
    },
  },
}
```

## Voice channels

OpenClaw can join Discord voice channels for realtime, continuous conversations. This is separate from voice message attachments.Requirements:

*   Enable native commands (`commands.native` or `channels.discord.commands.native`).
*   Configure `channels.discord.voice`.
*   The bot needs Connect + Speak permissions in the target voice channel.

Use the Discord-only native command `/vc join|leave|status` to control sessions. The command uses the account default agent and follows the same allowlist and group policy rules as other Discord commands.Auto-join example:

```
{
  channels: {
    discord: {
      voice: {
        enabled: true,
        autoJoin: [
          {
            guildId: "123456789012345678",
            channelId: "234567890123456789",
          },
        ],
        daveEncryption: true,
        decryptionFailureTolerance: 24,
        tts: {
          provider: "openai",
          openai: { voice: "alloy" },
        },
      },
    },
  },
}
```

Notes:

*   `voice.tts` overrides `messages.tts` for voice playback only.
*   Voice transcript turns derive owner status from Discord `allowFrom` (or `dm.allowFrom`); non-owner speakers cannot access owner-only tools (for example `gateway` and `cron`).
*   Voice is enabled by default; set `channels.discord.voice.enabled=false` to disable it.
*   `voice.daveEncryption` and `voice.decryptionFailureTolerance` pass through to `@discordjs/voice` join options.
*   `@discordjs/voice` defaults are `daveEncryption=true` and `decryptionFailureTolerance=24` if unset.
*   OpenClaw also watches receive decrypt failures and auto-recovers by leaving/rejoining the voice channel after repeated failures in a short window.
*   If receive logs repeatedly show `DecryptionFailed(UnencryptedWhenPassthroughDisabled)`, this may be the upstream `@discordjs/voice` receive bug tracked in [discord.js #11419](https://github.com/discordjs/discord.js/issues/11419).

## Voice messages

Discord voice messages show a waveform preview and require OGG/Opus audio plus metadata. OpenClaw generates the waveform automatically, but it needs `ffmpeg` and `ffprobe` available on the gateway host to inspect and convert audio files.Requirements and constraints:

*   Provide a **local file path** (URLs are rejected).
*   Omit text content (Discord does not allow text + voice message in the same payload).
*   Any audio format is accepted; OpenClaw converts to OGG/Opus when needed.

Example:

```
message(action="send", channel="discord", target="channel:123", path="/path/to/audio.mp3", asVoice=true)
```

## Troubleshooting

## Configuration reference pointers

Primary reference:

*   [Configuration reference - Discord](https://docs.openclaw.ai/gateway/configuration-reference#discord)

High-signal Discord fields:

*   startup/auth: `enabled`, `token`, `accounts.*`, `allowBots`
*   policy: `groupPolicy`, `dm.*`, `guilds.*`, `guilds.*.channels.*`
*   command: `commands.native`, `commands.useAccessGroups`, `configWrites`, `slashCommand.*`
*   event queue: `eventQueue.listenerTimeout` (listener budget), `eventQueue.maxQueueSize`, `eventQueue.maxConcurrency`
*   inbound worker: `inboundWorker.runTimeoutMs`
*   reply/history: `replyToMode`, `historyLimit`, `dmHistoryLimit`, `dms.*.historyLimit`
*   delivery: `textChunkLimit`, `chunkMode`, `maxLinesPerMessage`
*   streaming: `streaming` (legacy alias: `streamMode`), `draftChunk`, `blockStreaming`, `blockStreamingCoalesce`
*   media/retry: `mediaMaxMb`, `retry`
    *   `mediaMaxMb` caps outbound Discord uploads (default: `8MB`)

*   actions: `actions.*`
*   presence: `activity`, `status`, `activityType`, `activityUrl`
*   UI: `ui.components.accentColor`
*   features: `threadBindings`, top-level `bindings[]` (`type: "acp"`), `pluralkit`, `execApprovals`, `intents`, `agentComponents`, `heartbeat`, `responsePrefix`

## Safety and operations

*   Treat bot tokens as secrets (`DISCORD_BOT_TOKEN` preferred in supervised environments).
*   Grant least-privilege Discord permissions.
*   If command deploy/state is stale, restart gateway and re-check with `openclaw channels status --probe`.

*   [Pairing](https://docs.openclaw.ai/channels/pairing)
*   [Channel routing](https://docs.openclaw.ai/channels/channel-routing)
*   [Multi-agent routing](https://docs.openclaw.ai/concepts/multi-agent)
*   [Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)
*   [Slash commands](https://docs.openclaw.ai/tools/slash-commands)
