# cli/backup

Source: https://docs.openclaw.ai/cli/backup

Title: backup - OpenClaw

URL Source: https://docs.openclaw.ai/cli/backup

Markdown Content:
# backup - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/backup#content-area)

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

Gateway and service

backup

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
    *   [backup](https://docs.openclaw.ai/cli/backup)
    *   [daemon](https://docs.openclaw.ai/cli/daemon)
    *   [doctor](https://docs.openclaw.ai/cli/doctor)
    *   [gateway](https://docs.openclaw.ai/cli/gateway)
    *   [health](https://docs.openclaw.ai/cli/health)
    *   [logs](https://docs.openclaw.ai/cli/logs)
    *   [onboard](https://docs.openclaw.ai/cli/onboard)
    *   [reset](https://docs.openclaw.ai/cli/reset)
    *   [secrets](https://docs.openclaw.ai/cli/secrets)
    *   [security](https://docs.openclaw.ai/cli/security)
    *   [setup](https://docs.openclaw.ai/cli/setup)
    *   [status](https://docs.openclaw.ai/cli/status)
    *   [uninstall](https://docs.openclaw.ai/cli/uninstall)
    *   [update](https://docs.openclaw.ai/cli/update)

*   Agents and sessions 
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

*   [openclaw backup](https://docs.openclaw.ai/cli/backup#openclaw-backup)
*   [Notes](https://docs.openclaw.ai/cli/backup#notes)
*   [What gets backed up](https://docs.openclaw.ai/cli/backup#what-gets-backed-up)
*   [Invalid config behavior](https://docs.openclaw.ai/cli/backup#invalid-config-behavior)
*   [Size and performance](https://docs.openclaw.ai/cli/backup#size-and-performance)

Gateway and service

# backup

# [​](https://docs.openclaw.ai/cli/backup#openclaw-backup)

`openclaw backup`

Create a local backup archive for OpenClaw state, config, credentials, sessions, and optionally workspaces.

```
openclaw backup create
openclaw backup create --output ~/Backups
openclaw backup create --dry-run --json
openclaw backup create --verify
openclaw backup create --no-include-workspace
openclaw backup create --only-config
openclaw backup verify ./2026-03-09T00-00-00.000Z-openclaw-backup.tar.gz
```

## [​](https://docs.openclaw.ai/cli/backup#notes)

Notes

*   The archive includes a `manifest.json` file with the resolved source paths and archive layout.
*   Default output is a timestamped `.tar.gz` archive in the current working directory.
*   If the current working directory is inside a backed-up source tree, OpenClaw falls back to your home directory for the default archive location.
*   Existing archive files are never overwritten.
*   Output paths inside the source state/workspace trees are rejected to avoid self-inclusion.
*   `openclaw backup verify <archive>` validates that the archive contains exactly one root manifest, rejects traversal-style archive paths, and checks that every manifest-declared payload exists in the tarball.
*   `openclaw backup create --verify` runs that validation immediately after writing the archive.
*   `openclaw backup create --only-config` backs up just the active JSON config file.

## [​](https://docs.openclaw.ai/cli/backup#what-gets-backed-up)

What gets backed up

`openclaw backup create` plans backup sources from your local OpenClaw install:
*   The state directory returned by OpenClaw’s local state resolver, usually `~/.openclaw`
*   The active config file path
*   The OAuth / credentials directory
*   Workspace directories discovered from the current config, unless you pass `--no-include-workspace`

If you use `--only-config`, OpenClaw skips state, credentials, and workspace discovery and archives only the active config file path.OpenClaw canonicalizes paths before building the archive. If config, credentials, or a workspace already live inside the state directory, they are not duplicated as separate top-level backup sources. Missing paths are skipped.The archive payload stores file contents from those source trees, and the embedded `manifest.json` records the resolved absolute source paths plus the archive layout used for each asset.
## [​](https://docs.openclaw.ai/cli/backup#invalid-config-behavior)

Invalid config behavior

`openclaw backup` intentionally bypasses the normal config preflight so it can still help during recovery. Because workspace discovery depends on a valid config, `openclaw backup create` now fails fast when the config file exists but is invalid and workspace backup is still enabled.If you still want a partial backup in that situation, rerun:

```
openclaw backup create --no-include-workspace
```

That keeps state, config, and credentials in scope while skipping workspace discovery entirely.If you only need a copy of the config file itself, `--only-config` also works when the config is malformed because it does not rely on parsing the config for workspace discovery.
## [​](https://docs.openclaw.ai/cli/backup#size-and-performance)

Size and performance

OpenClaw does not enforce a built-in maximum backup size or per-file size limit.Practical limits come from the local machine and destination filesystem:
*   Available space for the temporary archive write plus the final archive
*   Time to walk large workspace trees and compress them into a `.tar.gz`
*   Time to rescan the archive if you use `openclaw backup create --verify` or run `openclaw backup verify`
*   Filesystem behavior at the destination path. OpenClaw prefers a no-overwrite hard-link publish step and falls back to exclusive copy when hard links are unsupported

Large workspaces are usually the main driver of archive size. If you want a smaller or faster backup, use `--no-include-workspace`.For the smallest archive, use `--only-config`.

[CLI Reference](https://docs.openclaw.ai/cli)[daemon](https://docs.openclaw.ai/cli/daemon)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
