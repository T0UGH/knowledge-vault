# cli/message

Source: https://docs.openclaw.ai/cli/message

Title: message - OpenClaw

URL Source: https://docs.openclaw.ai/cli/message

Markdown Content:
# message - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/message#content-area)

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

Agents and sessions

message

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
    *   [agent](https://docs.openclaw.ai/cli/agent)
    *   [agents](https://docs.openclaw.ai/cli/agents)
    *   [hooks](https://docs.openclaw.ai/cli/hooks)
    *   [memory](https://docs.openclaw.ai/cli/memory)
    *   [message](https://docs.openclaw.ai/cli/message)
    *   [models](https://docs.openclaw.ai/cli/models)
    *   [sessions](https://docs.openclaw.ai/cli/sessions)
    *   [system](https://docs.openclaw.ai/cli/system)

*   Channels and messaging 
*   Tools and execution 
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

*   [openclaw message](https://docs.openclaw.ai/cli/message#openclaw-message)
*   [Usage](https://docs.openclaw.ai/cli/message#usage)
*   [Common flags](https://docs.openclaw.ai/cli/message#common-flags)
*   [SecretRef behavior](https://docs.openclaw.ai/cli/message#secretref-behavior)
*   [Actions](https://docs.openclaw.ai/cli/message#actions)
*   [Core](https://docs.openclaw.ai/cli/message#core)
*   [Threads](https://docs.openclaw.ai/cli/message#threads)
*   [Emojis](https://docs.openclaw.ai/cli/message#emojis)
*   [Stickers](https://docs.openclaw.ai/cli/message#stickers)
*   [Roles / Channels / Members / Voice](https://docs.openclaw.ai/cli/message#roles-%2F-channels-%2F-members-%2F-voice)
*   [Events](https://docs.openclaw.ai/cli/message#events)
*   [Moderation (Discord)](https://docs.openclaw.ai/cli/message#moderation-discord)
*   [Broadcast](https://docs.openclaw.ai/cli/message#broadcast)
*   [Examples](https://docs.openclaw.ai/cli/message#examples)

Agents and sessions

# message

# [​](https://docs.openclaw.ai/cli/message#openclaw-message)

`openclaw message`

Single outbound command for sending messages and channel actions (Discord/Google Chat/Slack/Mattermost (plugin)/Telegram/WhatsApp/Signal/iMessage/Microsoft Teams).
## [​](https://docs.openclaw.ai/cli/message#usage)

Usage

```
openclaw message <subcommand> [flags]
```

Channel selection:
*   `--channel` required if more than one channel is configured.
*   If exactly one channel is configured, it becomes the default.
*   Values: `whatsapp|telegram|discord|googlechat|slack|mattermost|signal|imessage|msteams` (Mattermost requires plugin)

Target formats (`--target`):
*   WhatsApp: E.164 or group JID
*   Telegram: chat id or `@username`
*   Discord: `channel:<id>` or `user:<id>` (or `<@id>` mention; raw numeric ids are treated as channels)
*   Google Chat: `spaces/<spaceId>` or `users/<userId>`
*   Slack: `channel:<id>` or `user:<id>` (raw channel id is accepted)
*   Mattermost (plugin): `channel:<id>`, `user:<id>`, or `@username` (bare ids are treated as channels)
*   Signal: `+E.164`, `group:<id>`, `signal:+E.164`, `signal:group:<id>`, or `username:<name>`/`u:<name>`
*   iMessage: handle, `chat_id:<id>`, `chat_guid:<guid>`, or `chat_identifier:<id>`
*   Microsoft Teams: conversation id (`19:...@thread.tacv2`) or `conversation:<id>` or `user:<aad-object-id>`

Name lookup:
*   For supported providers (Discord/Slack/etc), channel names like `Help` or `#help` are resolved via the directory cache.
*   On cache miss, OpenClaw will attempt a live directory lookup when the provider supports it.

## [​](https://docs.openclaw.ai/cli/message#common-flags)

Common flags

*   `--channel <name>`
*   `--account <id>`
*   `--target <dest>` (target channel or user for send/poll/read/etc)
*   `--targets <name>` (repeat; broadcast only)
*   `--json`
*   `--dry-run`
*   `--verbose`

## [​](https://docs.openclaw.ai/cli/message#secretref-behavior)

SecretRef behavior

*   `openclaw message` resolves supported channel SecretRefs before running the selected action.
*   Resolution is scoped to the active action target when possible:
    *   channel-scoped when `--channel` is set (or inferred from prefixed targets like `discord:...`)
    *   account-scoped when `--account` is set (channel globals + selected account surfaces)
    *   when `--account` is omitted, OpenClaw does not force a `default` account SecretRef scope

*   Unresolved SecretRefs on unrelated channels do not block a targeted message action.
*   If the selected channel/account SecretRef is unresolved, the command fails closed for that action.

## [​](https://docs.openclaw.ai/cli/message#actions)

Actions

### [​](https://docs.openclaw.ai/cli/message#core)

Core

*   `send`
    *   Channels: WhatsApp/Telegram/Discord/Google Chat/Slack/Mattermost (plugin)/Signal/iMessage/Microsoft Teams
    *   Required: `--target`, plus `--message` or `--media`
    *   Optional: `--media`, `--reply-to`, `--thread-id`, `--gif-playback`
    *   Telegram only: `--buttons` (requires `channels.telegram.capabilities.inlineButtons` to allow it)
    *   Telegram only: `--force-document` (send images and GIFs as documents to avoid Telegram compression)
    *   Telegram only: `--thread-id` (forum topic id)
    *   Slack only: `--thread-id` (thread timestamp; `--reply-to` uses the same field)
    *   WhatsApp only: `--gif-playback`

*   `poll`
    *   Channels: WhatsApp/Telegram/Discord/Matrix/Microsoft Teams
    *   Required: `--target`, `--poll-question`, `--poll-option` (repeat)
    *   Optional: `--poll-multi`
    *   Discord only: `--poll-duration-hours`, `--silent`, `--message`
    *   Telegram only: `--poll-duration-seconds` (5-600), `--silent`, `--poll-anonymous` / `--poll-public`, `--thread-id`

*   `react`
    *   Channels: Discord/Google Chat/Slack/Telegram/WhatsApp/Signal
    *   Required: `--message-id`, `--target`
    *   Optional: `--emoji`, `--remove`, `--participant`, `--from-me`, `--target-author`, `--target-author-uuid`
    *   Note: `--remove` requires `--emoji` (omit `--emoji` to clear own reactions where supported; see /tools/reactions)
    *   WhatsApp only: `--participant`, `--from-me`
    *   Signal group reactions: `--target-author` or `--target-author-uuid` required

*   `reactions`
    *   Channels: Discord/Google Chat/Slack
    *   Required: `--message-id`, `--target`
    *   Optional: `--limit`

*   `read`
    *   Channels: Discord/Slack
    *   Required: `--target`
    *   Optional: `--limit`, `--before`, `--after`
    *   Discord only: `--around`

*   `edit`
    *   Channels: Discord/Slack
    *   Required: `--message-id`, `--message`, `--target`

*   `delete`
    *   Channels: Discord/Slack/Telegram
    *   Required: `--message-id`, `--target`

*   `pin` / `unpin`
    *   Channels: Discord/Slack
    *   Required: `--message-id`, `--target`

*   `pins` (list)
    *   Channels: Discord/Slack
    *   Required: `--target`

*   `permissions`
    *   Channels: Discord
    *   Required: `--target`

*   `search`
    *   Channels: Discord
    *   Required: `--guild-id`, `--query`
    *   Optional: `--channel-id`, `--channel-ids` (repeat), `--author-id`, `--author-ids` (repeat), `--limit`

### [​](https://docs.openclaw.ai/cli/message#threads)

Threads

*   `thread create`
    *   Channels: Discord
    *   Required: `--thread-name`, `--target` (channel id)
    *   Optional: `--message-id`, `--message`, `--auto-archive-min`

*   `thread list`
    *   Channels: Discord
    *   Required: `--guild-id`
    *   Optional: `--channel-id`, `--include-archived`, `--before`, `--limit`

*   `thread reply`
    *   Channels: Discord
    *   Required: `--target` (thread id), `--message`
    *   Optional: `--media`, `--reply-to`

### [​](https://docs.openclaw.ai/cli/message#emojis)

Emojis

*   `emoji list`
    *   Discord: `--guild-id`
    *   Slack: no extra flags

*   `emoji upload`
    *   Channels: Discord
    *   Required: `--guild-id`, `--emoji-name`, `--media`
    *   Optional: `--role-ids` (repeat)

### [​](https://docs.openclaw.ai/cli/message#stickers)

Stickers

*   `sticker send`
    *   Channels: Discord
    *   Required: `--target`, `--sticker-id` (repeat)
    *   Optional: `--message`

*   `sticker upload`
    *   Channels: Discord
    *   Required: `--guild-id`, `--sticker-name`, `--sticker-desc`, `--sticker-tags`, `--media`

### [​](https://docs.openclaw.ai/cli/message#roles-/-channels-/-members-/-voice)

Roles / Channels / Members / Voice

*   `role info` (Discord): `--guild-id`
*   `role add` / `role remove` (Discord): `--guild-id`, `--user-id`, `--role-id`
*   `channel info` (Discord): `--target`
*   `channel list` (Discord): `--guild-id`
*   `member info` (Discord/Slack): `--user-id` (+ `--guild-id` for Discord)
*   `voice status` (Discord): `--guild-id`, `--user-id`

### [​](https://docs.openclaw.ai/cli/message#events)

Events

*   `event list` (Discord): `--guild-id`
*   `event create` (Discord): `--guild-id`, `--event-name`, `--start-time`
    *   Optional: `--end-time`, `--desc`, `--channel-id`, `--location`, `--event-type`

### [​](https://docs.openclaw.ai/cli/message#moderation-discord)

Moderation (Discord)

*   `timeout`: `--guild-id`, `--user-id` (optional `--duration-min` or `--until`; omit both to clear timeout)
*   `kick`: `--guild-id`, `--user-id` (+ `--reason`)
*   `ban`: `--guild-id`, `--user-id` (+ `--delete-days`, `--reason`)
    *   `timeout` also supports `--reason`

### [​](https://docs.openclaw.ai/cli/message#broadcast)

Broadcast

*   `broadcast`
    *   Channels: any configured channel; use `--channel all` to target all providers
    *   Required: `--targets` (repeat)
    *   Optional: `--message`, `--media`, `--dry-run`

## [​](https://docs.openclaw.ai/cli/message#examples)

Examples

Send a Discord reply:

```
openclaw message send --channel discord \
  --target channel:123 --message "hi" --reply-to 456
```

Send a Discord message with components:

```
openclaw message send --channel discord \
  --target channel:123 --message "Choose:" \
  --components '{"text":"Choose a path","blocks":[{"type":"actions","buttons":[{"label":"Approve","style":"success"},{"label":"Decline","style":"danger"}]}]}'
```

See [Discord components](https://docs.openclaw.ai/channels/discord#interactive-components) for the full schema.Create a Discord poll:

```
openclaw message poll --channel discord \
  --target channel:123 \
  --poll-question "Snack?" \
  --poll-option Pizza --poll-option Sushi \
  --poll-multi --poll-duration-hours 48
```

Create a Telegram poll (auto-close in 2 minutes):

```
openclaw message poll --channel telegram \
  --target @mychat \
  --poll-question "Lunch?" \
  --poll-option Pizza --poll-option Sushi \
  --poll-duration-seconds 120 --silent
```

Send a Teams proactive message:

```
openclaw message send --channel msteams \
  --target conversation:19:abc@thread.tacv2 --message "hi"
```

Create a Teams poll:

```
openclaw message poll --channel msteams \
  --target conversation:19:abc@thread.tacv2 \
  --poll-question "Lunch?" \
  --poll-option Pizza --poll-option Sushi
```

React in Slack:

```
openclaw message react --channel slack \
  --target C123 --message-id 456 --emoji "✅"
```

React in a Signal group:

```
openclaw message react --channel signal \
  --target signal:group:abc123 --message-id 1737630212345 \
  --emoji "✅" --target-author-uuid 123e4567-e89b-12d3-a456-426614174000
```

Send Telegram inline buttons:

```
openclaw message send --channel telegram --target @mychat --message "Choose:" \
  --buttons '[ [{"text":"Yes","callback_data":"cmd:yes"}], [{"text":"No","callback_data":"cmd:no"}] ]'
```

Send a Telegram image as a document to avoid compression:

```
openclaw message send --channel telegram --target @mychat \
  --media ./diagram.png --force-document
```

[memory](https://docs.openclaw.ai/cli/memory)[models](https://docs.openclaw.ai/cli/models)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
