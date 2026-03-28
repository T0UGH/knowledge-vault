# cli/plugins

Source: https://docs.openclaw.ai/cli/plugins

Title: plugins - OpenClaw

URL Source: https://docs.openclaw.ai/cli/plugins

Markdown Content:
# plugins - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/plugins#content-area)

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

Plugins and skills

plugins

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
*   Channels and messaging 
*   Tools and execution 
*   Configuration 
*   Plugins and skills 
    *   [plugins](https://docs.openclaw.ai/cli/plugins)
    *   [skills](https://docs.openclaw.ai/cli/skills)

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

*   [openclaw plugins](https://docs.openclaw.ai/cli/plugins#openclaw-plugins)
*   [Commands](https://docs.openclaw.ai/cli/plugins#commands)
*   [Install](https://docs.openclaw.ai/cli/plugins#install)
*   [Uninstall](https://docs.openclaw.ai/cli/plugins#uninstall)
*   [Update](https://docs.openclaw.ai/cli/plugins#update)
*   [Inspect](https://docs.openclaw.ai/cli/plugins#inspect)

Plugins and skills

# plugins

# [​](https://docs.openclaw.ai/cli/plugins#openclaw-plugins)

`openclaw plugins`

Manage Gateway plugins/extensions, hook packs, and compatible bundles.Related:
*   Plugin system: [Plugins](https://docs.openclaw.ai/tools/plugin)
*   Bundle compatibility: [Plugin bundles](https://docs.openclaw.ai/plugins/bundles)
*   Plugin manifest + schema: [Plugin manifest](https://docs.openclaw.ai/plugins/manifest)
*   Security hardening: [Security](https://docs.openclaw.ai/gateway/security)

## [​](https://docs.openclaw.ai/cli/plugins#commands)

Commands

```
openclaw plugins list
openclaw plugins install <path-or-spec>
openclaw plugins inspect <id>
openclaw plugins enable <id>
openclaw plugins disable <id>
openclaw plugins uninstall <id>
openclaw plugins doctor
openclaw plugins update <id>
openclaw plugins update --all
openclaw plugins marketplace list <marketplace>
```

Bundled plugins ship with OpenClaw but start disabled. Use `plugins enable` to activate them.Native OpenClaw plugins must ship `openclaw.plugin.json` with an inline JSON Schema (`configSchema`, even if empty). Compatible bundles use their own bundle manifests instead.`plugins list` shows `Format: openclaw` or `Format: bundle`. Verbose list/info output also shows the bundle subtype (`codex`, `claude`, or `cursor`) plus detected bundle capabilities.
### [​](https://docs.openclaw.ai/cli/plugins#install)

Install

```
openclaw plugins install <package>                      # ClawHub first, then npm
openclaw plugins install clawhub:<package>              # ClawHub only
openclaw plugins install <package> --pin                # pin version
openclaw plugins install <path>                         # local path
openclaw plugins install <plugin>@<marketplace>         # marketplace
openclaw plugins install <plugin> --marketplace <name>  # marketplace (explicit)
```

Bare package names are checked against ClawHub first, then npm. Security note: treat plugin installs like running code. Prefer pinned versions.`plugins install` is also the install surface for hook packs that expose `openclaw.hooks` in `package.json`. Use `openclaw hooks` for filtered hook visibility and per-hook enablement, not package installation.Npm specs are **registry-only** (package name + optional **exact version** or **dist-tag**). Git/URL/file specs and semver ranges are rejected. Dependency installs run with `--ignore-scripts` for safety.Bare specs and `@latest` stay on the stable track. If npm resolves either of those to a prerelease, OpenClaw stops and asks you to opt in explicitly with a prerelease tag such as `@beta`/`@rc` or an exact prerelease version such as `@1.2.3-beta.4`.If a bare install spec matches a bundled plugin id (for example `diffs`), OpenClaw installs the bundled plugin directly. To install an npm package with the same name, use an explicit scoped spec (for example `@scope/diffs`).Supported archives: `.zip`, `.tgz`, `.tar.gz`, `.tar`.Claude marketplace installs are also supported.ClawHub installs use an explicit `clawhub:<package>` locator:

```
openclaw plugins install clawhub:openclaw-codex-app-server
openclaw plugins install clawhub:openclaw-codex-app-server@1.2.3
```

OpenClaw now also prefers ClawHub for bare npm-safe plugin specs. It only falls back to npm if ClawHub does not have that package or version:

```
openclaw plugins install openclaw-codex-app-server
```

OpenClaw downloads the package archive from ClawHub, checks the advertised plugin API / minimum gateway compatibility, then installs it through the normal archive path. Recorded installs keep their ClawHub source metadata for later updates.Use `plugin@marketplace` shorthand when the marketplace name exists in Claude’s local registry cache at `~/.claude/plugins/known_marketplaces.json`:

```
openclaw plugins marketplace list <marketplace-name>
openclaw plugins install <plugin-name>@<marketplace-name>
```

Use `--marketplace` when you want to pass the marketplace source explicitly:

```
openclaw plugins install <plugin-name> --marketplace <marketplace-name>
openclaw plugins install <plugin-name> --marketplace <owner/repo>
openclaw plugins install <plugin-name> --marketplace ./my-marketplace
```

Marketplace sources can be:
*   a Claude known-marketplace name from `~/.claude/plugins/known_marketplaces.json`
*   a local marketplace root or `marketplace.json` path
*   a GitHub repo shorthand such as `owner/repo`
*   a git URL

For remote marketplaces loaded from GitHub or git, plugin entries must stay inside the cloned marketplace repo. OpenClaw accepts relative path sources from that repo and rejects external git, GitHub, URL/archive, and absolute-path plugin sources from remote manifests.For local paths and archives, OpenClaw auto-detects:
*   native OpenClaw plugins (`openclaw.plugin.json`)
*   Codex-compatible bundles (`.codex-plugin/plugin.json`)
*   Claude-compatible bundles (`.claude-plugin/plugin.json` or the default Claude component layout)
*   Cursor-compatible bundles (`.cursor-plugin/plugin.json`)

Compatible bundles install into the normal extensions root and participate in the same list/info/enable/disable flow. Today, bundle skills, Claude command-skills, Claude `settings.json` defaults, Cursor command-skills, and compatible Codex hook directories are supported; other detected bundle capabilities are shown in diagnostics/info but are not yet wired into runtime execution.Use `--link` to avoid copying a local directory (adds to `plugins.load.paths`):

```
openclaw plugins install -l ./my-plugin
```

Use `--pin` on npm installs to save the resolved exact spec (`name@version`) in `plugins.installs` while keeping the default behavior unpinned.
### [​](https://docs.openclaw.ai/cli/plugins#uninstall)

Uninstall

```
openclaw plugins uninstall <id>
openclaw plugins uninstall <id> --dry-run
openclaw plugins uninstall <id> --keep-files
```

`uninstall` removes plugin records from `plugins.entries`, `plugins.installs`, the plugin allowlist, and linked `plugins.load.paths` entries when applicable. For active memory plugins, the memory slot resets to `memory-core`.By default, uninstall also removes the plugin install directory under the active state dir extensions root (`$OPENCLAW_STATE_DIR/extensions/<id>`). Use `--keep-files` to keep files on disk.`--keep-config` is supported as a deprecated alias for `--keep-files`.
### [​](https://docs.openclaw.ai/cli/plugins#update)

Update

```
openclaw plugins update <id-or-npm-spec>
openclaw plugins update --all
openclaw plugins update <id-or-npm-spec> --dry-run
openclaw plugins update @openclaw/voice-call@beta
```

Updates apply to tracked installs in `plugins.installs` and tracked hook-pack installs in `hooks.internal.installs`.When you pass a plugin id, OpenClaw reuses the recorded install spec for that plugin. That means previously stored dist-tags such as `@beta` and exact pinned versions continue to be used on later `update <id>` runs.For npm installs, you can also pass an explicit npm package spec with a dist-tag or exact version. OpenClaw resolves that package name back to the tracked plugin record, updates that installed plugin, and records the new npm spec for future id-based updates.When a stored integrity hash exists and the fetched artifact hash changes, OpenClaw prints a warning and asks for confirmation before proceeding. Use global `--yes` to bypass prompts in CI/non-interactive runs.
### [​](https://docs.openclaw.ai/cli/plugins#inspect)

Inspect

```
openclaw plugins inspect <id>
openclaw plugins inspect <id> --json
```

Deep introspection for a single plugin. Shows identity, load status, source, registered capabilities, hooks, tools, commands, services, gateway methods, HTTP routes, policy flags, diagnostics, and install metadata.Each plugin is classified by what it actually registers at runtime:
*   **plain-capability** — one capability type (e.g. a provider-only plugin)
*   **hybrid-capability** — multiple capability types (e.g. text + speech + images)
*   **hook-only** — only hooks, no capabilities or surfaces
*   **non-capability** — tools/commands/services but no capabilities

See [Plugin shapes](https://docs.openclaw.ai/plugins/architecture#plugin-shapes) for more on the capability model.The `--json` flag outputs a machine-readable report suitable for scripting and auditing.`info` is an alias for `inspect`.

[webhooks](https://docs.openclaw.ai/cli/webhooks)[skills](https://docs.openclaw.ai/cli/skills)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
