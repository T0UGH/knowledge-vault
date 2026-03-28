# tools/exec

Source: https://docs.openclaw.ai/tools/exec

Title: Exec Tool - OpenClaw

URL Source: https://docs.openclaw.ai/tools/exec

Markdown Content:
# Exec Tool - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/exec#content-area)

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

Exec Tool

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

*   [Exec tool](https://docs.openclaw.ai/tools/exec#exec-tool)
*   [Parameters](https://docs.openclaw.ai/tools/exec#parameters)
*   [Config](https://docs.openclaw.ai/tools/exec#config)
*   [PATH handling](https://docs.openclaw.ai/tools/exec#path-handling)
*   [Session overrides (/exec)](https://docs.openclaw.ai/tools/exec#session-overrides-%2Fexec)
*   [Authorization model](https://docs.openclaw.ai/tools/exec#authorization-model)
*   [Exec approvals (companion app / node host)](https://docs.openclaw.ai/tools/exec#exec-approvals-companion-app-%2F-node-host)
*   [Allowlist + safe bins](https://docs.openclaw.ai/tools/exec#allowlist-%2B-safe-bins)
*   [Examples](https://docs.openclaw.ai/tools/exec#examples)
*   [apply_patch](https://docs.openclaw.ai/tools/exec#apply_patch)

Tools

# Exec Tool

# [​](https://docs.openclaw.ai/tools/exec#exec-tool)

Exec tool

Run shell commands in the workspace. Supports foreground + background execution via `process`. If `process` is disallowed, `exec` runs synchronously and ignores `yieldMs`/`background`. Background sessions are scoped per agent; `process` only sees sessions from the same agent.
## [​](https://docs.openclaw.ai/tools/exec#parameters)

Parameters

*   `command` (required)
*   `workdir` (defaults to cwd)
*   `env` (key/value overrides)
*   `yieldMs` (default 10000): auto-background after delay
*   `background` (bool): background immediately
*   `timeout` (seconds, default 1800): kill on expiry
*   `pty` (bool): run in a pseudo-terminal when available (TTY-only CLIs, coding agents, terminal UIs)
*   `host` (`sandbox | gateway | node`): where to execute
*   `security` (`deny | allowlist | full`): enforcement mode for `gateway`/`node`
*   `ask` (`off | on-miss | always`): approval prompts for `gateway`/`node`
*   `node` (string): node id/name for `host=node`
*   `elevated` (bool): request elevated mode (gateway host); `security=full` is only forced when elevated resolves to `full`

Notes:
*   `host` defaults to `sandbox`.
*   `elevated` is ignored when sandboxing is off (exec already runs on the host).
*   `gateway`/`node` approvals are controlled by `~/.openclaw/exec-approvals.json`.
*   `node` requires a paired node (companion app or headless node host).
*   If multiple nodes are available, set `exec.node` or `tools.exec.node` to select one.
*   On non-Windows hosts, exec uses `SHELL` when set; if `SHELL` is `fish`, it prefers `bash` (or `sh`) from `PATH` to avoid fish-incompatible scripts, then falls back to `SHELL` if neither exists.
*   On Windows hosts, exec prefers PowerShell 7 (`pwsh`) discovery (Program Files, ProgramW6432, then PATH), then falls back to Windows PowerShell 5.1.
*   Host execution (`gateway`/`node`) rejects `env.PATH` and loader overrides (`LD_*`/`DYLD_*`) to prevent binary hijacking or injected code.
*   OpenClaw sets `OPENCLAW_SHELL=exec` in the spawned command environment (including PTY and sandbox execution) so shell/profile rules can detect exec-tool context.
*   Important: sandboxing is **off by default**. If sandboxing is off and `host=sandbox` is explicitly configured/requested, exec now fails closed instead of silently running on the gateway host. Enable sandboxing or use `host=gateway` with approvals.
*   Script preflight checks (for common Python/Node shell-syntax mistakes) only inspect files inside the effective `workdir` boundary. If a script path resolves outside `workdir`, preflight is skipped for that file.

## [​](https://docs.openclaw.ai/tools/exec#config)

Config

*   `tools.exec.notifyOnExit` (default: true): when true, backgrounded exec sessions enqueue a system event and request a heartbeat on exit.
*   `tools.exec.approvalRunningNoticeMs` (default: 10000): emit a single “running” notice when an approval-gated exec runs longer than this (0 disables).
*   `tools.exec.host` (default: `sandbox`)
*   `tools.exec.security` (default: `deny` for sandbox, `allowlist` for gateway + node when unset)
*   `tools.exec.ask` (default: `on-miss`)
*   `tools.exec.node` (default: unset)
*   `tools.exec.strictInlineEval` (default: false): when true, inline interpreter eval forms such as `python -c`, `node -e`, `ruby -e`, `perl -e`, `php -r`, `lua -e`, and `osascript -e` always require explicit approval and are never persisted by `allow-always`.
*   `tools.exec.pathPrepend`: list of directories to prepend to `PATH` for exec runs (gateway + sandbox only).
*   `tools.exec.safeBins`: stdin-only safe binaries that can run without explicit allowlist entries. For behavior details, see [Safe bins](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-stdin-only).
*   `tools.exec.safeBinTrustedDirs`: additional explicit directories trusted for `safeBins` path checks. `PATH` entries are never auto-trusted. Built-in defaults are `/bin` and `/usr/bin`.
*   `tools.exec.safeBinProfiles`: optional custom argv policy per safe bin (`minPositional`, `maxPositional`, `allowedValueFlags`, `deniedFlags`).

Example:

```
{
  tools: {
    exec: {
      pathPrepend: ["~/bin", "/opt/oss/bin"],
    },
  },
}
```

### [​](https://docs.openclaw.ai/tools/exec#path-handling)

PATH handling

*   `host=gateway`: merges your login-shell `PATH` into the exec environment. `env.PATH` overrides are rejected for host execution. The daemon itself still runs with a minimal `PATH`:
    *   macOS: `/opt/homebrew/bin`, `/usr/local/bin`, `/usr/bin`, `/bin`
    *   Linux: `/usr/local/bin`, `/usr/bin`, `/bin`

*   `host=sandbox`: runs `sh -lc` (login shell) inside the container, so `/etc/profile` may reset `PATH`. OpenClaw prepends `env.PATH` after profile sourcing via an internal env var (no shell interpolation); `tools.exec.pathPrepend` applies here too.
*   `host=node`: only non-blocked env overrides you pass are sent to the node. `env.PATH` overrides are rejected for host execution and ignored by node hosts. If you need additional PATH entries on a node, configure the node host service environment (systemd/launchd) or install tools in standard locations.

Per-agent node binding (use the agent list index in config):

```
openclaw config get agents.list
openclaw config set agents.list[0].tools.exec.node "node-id-or-name"
```

Control UI: the Nodes tab includes a small “Exec node binding” panel for the same settings.
## [​](https://docs.openclaw.ai/tools/exec#session-overrides-/exec)

Session overrides (`/exec`)

Use `/exec` to set **per-session** defaults for `host`, `security`, `ask`, and `node`. Send `/exec` with no arguments to show the current values.Example:

```
/exec host=gateway security=allowlist ask=on-miss node=mac-1
```

## [​](https://docs.openclaw.ai/tools/exec#authorization-model)

Authorization model

`/exec` is only honored for **authorized senders** (channel allowlists/pairing plus `commands.useAccessGroups`). It updates **session state only** and does not write config. To hard-disable exec, deny it via tool policy (`tools.deny: ["exec"]` or per-agent). Host approvals still apply unless you explicitly set `security=full` and `ask=off`.
## [​](https://docs.openclaw.ai/tools/exec#exec-approvals-companion-app-/-node-host)

Exec approvals (companion app / node host)

Sandboxed agents can require per-request approval before `exec` runs on the gateway or node host. See [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals) for the policy, allowlist, and UI flow.When approvals are required, the exec tool returns immediately with `status: "approval-pending"` and an approval id. Once approved (or denied / timed out), the Gateway emits system events (`Exec finished` / `Exec denied`). If the command is still running after `tools.exec.approvalRunningNoticeMs`, a single `Exec running` notice is emitted.
## [​](https://docs.openclaw.ai/tools/exec#allowlist-+-safe-bins)

Allowlist + safe bins

Manual allowlist enforcement matches **resolved binary paths only** (no basename matches). When `security=allowlist`, shell commands are auto-allowed only if every pipeline segment is allowlisted or a safe bin. Chaining (`;`, `&&`, `||`) and redirections are rejected in allowlist mode unless every top-level segment satisfies the allowlist (including safe bins). Redirections remain unsupported.`autoAllowSkills` is a separate convenience path in exec approvals. It is not the same as manual path allowlist entries. For strict explicit trust, keep `autoAllowSkills` disabled.Use the two controls for different jobs:
*   `tools.exec.safeBins`: small, stdin-only stream filters.
*   `tools.exec.safeBinTrustedDirs`: explicit extra trusted directories for safe-bin executable paths.
*   `tools.exec.safeBinProfiles`: explicit argv policy for custom safe bins.
*   allowlist: explicit trust for executable paths.

Do not treat `safeBins` as a generic allowlist, and do not add interpreter/runtime binaries (for example `python3`, `node`, `ruby`, `bash`). If you need those, use explicit allowlist entries and keep approval prompts enabled. `openclaw security audit` warns when interpreter/runtime `safeBins` entries are missing explicit profiles, and `openclaw doctor --fix` can scaffold missing custom `safeBinProfiles` entries. `openclaw security audit` and `openclaw doctor` also warn when you explicitly add broad-behavior bins such as `jq` back into `safeBins`. If you explicitly allowlist interpreters, enable `tools.exec.strictInlineEval` so inline code-eval forms still require a fresh approval.For full policy details and examples, see [Exec approvals](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-stdin-only) and [Safe bins versus allowlist](https://docs.openclaw.ai/tools/exec-approvals#safe-bins-versus-allowlist).
## [​](https://docs.openclaw.ai/tools/exec#examples)

Examples

Foreground:

```
{ "tool": "exec", "command": "ls -la" }
```

Background + poll:

```
{"tool":"exec","command":"npm run build","yieldMs":1000}
{"tool":"process","action":"poll","sessionId":"<id>"}
```

Send keys (tmux-style):

```
{"tool":"process","action":"send-keys","sessionId":"<id>","keys":["Enter"]}
{"tool":"process","action":"send-keys","sessionId":"<id>","keys":["C-c"]}
{"tool":"process","action":"send-keys","sessionId":"<id>","keys":["Up","Up","Enter"]}
```

Submit (send CR only):

```
{ "tool": "process", "action": "submit", "sessionId": "<id>" }
```

Paste (bracketed by default):

```
{ "tool": "process", "action": "paste", "sessionId": "<id>", "text": "line1\nline2\n" }
```

## [​](https://docs.openclaw.ai/tools/exec#apply_patch)

apply_patch

`apply_patch` is a subtool of `exec` for structured multi-file edits. It is enabled by default for OpenAI and OpenAI Codex models. Use config only when you want to disable it or restrict it to specific models:

```
{
  tools: {
    exec: {
      applyPatch: { workspaceOnly: true, allowModels: ["gpt-5.2"] },
    },
  },
}
```

Notes:
*   Only available for OpenAI/OpenAI Codex models.
*   Tool policy still applies; `allow: ["write"]` implicitly allows `apply_patch`.
*   Config lives under `tools.exec.applyPatch`.
*   `tools.exec.applyPatch.enabled` defaults to `true`; set it to `false` to disable the tool for OpenAI models.
*   `tools.exec.applyPatch.workspaceOnly` defaults to `true` (workspace-contained). Set it to `false` only if you intentionally want `apply_patch` to write/delete outside the workspace directory.

[Elevated Mode](https://docs.openclaw.ai/tools/elevated)[Exec Approvals](https://docs.openclaw.ai/tools/exec-approvals)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
