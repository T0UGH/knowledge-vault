# cli/daemon

Source: https://docs.openclaw.ai/cli/daemon

Title: daemon - OpenClaw

URL Source: https://docs.openclaw.ai/cli/daemon

Markdown Content:
# daemon - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/daemon#content-area)

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

daemon

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

*   [openclaw daemon](https://docs.openclaw.ai/cli/daemon#openclaw-daemon)
*   [Usage](https://docs.openclaw.ai/cli/daemon#usage)
*   [Subcommands](https://docs.openclaw.ai/cli/daemon#subcommands)
*   [Common options](https://docs.openclaw.ai/cli/daemon#common-options)
*   [Prefer](https://docs.openclaw.ai/cli/daemon#prefer)

Gateway and service

# daemon

# [​](https://docs.openclaw.ai/cli/daemon#openclaw-daemon)

`openclaw daemon`

Legacy alias for Gateway service management commands.`openclaw daemon ...` maps to the same service control surface as `openclaw gateway ...` service commands.
## [​](https://docs.openclaw.ai/cli/daemon#usage)

Usage

```
openclaw daemon status
openclaw daemon install
openclaw daemon start
openclaw daemon stop
openclaw daemon restart
openclaw daemon uninstall
```

## [​](https://docs.openclaw.ai/cli/daemon#subcommands)

Subcommands

*   `status`: show service install state and probe Gateway health
*   `install`: install service (`launchd`/`systemd`/`schtasks`)
*   `uninstall`: remove service
*   `start`: start service
*   `stop`: stop service
*   `restart`: restart service

## [​](https://docs.openclaw.ai/cli/daemon#common-options)

Common options

*   `status`: `--url`, `--token`, `--password`, `--timeout`, `--no-probe`, `--require-rpc`, `--deep`, `--json`
*   `install`: `--port`, `--runtime <node|bun>`, `--token`, `--force`, `--json`
*   lifecycle (`uninstall|start|stop|restart`): `--json`

Notes:
*   `status` resolves configured auth SecretRefs for probe auth when possible.
*   If a required auth SecretRef is unresolved in this command path, `daemon status --json` reports `rpc.authWarning` when probe connectivity/auth fails; pass `--token`/`--password` explicitly or resolve the secret source first.
*   If the probe succeeds, unresolved auth-ref warnings are suppressed to avoid false positives.
*   On Linux systemd installs, `status` token-drift checks include both `Environment=` and `EnvironmentFile=` unit sources.
*   When token auth requires a token and `gateway.auth.token` is SecretRef-managed, `install` validates that the SecretRef is resolvable but does not persist the resolved token into service environment metadata.
*   If token auth requires a token and the configured token SecretRef is unresolved, install fails closed.
*   If both `gateway.auth.token` and `gateway.auth.password` are configured and `gateway.auth.mode` is unset, install is blocked until mode is set explicitly.

## [​](https://docs.openclaw.ai/cli/daemon#prefer)

Prefer

Use [`openclaw gateway`](https://docs.openclaw.ai/cli/gateway) for current docs and examples.

[backup](https://docs.openclaw.ai/cli/backup)[doctor](https://docs.openclaw.ai/cli/doctor)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
