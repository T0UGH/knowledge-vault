# tools/exec-approvals

Source: https://docs.openclaw.ai/tools/exec-approvals

Title: Exec Approvals - OpenClaw

URL Source: https://docs.openclaw.ai/tools/exec-approvals

Markdown Content:
# Exec Approvals - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/exec-approvals#content-area)

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

Tools

Exec Approvals

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Tools and Plugins](https://docs.openclaw.ai/tools)

##### Plugins

*   [Install and Configure](https://docs.openclaw.ai/tools/plugin)
*   [Community Plugins](https://docs.openclaw.ai/plugins/community)
*   [Plugin Bundles](https://docs.openclaw.ai/plugins/bundles)
*   Building Plugins 
*   SDK Reference 

##### Skills

*   [Skills](https://docs.openclaw.ai/tools/skills)
*   [Creating Skills](https://docs.openclaw.ai/tools/creating-skills)
*   [Skills Config](https://docs.openclaw.ai/tools/skills-config)
*   [Slash Commands](https://docs.openclaw.ai/tools/slash-commands)
*   [ClawHub](https://docs.openclaw.ai/tools/clawhub)
*   [OpenProse](https://docs.openclaw.ai/prose)

##### Automation

*   [Hooks](https://docs.openclaw.ai/automation/hooks)
*   [Standing Orders](https://docs.openclaw.ai/automation/standing-orders)
*   [Cron Jobs](https://docs.openclaw.ai/automation/cron-jobs)
*   [Cron vs Heartbeat](https://docs.openclaw.ai/automation/cron-vs-heartbeat)
*   [Automation Troubleshooting](https://docs.openclaw.ai/automation/troubleshooting)
*   [Webhooks](https://docs.openclaw.ai/automation/webhook)
*   [Gmail PubSub](https://docs.openclaw.ai/automation/gmail-pubsub)
*   [Polls](https://docs.openclaw.ai/automation/poll)
*   [Auth Monitoring](https://docs.openclaw.ai/automation/auth-monitoring)

##### Tools

*   [apply_patch Tool](https://docs.openclaw.ai/tools/apply-patch)
*   Web Browser 
*   Web Tools 
*   [BTW Side Questions](https://docs.openclaw.ai/tools/btw)
*   [Diffs](https://docs.openclaw.ai/tools/diffs)
*   [Elevated Mode](https://docs.openclaw.ai/tools/elevated)
*   [Exec Tool](https://docs.openclaw.ai/tools/exec)
*   [Exec Approvals](https://docs.openclaw.ai/tools/exec-approvals)
*   [LLM Task](https://docs.openclaw.ai/tools/llm-task)
*   [Lobster](https://docs.openclaw.ai/tools/lobster)
*   [Tool-loop detection](https://docs.openclaw.ai/tools/loop-detection)
*   [PDF Tool](https://docs.openclaw.ai/tools/pdf)
*   [Reactions](https://docs.openclaw.ai/tools/reactions)
*   [Thinking Levels](https://docs.openclaw.ai/tools/thinking)

##### Agent coordination

*   [Agent Send](https://docs.openclaw.ai/tools/agent-send)
*   [Sub-Agents](https://docs.openclaw.ai/tools/subagents)
*   [ACP Agents](https://docs.openclaw.ai/tools/acp-agents)
*   [Multi-Agent Sandbox & Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools)

On this page

*   [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals#exec-approvals)
*   [Where it applies](https://docs.openclaw.ai/tools/exec-approvals#where-it-applies)
*   [Settings and storage](https://docs.openclaw.ai/tools/exec-approvals#settings-and-storage)
*   [Policy knobs](https://docs.openclaw.ai/tools/exec-approvals#policy-knobs)
*   [Security (exec.security)](https://docs.openclaw.ai/tools/exec-approvals#security-exec-security)
*   [Ask (exec.ask)](https://docs.openclaw.ai/tools/exec-approvals#ask-exec-ask)
*   [Ask fallback (askFallback)](https://docs.openclaw.ai/tools/exec-approvals#ask-fallback-askfallback)
*   [Inline interpreter eval hardening (tools.exec.strictInlineEval)](https://docs.openclaw.ai/tools/exec-approvals#inline-interpreter-eval-hardening-tools-exec-strictinlineeval)
*   [Allowlist (per agent)](https://docs.openclaw.ai/tools/exec-approvals#allowlist-per-agent)
*   [Auto-allow skill CLIs](https://docs.openclaw.ai/tools/exec-approvals#auto-allow-skill-clis)
*   [Safe bins (stdin-only)](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-stdin-only)
*   [Safe bins versus allowlist](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-versus-allowlist)
*   [Control UI editing](https://docs.openclaw.ai/tools/exec-approvals#control-ui-editing)
*   [Approval flow](https://docs.openclaw.ai/tools/exec-approvals#approval-flow)
*   [Interpreter/runtime commands](https://docs.openclaw.ai/tools/exec-approvals#interpreter%2Fruntime-commands)
*   [Approval forwarding to chat channels](https://docs.openclaw.ai/tools/exec-approvals#approval-forwarding-to-chat-channels)
*   [Plugin approval forwarding](https://docs.openclaw.ai/tools/exec-approvals#plugin-approval-forwarding)
*   [Built-in chat approval clients](https://docs.openclaw.ai/tools/exec-approvals#built-in-chat-approval-clients)
*   [macOS IPC flow](https://docs.openclaw.ai/tools/exec-approvals#macos-ipc-flow)
*   [System events](https://docs.openclaw.ai/tools/exec-approvals#system-events)
*   [Implications](https://docs.openclaw.ai/tools/exec-approvals#implications)

Tools

# Exec Approvals

# [​](https://docs.openclaw.ai/tools/exec-approvals#exec-approvals)

Exec approvals

Exec approvals are the **companion app / node host guardrail** for letting a sandboxed agent run commands on a real host (`gateway` or `node`). Think of it like a safety interlock: commands are allowed only when policy + allowlist + (optional) user approval all agree. Exec approvals are **in addition** to tool policy and elevated gating (unless elevated is set to `full`, which skips approvals). Effective policy is the **stricter** of `tools.exec.*` and approvals defaults; if an approvals field is omitted, the `tools.exec` value is used.If the companion app UI is **not available**, any request that requires a prompt is resolved by the **ask fallback** (default: deny).
## [​](https://docs.openclaw.ai/tools/exec-approvals#where-it-applies)

Where it applies

Exec approvals are enforced locally on the execution host:
*   **gateway host** → `openclaw` process on the gateway machine
*   **node host** → node runner (macOS companion app or headless node host)

Trust model note:
*   Gateway-authenticated callers are trusted operators for that Gateway.
*   Paired nodes extend that trusted operator capability onto the node host.
*   Exec approvals reduce accidental execution risk, but are not a per-user auth boundary.
*   Approved node-host runs bind canonical execution context: canonical cwd, exact argv, env binding when present, and pinned executable path when applicable.
*   For shell scripts and direct interpreter/runtime file invocations, OpenClaw also tries to bind one concrete local file operand. If that bound file changes after approval but before execution, the run is denied instead of executing drifted content.
*   This file binding is intentionally best-effort, not a complete semantic model of every interpreter/runtime loader path. If approval mode cannot identify exactly one concrete local file to bind, it refuses to mint an approval-backed run instead of pretending full coverage.

macOS split:
*   **node host service** forwards `system.run` to the **macOS app** over local IPC.
*   **macOS app** enforces approvals + executes the command in UI context.

## [​](https://docs.openclaw.ai/tools/exec-approvals#settings-and-storage)

Settings and storage

Approvals live in a local JSON file on the execution host:`~/.openclaw/exec-approvals.json`Example schema:

```
{
  "version": 1,
  "socket": {
    "path": "~/.openclaw/exec-approvals.sock",
    "token": "base64url-token"
  },
  "defaults": {
    "security": "deny",
    "ask": "on-miss",
    "askFallback": "deny",
    "autoAllowSkills": false
  },
  "agents": {
    "main": {
      "security": "allowlist",
      "ask": "on-miss",
      "askFallback": "deny",
      "autoAllowSkills": true,
      "allowlist": [
        {
          "id": "B0C8C0B3-2C2D-4F8A-9A3C-5A4B3C2D1E0F",
          "pattern": "~/Projects/**/bin/rg",
          "lastUsedAt": 1737150000000,
          "lastUsedCommand": "rg -n TODO",
          "lastResolvedPath": "/Users/user/Projects/.../bin/rg"
        }
      ]
    }
  }
}
```

## [​](https://docs.openclaw.ai/tools/exec-approvals#policy-knobs)

Policy knobs

### [​](https://docs.openclaw.ai/tools/exec-approvals#security-exec-security)

Security (`exec.security`)

*   **deny**: block all host exec requests.
*   **allowlist**: allow only allowlisted commands.
*   **full**: allow everything (equivalent to elevated).

### [​](https://docs.openclaw.ai/tools/exec-approvals#ask-exec-ask)

Ask (`exec.ask`)

*   **off**: never prompt.
*   **on-miss**: prompt only when allowlist does not match.
*   **always**: prompt on every command.

### [​](https://docs.openclaw.ai/tools/exec-approvals#ask-fallback-askfallback)

Ask fallback (`askFallback`)

If a prompt is required but no UI is reachable, fallback decides:
*   **deny**: block.
*   **allowlist**: allow only if allowlist matches.
*   **full**: allow.

### [​](https://docs.openclaw.ai/tools/exec-approvals#inline-interpreter-eval-hardening-tools-exec-strictinlineeval)

Inline interpreter eval hardening (`tools.exec.strictInlineEval`)

When `tools.exec.strictInlineEval=true`, OpenClaw treats inline code-eval forms as approval-only even if the interpreter binary itself is allowlisted.Examples:
*   `python -c`
*   `node -e`, `node --eval`, `node -p`
*   `ruby -e`
*   `perl -e`, `perl -E`
*   `php -r`
*   `lua -e`
*   `osascript -e`

This is defense-in-depth for interpreter loaders that do not map cleanly to one stable file operand. In strict mode:
*   these commands still need explicit approval;
*   `allow-always` does not persist new allowlist entries for them automatically.

## [​](https://docs.openclaw.ai/tools/exec-approvals#allowlist-per-agent)

Allowlist (per agent)

Allowlists are **per agent**. If multiple agents exist, switch which agent you’re editing in the macOS app. Patterns are **case-insensitive glob matches**. Patterns should resolve to **binary paths** (basename-only entries are ignored). Legacy `agents.default` entries are migrated to `agents.main` on load.Examples:
*   `~/Projects/**/bin/peekaboo`
*   `~/.local/bin/*`
*   `/opt/homebrew/bin/rg`

Each allowlist entry tracks:
*   **id** stable UUID used for UI identity (optional)
*   **last used** timestamp
*   **last used command**
*   **last resolved path**

## [​](https://docs.openclaw.ai/tools/exec-approvals#auto-allow-skill-clis)

Auto-allow skill CLIs

When **Auto-allow skill CLIs** is enabled, executables referenced by known skills are treated as allowlisted on nodes (macOS node or headless node host). This uses `skills.bins` over the Gateway RPC to fetch the skill bin list. Disable this if you want strict manual allowlists.Important trust notes:
*   This is an **implicit convenience allowlist**, separate from manual path allowlist entries.
*   It is intended for trusted operator environments where Gateway and node are in the same trust boundary.
*   If you require strict explicit trust, keep `autoAllowSkills: false` and use manual path allowlist entries only.

## [​](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-stdin-only)

Safe bins (stdin-only)

`tools.exec.safeBins` defines a small list of **stdin-only** binaries (for example `cut`) that can run in allowlist mode **without** explicit allowlist entries. Safe bins reject positional file args and path-like tokens, so they can only operate on the incoming stream. Treat this as a narrow fast-path for stream filters, not a general trust list. Do **not** add interpreter or runtime binaries (for example `python3`, `node`, `ruby`, `bash`, `sh`, `zsh`) to `safeBins`. If a command can evaluate code, execute subcommands, or read files by design, prefer explicit allowlist entries and keep approval prompts enabled. Custom safe bins must define an explicit profile in `tools.exec.safeBinProfiles.<bin>`. Validation is deterministic from argv shape only (no host filesystem existence checks), which prevents file-existence oracle behavior from allow/deny differences. File-oriented options are denied for default safe bins (for example `sort -o`, `sort --output`, `sort --files0-from`, `sort --compress-program`, `sort --random-source`, `sort --temporary-directory`/`-T`, `wc --files0-from`, `jq -f/--from-file`, `grep -f/--file`). Safe bins also enforce explicit per-binary flag policy for options that break stdin-only behavior (for example `sort -o/--output/--compress-program` and grep recursive flags). Long options are validated fail-closed in safe-bin mode: unknown flags and ambiguous abbreviations are rejected. Denied flags by safe-bin profile:
*   `grep`: `--dereference-recursive`, `--directories`, `--exclude-from`, `--file`, `--recursive`, `-R`, `-d`, `-f`, `-r`
*   `jq`: `--argfile`, `--from-file`, `--library-path`, `--rawfile`, `--slurpfile`, `-L`, `-f`
*   `sort`: `--compress-program`, `--files0-from`, `--output`, `--random-source`, `--temporary-directory`, `-T`, `-o`
*   `wc`: `--files0-from`

Safe bins also force argv tokens to be treated as **literal text** at execution time (no globbing and no `$VARS` expansion) for stdin-only segments, so patterns like `*` or `$HOME/...` cannot be used to smuggle file reads. Safe bins must also resolve from trusted binary directories (system defaults plus optional `tools.exec.safeBinTrustedDirs`). `PATH` entries are never auto-trusted. Default trusted safe-bin directories are intentionally minimal: `/bin`, `/usr/bin`. If your safe-bin executable lives in package-manager/user paths (for example `/opt/homebrew/bin`, `/usr/local/bin`, `/opt/local/bin`, `/snap/bin`), add them explicitly to `tools.exec.safeBinTrustedDirs`. Shell chaining and redirections are not auto-allowed in allowlist mode.Shell chaining (`&&`, `||`, `;`) is allowed when every top-level segment satisfies the allowlist (including safe bins or skill auto-allow). Redirections remain unsupported in allowlist mode. Command substitution (`$()` / backticks) is rejected during allowlist parsing, including inside double quotes; use single quotes if you need literal `$()` text. On macOS companion-app approvals, raw shell text containing shell control or expansion syntax (`&&`, `||`, `;`, `|`, ```, `$`, `<`, `>`, `(`, `)`) is treated as an allowlist miss unless the shell binary itself is allowlisted. For shell wrappers (`bash|sh|zsh ... -c/-lc`), request-scoped env overrides are reduced to a small explicit allowlist (`TERM`, `LANG`, `LC_*`, `COLORTERM`, `NO_COLOR`, `FORCE_COLOR`). For allow-always decisions in allowlist mode, known dispatch wrappers (`env`, `nice`, `nohup`, `stdbuf`, `timeout`) persist inner executable paths instead of wrapper paths. Shell multiplexers (`busybox`, `toybox`) are also unwrapped for shell applets (`sh`, `ash`, etc.) so inner executables are persisted instead of multiplexer binaries. If a wrapper or multiplexer cannot be safely unwrapped, no allowlist entry is persisted automatically. If you allowlist interpreters like `python3` or `node`, prefer `tools.exec.strictInlineEval=true` so inline eval still requires an explicit approval.Default safe bins:`cut`, `uniq`, `head`, `tail`, `tr`, `wc``grep` and `sort` are not in the default list. If you opt in, keep explicit allowlist entries for their non-stdin workflows. For `grep` in safe-bin mode, provide the pattern with `-e`/`--regexp`; positional pattern form is rejected so file operands cannot be smuggled as ambiguous positionals.
### [​](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-versus-allowlist)

Safe bins versus allowlist

| Topic | `tools.exec.safeBins` | Allowlist (`exec-approvals.json`) |
| --- | --- | --- |
| Goal | Auto-allow narrow stdin filters | Explicitly trust specific executables |
| Match type | Executable name + safe-bin argv policy | Resolved executable path glob pattern |
| Argument scope | Restricted by safe-bin profile and literal-token rules | Path match only; arguments are otherwise your responsibility |
| Typical examples | `head`, `tail`, `tr`, `wc` | `jq`, `python3`, `node`, `ffmpeg`, custom CLIs |
| Best use | Low-risk text transforms in pipelines | Any tool with broader behavior or side effects |

Configuration location:
*   `safeBins` comes from config (`tools.exec.safeBins` or per-agent `agents.list[].tools.exec.safeBins`).
*   `safeBinTrustedDirs` comes from config (`tools.exec.safeBinTrustedDirs` or per-agent `agents.list[].tools.exec.safeBinTrustedDirs`).
*   `safeBinProfiles` comes from config (`tools.exec.safeBinProfiles` or per-agent `agents.list[].tools.exec.safeBinProfiles`). Per-agent profile keys override global keys.
*   allowlist entries live in host-local `~/.openclaw/exec-approvals.json` under `agents.<id>.allowlist` (or via Control UI / `openclaw approvals allowlist ...`).
*   `openclaw security audit` warns with `tools.exec.safe_bins_interpreter_unprofiled` when interpreter/runtime bins appear in `safeBins` without explicit profiles.
*   `openclaw doctor --fix` can scaffold missing custom `safeBinProfiles.<bin>` entries as `{}` (review and tighten afterward). Interpreter/runtime bins are not auto-scaffolded.

Custom profile example:

```
{
  tools: {
    exec: {
      safeBins: ["jq", "myfilter"],
      safeBinProfiles: {
        myfilter: {
          minPositional: 0,
          maxPositional: 0,
          allowedValueFlags: ["-n", "--limit"],
          deniedFlags: ["-f", "--file", "-c", "--command"],
        },
      },
    },
  },
}
```

If you explicitly opt `jq` into `safeBins`, OpenClaw still rejects the `env` builtin in safe-bin mode so `jq -n env` cannot dump the host process environment without an explicit allowlist path or approval prompt.
## [​](https://docs.openclaw.ai/tools/exec-approvals#control-ui-editing)

Control UI editing

Use the **Control UI → Nodes → Exec approvals** card to edit defaults, per‑agent overrides, and allowlists. Pick a scope (Defaults or an agent), tweak the policy, add/remove allowlist patterns, then **Save**. The UI shows **last used** metadata per pattern so you can keep the list tidy.The target selector chooses **Gateway** (local approvals) or a **Node**. Nodes must advertise `system.execApprovals.get/set` (macOS app or headless node host). If a node does not advertise exec approvals yet, edit its local `~/.openclaw/exec-approvals.json` directly.CLI: `openclaw approvals` supports gateway or node editing (see [Approvals CLI](https://docs.openclaw.ai/cli/approvals)).
## [​](https://docs.openclaw.ai/tools/exec-approvals#approval-flow)

Approval flow

When a prompt is required, the gateway broadcasts `exec.approval.requested` to operator clients. The Control UI and macOS app resolve it via `exec.approval.resolve`, then the gateway forwards the approved request to the node host.For `host=node`, approval requests include a canonical `systemRunPlan` payload. The gateway uses that plan as the authoritative command/cwd/session context when forwarding approved `system.run` requests.
## [​](https://docs.openclaw.ai/tools/exec-approvals#interpreter/runtime-commands)

Interpreter/runtime commands

Approval-backed interpreter/runtime runs are intentionally conservative:
*   Exact argv/cwd/env context is always bound.
*   Direct shell script and direct runtime file forms are best-effort bound to one concrete local file snapshot.
*   Common package-manager wrapper forms that still resolve to one direct local file (for example `pnpm exec`, `pnpm node`, `npm exec`, `npx`) are unwrapped before binding.
*   If OpenClaw cannot identify exactly one concrete local file for an interpreter/runtime command (for example package scripts, eval forms, runtime-specific loader chains, or ambiguous multi-file forms), approval-backed execution is denied instead of claiming semantic coverage it does not have.
*   For those workflows, prefer sandboxing, a separate host boundary, or an explicit trusted allowlist/full workflow where the operator accepts the broader runtime semantics.

When approvals are required, the exec tool returns immediately with an approval id. Use that id to correlate later system events (`Exec finished` / `Exec denied`). If no decision arrives before the timeout, the request is treated as an approval timeout and surfaced as a denial reason.The confirmation dialog includes:
*   command + args
*   cwd
*   agent id
*   resolved executable path
*   host + policy metadata

Actions:
*   **Allow once** → run now
*   **Always allow** → add to allowlist + run
*   **Deny** → block

## [​](https://docs.openclaw.ai/tools/exec-approvals#approval-forwarding-to-chat-channels)

Approval forwarding to chat channels

You can forward exec approval prompts to any chat channel (including plugin channels) and approve them with `/approve`. This uses the normal outbound delivery pipeline.Config:

```
{
  approvals: {
    exec: {
      enabled: true,
      mode: "session", // "session" | "targets" | "both"
      agentFilter: ["main"],
      sessionFilter: ["discord"], // substring or regex
      targets: [
        { channel: "slack", to: "U12345678" },
        { channel: "telegram", to: "123456789" },
      ],
    },
  },
}
```

Reply in chat:

```
/approve <id> allow-once
/approve <id> allow-always
/approve <id> deny
```

The `/approve` command handles both exec approvals and plugin approvals. If the ID does not match a pending exec approval, it automatically checks plugin approvals.
### [​](https://docs.openclaw.ai/tools/exec-approvals#plugin-approval-forwarding)

Plugin approval forwarding

Plugin approval forwarding uses the same delivery pipeline as exec approvals but has its own independent config under `approvals.plugin`. Enabling or disabling one does not affect the other.

```
{
  approvals: {
    plugin: {
      enabled: true,
      mode: "targets",
      agentFilter: ["main"],
      targets: [
        { channel: "slack", to: "U12345678" },
        { channel: "telegram", to: "123456789" },
      ],
    },
  },
}
```

The config shape is identical to `approvals.exec`: `enabled`, `mode`, `agentFilter`, `sessionFilter`, and `targets` work the same way.Channels that support interactive exec approval buttons (such as Telegram) also render buttons for plugin approvals. Channels without adapter support fall back to plain text with `/approve` instructions.
### [​](https://docs.openclaw.ai/tools/exec-approvals#built-in-chat-approval-clients)

Built-in chat approval clients

Discord and Telegram can also act as explicit exec approval clients with channel-specific config.
*   Discord: `channels.discord.execApprovals.*`
*   Telegram: `channels.telegram.execApprovals.*`

These clients are opt-in. If a channel does not have exec approvals enabled, OpenClaw does not treat that channel as an approval surface just because the conversation happened there.Shared behavior:
*   only configured approvers can approve or deny
*   the requester does not need to be an approver
*   when channel delivery is enabled, approval prompts include the command text
*   if no operator UI or configured approval client can accept the request, the prompt falls back to `askFallback`

Telegram defaults to approver DMs (`target: "dm"`). You can switch to `channel` or `both` when you want approval prompts to appear in the originating Telegram chat/topic as well. For Telegram forum topics, OpenClaw preserves the topic for the approval prompt and the post-approval follow-up.See:
*   [Discord](https://docs.openclaw.ai/channels/discord)
*   [Telegram](https://docs.openclaw.ai/channels/telegram)

### [​](https://docs.openclaw.ai/tools/exec-approvals#macos-ipc-flow)

macOS IPC flow

```
Gateway -> Node Service (WS)
                 |  IPC (UDS + token + HMAC + TTL)
                 v
             Mac App (UI + approvals + system.run)
```

Security notes:
*   Unix socket mode `0600`, token stored in `exec-approvals.json`.
*   Same-UID peer check.
*   Challenge/response (nonce + HMAC token + request hash) + short TTL.

## [​](https://docs.openclaw.ai/tools/exec-approvals#system-events)

System events

Exec lifecycle is surfaced as system messages:
*   `Exec running` (only if the command exceeds the running notice threshold)
*   `Exec finished`
*   `Exec denied`

These are posted to the agent’s session after the node reports the event. Gateway-host exec approvals emit the same lifecycle events when the command finishes (and optionally when running longer than the threshold). Approval-gated execs reuse the approval id as the `runId` in these messages for easy correlation.
## [​](https://docs.openclaw.ai/tools/exec-approvals#implications)

Implications

*   **full** is powerful; prefer allowlists when possible.
*   **ask** keeps you in the loop while still allowing fast approvals.
*   Per-agent allowlists prevent one agent’s approvals from leaking into others.
*   Approvals only apply to host exec requests from **authorized senders**. Unauthorized senders cannot issue `/exec`.
*   `/exec security=full` is a session-level convenience for authorized operators and skips approvals by design. To hard-block host exec, set approvals security to `deny` or deny the `exec` tool via tool policy.

Related:
*   [Exec tool](https://docs.openclaw.ai/tools/exec)
*   [Elevated mode](https://docs.openclaw.ai/tools/elevated)
*   [Skills](https://docs.openclaw.ai/tools/skills)

[Exec Tool](https://docs.openclaw.ai/tools/exec)[LLM Task](https://docs.openclaw.ai/tools/llm-task)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
