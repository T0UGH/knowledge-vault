# cli/config

Source: https://docs.openclaw.ai/cli/config

Title: config - OpenClaw

URL Source: https://docs.openclaw.ai/cli/config

Markdown Content:
# config - OpenClaw

[Skip to main content](https://docs.openclaw.ai/cli/config#content-area)

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

Configuration

config

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### CLI commands

*   [CLI Reference](https://docs.openclaw.ai/cli)
*   Gateway and service 
*   Agents and sessions 
*   Channels and messaging 
*   Tools and execution 
*   Configuration 
    *   [config](https://docs.openclaw.ai/cli/config)
    *   [configure](https://docs.openclaw.ai/cli/configure)
    *   [webhooks](https://docs.openclaw.ai/cli/webhooks)

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

*   [openclaw config](https://docs.openclaw.ai/cli/config#openclaw-config)
*   [Examples](https://docs.openclaw.ai/cli/config#examples)
*   [config schema](https://docs.openclaw.ai/cli/config#config-schema)
*   [Paths](https://docs.openclaw.ai/cli/config#paths)
*   [Values](https://docs.openclaw.ai/cli/config#values)
*   [config set modes](https://docs.openclaw.ai/cli/config#config-set-modes)
*   [Provider Builder Flags](https://docs.openclaw.ai/cli/config#provider-builder-flags)
*   [Dry run](https://docs.openclaw.ai/cli/config#dry-run)
*   [JSON Output Shape](https://docs.openclaw.ai/cli/config#json-output-shape)
*   [Subcommands](https://docs.openclaw.ai/cli/config#subcommands)
*   [Validate](https://docs.openclaw.ai/cli/config#validate)

Configuration

# config

# [​](https://docs.openclaw.ai/cli/config#openclaw-config)

`openclaw config`

Config helpers for non-interactive edits in `openclaw.json`: get/set/unset/file/schema/validate values by path and print the active config file. Run without a subcommand to open the configure wizard (same as `openclaw configure`).
## [​](https://docs.openclaw.ai/cli/config#examples)

Examples

```
openclaw config file
openclaw config schema
openclaw config get browser.executablePath
openclaw config set browser.executablePath "/usr/bin/google-chrome"
openclaw config set agents.defaults.heartbeat.every "2h"
openclaw config set agents.list[0].tools.exec.node "node-id-or-name"
openclaw config set channels.discord.token --ref-provider default --ref-source env --ref-id DISCORD_BOT_TOKEN
openclaw config set secrets.providers.vaultfile --provider-source file --provider-path /etc/openclaw/secrets.json --provider-mode json
openclaw config unset plugins.entries.brave.config.webSearch.apiKey
openclaw config set channels.discord.token --ref-provider default --ref-source env --ref-id DISCORD_BOT_TOKEN --dry-run
openclaw config validate
openclaw config validate --json
```

### [​](https://docs.openclaw.ai/cli/config#config-schema)

`config schema`

Print the generated JSON schema for `openclaw.json` to stdout as plain text.

```
openclaw config schema
```

Pipe it into a file when you want to inspect or validate it with other tools:

```
openclaw config schema > openclaw.schema.json
```

### [​](https://docs.openclaw.ai/cli/config#paths)

Paths

Paths use dot or bracket notation:

```
openclaw config get agents.defaults.workspace
openclaw config get agents.list[0].id
```

Use the agent list index to target a specific agent:

```
openclaw config get agents.list
openclaw config set agents.list[1].tools.exec.node "node-id-or-name"
```

## [​](https://docs.openclaw.ai/cli/config#values)

Values

Values are parsed as JSON5 when possible; otherwise they are treated as strings. Use `--strict-json` to require JSON5 parsing. `--json` remains supported as a legacy alias.

```
openclaw config set agents.defaults.heartbeat.every "0m"
openclaw config set gateway.port 19001 --strict-json
openclaw config set channels.whatsapp.groups '["*"]' --strict-json
```

## [​](https://docs.openclaw.ai/cli/config#config-set-modes)

`config set` modes

`openclaw config set` supports four assignment styles:
1.   Value mode: `openclaw config set <path> <value>`
2.   SecretRef builder mode:

```
openclaw config set channels.discord.token \
  --ref-provider default \
  --ref-source env \
  --ref-id DISCORD_BOT_TOKEN
```

1.   Provider builder mode (`secrets.providers.<alias>` path only):

```
openclaw config set secrets.providers.vault \
  --provider-source exec \
  --provider-command /usr/local/bin/openclaw-vault \
  --provider-arg read \
  --provider-arg openai/api-key \
  --provider-timeout-ms 5000
```

1.   Batch mode (`--batch-json` or `--batch-file`):

```
openclaw config set --batch-json '[
  {
    "path": "secrets.providers.default",
    "provider": { "source": "env" }
  },
  {
    "path": "channels.discord.token",
    "ref": { "source": "env", "provider": "default", "id": "DISCORD_BOT_TOKEN" }
  }
]'
```

```
openclaw config set --batch-file ./config-set.batch.json --dry-run
```

Batch parsing always uses the batch payload (`--batch-json`/`--batch-file`) as the source of truth. `--strict-json` / `--json` do not change batch parsing behavior.JSON path/value mode remains supported for both SecretRefs and providers:

```
openclaw config set channels.discord.token \
  '{"source":"env","provider":"default","id":"DISCORD_BOT_TOKEN"}' \
  --strict-json

openclaw config set secrets.providers.vaultfile \
  '{"source":"file","path":"/etc/openclaw/secrets.json","mode":"json"}' \
  --strict-json
```

## [​](https://docs.openclaw.ai/cli/config#provider-builder-flags)

Provider Builder Flags

Provider builder targets must use `secrets.providers.<alias>` as the path.Common flags:
*   `--provider-source <env|file|exec>`
*   `--provider-timeout-ms <ms>` (`file`, `exec`)

Env provider (`--provider-source env`):
*   `--provider-allowlist <ENV_VAR>` (repeatable)

File provider (`--provider-source file`):
*   `--provider-path <path>` (required)
*   `--provider-mode <singleValue|json>`
*   `--provider-max-bytes <bytes>`

Exec provider (`--provider-source exec`):
*   `--provider-command <path>` (required)
*   `--provider-arg <arg>` (repeatable)
*   `--provider-no-output-timeout-ms <ms>`
*   `--provider-max-output-bytes <bytes>`
*   `--provider-json-only`
*   `--provider-env <KEY=VALUE>` (repeatable)
*   `--provider-pass-env <ENV_VAR>` (repeatable)
*   `--provider-trusted-dir <path>` (repeatable)
*   `--provider-allow-insecure-path`
*   `--provider-allow-symlink-command`

Hardened exec provider example:

```
openclaw config set secrets.providers.vault \
  --provider-source exec \
  --provider-command /usr/local/bin/openclaw-vault \
  --provider-arg read \
  --provider-arg openai/api-key \
  --provider-json-only \
  --provider-pass-env VAULT_TOKEN \
  --provider-trusted-dir /usr/local/bin \
  --provider-timeout-ms 5000
```

## [​](https://docs.openclaw.ai/cli/config#dry-run)

Dry run

Use `--dry-run` to validate changes without writing `openclaw.json`.

```
openclaw config set channels.discord.token \
  --ref-provider default \
  --ref-source env \
  --ref-id DISCORD_BOT_TOKEN \
  --dry-run

openclaw config set channels.discord.token \
  --ref-provider default \
  --ref-source env \
  --ref-id DISCORD_BOT_TOKEN \
  --dry-run \
  --json

openclaw config set channels.discord.token \
  --ref-provider vault \
  --ref-source exec \
  --ref-id discord/token \
  --dry-run \
  --allow-exec
```

Dry-run behavior:
*   Builder mode: runs SecretRef resolvability checks for changed refs/providers.
*   JSON mode (`--strict-json`, `--json`, or batch mode): runs schema validation plus SecretRef resolvability checks.
*   Exec SecretRef checks are skipped by default during dry-run to avoid command side effects.
*   Use `--allow-exec` with `--dry-run` to opt in to exec SecretRef checks (this may execute provider commands).
*   `--allow-exec` is dry-run only and errors if used without `--dry-run`.

`--dry-run --json` prints a machine-readable report:
*   `ok`: whether dry-run passed
*   `operations`: number of assignments evaluated
*   `checks`: whether schema/resolvability checks ran
*   `checks.resolvabilityComplete`: whether resolvability checks ran to completion (false when exec refs are skipped)
*   `refsChecked`: number of refs actually resolved during dry-run
*   `skippedExecRefs`: number of exec refs skipped because `--allow-exec` was not set
*   `errors`: structured schema/resolvability failures when `ok=false`

### [​](https://docs.openclaw.ai/cli/config#json-output-shape)

JSON Output Shape

```
{
  ok: boolean,
  operations: number,
  configPath: string,
  inputModes: ["value" | "json" | "builder", ...],
  checks: {
    schema: boolean,
    resolvability: boolean,
    resolvabilityComplete: boolean,
  },
  refsChecked: number,
  skippedExecRefs: number,
  errors?: [
    {
      kind: "schema" | "resolvability",
      message: string,
      ref?: string, // present for resolvability errors
    },
  ],
}
```

Success example:

```
{
  "ok": true,
  "operations": 1,
  "configPath": "~/.openclaw/openclaw.json",
  "inputModes": ["builder"],
  "checks": {
    "schema": false,
    "resolvability": true,
    "resolvabilityComplete": true
  },
  "refsChecked": 1,
  "skippedExecRefs": 0
}
```

Failure example:

```
{
  "ok": false,
  "operations": 1,
  "configPath": "~/.openclaw/openclaw.json",
  "inputModes": ["builder"],
  "checks": {
    "schema": false,
    "resolvability": true,
    "resolvabilityComplete": true
  },
  "refsChecked": 1,
  "skippedExecRefs": 0,
  "errors": [
    {
      "kind": "resolvability",
      "message": "Error: Environment variable \"MISSING_TEST_SECRET\" is not set.",
      "ref": "env:default:MISSING_TEST_SECRET"
    }
  ]
}
```

If dry-run fails:
*   `config schema validation failed`: your post-change config shape is invalid; fix path/value or provider/ref object shape.
*   `SecretRef assignment(s) could not be resolved`: referenced provider/ref currently cannot resolve (missing env var, invalid file pointer, exec provider failure, or provider/source mismatch).
*   `Dry run note: skipped <n> exec SecretRef resolvability check(s)`: dry-run skipped exec refs; rerun with `--allow-exec` if you need exec resolvability validation.
*   For batch mode, fix failing entries and rerun `--dry-run` before writing.

## [​](https://docs.openclaw.ai/cli/config#subcommands)

Subcommands

*   `config file`: Print the active config file path (resolved from `OPENCLAW_CONFIG_PATH` or default location).

Restart the gateway after edits.
## [​](https://docs.openclaw.ai/cli/config#validate)

Validate

Validate the current config against the active schema without starting the gateway.

```
openclaw config validate
openclaw config validate --json
```

[Sandbox CLI](https://docs.openclaw.ai/cli/sandbox)[configure](https://docs.openclaw.ai/cli/configure)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
