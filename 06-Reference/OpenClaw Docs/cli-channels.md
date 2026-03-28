# cli/channels

Source: https://docs.openclaw.ai/cli/channels

Title: channels - OpenClaw

URL Source: https://docs.openclaw.ai/cli/channels

Markdown Content:
# channels - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/channels#content-area)

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

Channels and messaging

channels

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
*   Channels and messaging 
    *   [channels](https://docs.openclaw.ai/cli/channels)
    *   [devices](https://docs.openclaw.ai/cli/devices)
    *   [directory](https://docs.openclaw.ai/cli/directory)
    *   [pairing](https://docs.openclaw.ai/cli/pairing)
    *   [qr](https://docs.openclaw.ai/cli/qr)
    *   [voicecall](https://docs.openclaw.ai/cli/voicecall)

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

*   [openclaw channels](https://docs.openclaw.ai/cli/channels#openclaw-channels)
*   [Common commands](https://docs.openclaw.ai/cli/channels#common-commands)
*   [Add / remove accounts](https://docs.openclaw.ai/cli/channels#add-%2F-remove-accounts)
*   [Login / logout (interactive)](https://docs.openclaw.ai/cli/channels#login-%2F-logout-interactive)
*   [Troubleshooting](https://docs.openclaw.ai/cli/channels#troubleshooting)
*   [Capabilities probe](https://docs.openclaw.ai/cli/channels#capabilities-probe)
*   [Resolve names to IDs](https://docs.openclaw.ai/cli/channels#resolve-names-to-ids)

Channels and messaging

# channels

# [​](https://docs.openclaw.ai/cli/channels#openclaw-channels)

`openclaw channels`

Manage chat channel accounts and their runtime status on the Gateway.Related docs:
*   Channel guides: [Channels](https://docs.openclaw.ai/channels/index)
*   Gateway configuration: [Configuration](https://docs.openclaw.ai/gateway/configuration)

## [​](https://docs.openclaw.ai/cli/channels#common-commands)

Common commands

```
openclaw channels list
openclaw channels status
openclaw channels capabilities
openclaw channels capabilities --channel discord --target channel:123
openclaw channels resolve --channel slack "#general" "@jane"
openclaw channels logs --channel all
```

## [​](https://docs.openclaw.ai/cli/channels#add-/-remove-accounts)

Add / remove accounts

```
openclaw channels add --channel telegram --token <bot-token>
openclaw channels add --channel nostr --private-key "$NOSTR_PRIVATE_KEY"
openclaw channels remove --channel telegram --delete
```

Tip: `openclaw channels add --help` shows per-channel flags (token, private key, app token, signal-cli paths, etc).When you run `openclaw channels add` without flags, the interactive wizard can prompt:
*   account ids per selected channel
*   optional display names for those accounts
*   `Bind configured channel accounts to agents now?`

If you confirm bind now, the wizard asks which agent should own each configured channel account and writes account-scoped routing bindings.You can also manage the same routing rules later with `openclaw agents bindings`, `openclaw agents bind`, and `openclaw agents unbind` (see [agents](https://docs.openclaw.ai/cli/agents)).When you add a non-default account to a channel that is still using single-account top-level settings (no `channels.<channel>.accounts` entries yet), OpenClaw moves account-scoped single-account top-level values into `channels.<channel>.accounts.default`, then writes the new account. This preserves the original account behavior while moving to the multi-account shape.Routing behavior stays consistent:
*   Existing channel-only bindings (no `accountId`) continue to match the default account.
*   `channels add` does not auto-create or rewrite bindings in non-interactive mode.
*   Interactive setup can optionally add account-scoped bindings.

If your config was already in a mixed state (named accounts present, missing `default`, and top-level single-account values still set), run `openclaw doctor --fix` to move account-scoped values into `accounts.default`.
## [​](https://docs.openclaw.ai/cli/channels#login-/-logout-interactive)

Login / logout (interactive)

```
openclaw channels login --channel whatsapp
openclaw channels logout --channel whatsapp
```

## [​](https://docs.openclaw.ai/cli/channels#troubleshooting)

Troubleshooting

*   Run `openclaw status --deep` for a broad probe.
*   Use `openclaw doctor` for guided fixes.
*   `openclaw channels list` prints `Claude: HTTP 403 ... user:profile` → usage snapshot needs the `user:profile` scope. Use `--no-usage`, or provide a claude.ai session key (`CLAUDE_WEB_SESSION_KEY` / `CLAUDE_WEB_COOKIE`), or re-auth via Claude Code CLI.
*   `openclaw channels status` falls back to config-only summaries when the gateway is unreachable. If a supported channel credential is configured via SecretRef but unavailable in the current command path, it reports that account as configured with degraded notes instead of showing it as not configured.

## [​](https://docs.openclaw.ai/cli/channels#capabilities-probe)

Capabilities probe

Fetch provider capability hints (intents/scopes where available) plus static feature support:

```
openclaw channels capabilities
openclaw channels capabilities --channel discord --target channel:123
```

Notes:
*   `--channel` is optional; omit it to list every channel (including extensions).
*   `--target` accepts `channel:<id>` or a raw numeric channel id and only applies to Discord.
*   Probes are provider-specific: Discord intents + optional channel permissions; Slack bot + user scopes; Telegram bot flags + webhook; Signal daemon version; Microsoft Teams app token + Graph roles/scopes (annotated where known). Channels without probes report `Probe: unavailable`.

## [​](https://docs.openclaw.ai/cli/channels#resolve-names-to-ids)

Resolve names to IDs

Resolve channel/user names to IDs using the provider directory:

```
openclaw channels resolve --channel slack "#general" "@jane"
openclaw channels resolve --channel discord "My Server/#support" "@someone"
openclaw channels resolve --channel matrix "Project Room"
```

Notes:
*   Use `--kind user|group|auto` to force the target type.
*   Resolution prefers active matches when multiple entries share the same name.
*   `channels resolve` is read-only. If a selected account is configured via SecretRef but that credential is unavailable in the current command path, the command returns degraded unresolved results with notes instead of aborting the entire run.

[system](https://docs.openclaw.ai/cli/system)[devices](https://docs.openclaw.ai/cli/devices)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
