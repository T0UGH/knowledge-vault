# help/scripts

Source: https://docs.openclaw.ai/help/scripts

Title: Scripts - OpenClaw

URL Source: https://docs.openclaw.ai/help/scripts

Markdown Content:
# Scripts - OpenClaw

[Skip to main content](https://docs.openclaw.ai/help/scripts#content-area)

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

Environment and debugging

Scripts

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Help

*   [Help](https://docs.openclaw.ai/help)
*   [General Troubleshooting](https://docs.openclaw.ai/help/troubleshooting)
*   [FAQ](https://docs.openclaw.ai/help/faq)

##### Community

*   [OpenClaw Lore](https://docs.openclaw.ai/start/lore)

##### Environment and debugging

*   [Environment Variables](https://docs.openclaw.ai/help/environment)
*   [Debugging](https://docs.openclaw.ai/help/debugging)
*   [Testing](https://docs.openclaw.ai/help/testing)
*   [Scripts](https://docs.openclaw.ai/help/scripts)
*   [Node + tsx Crash](https://docs.openclaw.ai/debug/node-issue)
*   [Diagnostics Flags](https://docs.openclaw.ai/diagnostics/flags)

##### Compaction internals

*   [Session Management Deep Dive](https://docs.openclaw.ai/reference/session-management-compaction)

##### Developer setup

*   [Setup](https://docs.openclaw.ai/start/setup)
*   [Pi Development Workflow](https://docs.openclaw.ai/pi-dev)

##### Contributing

*   [CI Pipeline](https://docs.openclaw.ai/ci)

##### Docs meta

*   [Docs Hubs](https://docs.openclaw.ai/start/hubs)
*   [Docs directory](https://docs.openclaw.ai/start/docs-directory)

On this page

*   [Scripts](https://docs.openclaw.ai/help/scripts#scripts)
*   [Conventions](https://docs.openclaw.ai/help/scripts#conventions)
*   [Auth monitoring scripts](https://docs.openclaw.ai/help/scripts#auth-monitoring-scripts)
*   [When adding scripts](https://docs.openclaw.ai/help/scripts#when-adding-scripts)

Environment and debugging

# Scripts

# [​](https://docs.openclaw.ai/help/scripts#scripts)

Scripts

The `scripts/` directory contains helper scripts for local workflows and ops tasks. Use these when a task is clearly tied to a script; otherwise prefer the CLI.
## [​](https://docs.openclaw.ai/help/scripts#conventions)

Conventions

*   Scripts are **optional** unless referenced in docs or release checklists.
*   Prefer CLI surfaces when they exist (example: auth monitoring uses `openclaw models status --check`).
*   Assume scripts are host‑specific; read them before running on a new machine.

## [​](https://docs.openclaw.ai/help/scripts#auth-monitoring-scripts)

Auth monitoring scripts

Auth monitoring scripts are documented here: [/automation/auth-monitoring](https://docs.openclaw.ai/automation/auth-monitoring)
## [​](https://docs.openclaw.ai/help/scripts#when-adding-scripts)

When adding scripts

*   Keep scripts focused and documented.
*   Add a short entry in the relevant doc (or create one if missing).

[Testing](https://docs.openclaw.ai/help/testing)[Node + tsx Crash](https://docs.openclaw.ai/debug/node-issue)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
