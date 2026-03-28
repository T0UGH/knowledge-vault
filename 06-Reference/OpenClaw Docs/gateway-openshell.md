# gateway/openshell

Source: https://docs.openclaw.ai/gateway/openshell

Title: OpenShell - OpenClaw

URL Source: https://docs.openclaw.ai/gateway/openshell

Markdown Content:
# OpenShell - OpenClaw

[Skip to main content](https://docs.openclaw.ai/gateway/openshell#content-area)

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

Security and sandboxing

OpenShell

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Gateway

*   [Gateway Runbook](https://docs.openclaw.ai/gateway)
*   Configuration and operations 
*   Security and sandboxing 
    *   [Security](https://docs.openclaw.ai/gateway/security)
    *   [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing)
    *   [OpenShell](https://docs.openclaw.ai/gateway/openshell)
    *   [Sandbox vs Tool Policy vs Elevated](https://docs.openclaw.ai/gateway/sandbox-vs-tool-policy-vs-elevated)

*   Protocols and APIs 
*   Networking and discovery 

##### Remote access

*   [Remote Access](https://docs.openclaw.ai/gateway/remote)
*   [Remote Gateway Setup](https://docs.openclaw.ai/gateway/remote-gateway-readme)
*   [Tailscale](https://docs.openclaw.ai/gateway/tailscale)

##### Security

*   [Formal Verification (Security Models)](https://docs.openclaw.ai/security/formal-verification)
*   [Threat Model (MITRE ATLAS)](https://docs.openclaw.ai/security/THREAT-MODEL-ATLAS)
*   [Contributing to the Threat Model](https://docs.openclaw.ai/security/CONTRIBUTING-THREAT-MODEL)

##### Nodes and devices

*   [Nodes](https://docs.openclaw.ai/nodes)
*   [Node Troubleshooting](https://docs.openclaw.ai/nodes/troubleshooting)
*   [Media Understanding](https://docs.openclaw.ai/nodes/media-understanding)
*   [Image and Media Support](https://docs.openclaw.ai/nodes/images)
*   [Audio and Voice Notes](https://docs.openclaw.ai/nodes/audio)
*   [Camera Capture](https://docs.openclaw.ai/nodes/camera)
*   [Talk Mode](https://docs.openclaw.ai/nodes/talk)
*   [Voice Wake](https://docs.openclaw.ai/nodes/voicewake)
*   [Location Command](https://docs.openclaw.ai/nodes/location-command)
*   [Text-to-Speech](https://docs.openclaw.ai/tools/tts)

##### Web interfaces

*   [Web](https://docs.openclaw.ai/web)
*   [Control UI](https://docs.openclaw.ai/web/control-ui)
*   [Dashboard](https://docs.openclaw.ai/web/dashboard)
*   [WebChat](https://docs.openclaw.ai/web/webchat)
*   [TUI](https://docs.openclaw.ai/web/tui)

On this page

*   [OpenShell](https://docs.openclaw.ai/gateway/openshell#openshell)
*   [Prerequisites](https://docs.openclaw.ai/gateway/openshell#prerequisites)
*   [Quick start](https://docs.openclaw.ai/gateway/openshell#quick-start)
*   [Workspace modes](https://docs.openclaw.ai/gateway/openshell#workspace-modes)
*   [mirror](https://docs.openclaw.ai/gateway/openshell#mirror)
*   [remote](https://docs.openclaw.ai/gateway/openshell#remote)
*   [Choosing a mode](https://docs.openclaw.ai/gateway/openshell#choosing-a-mode)
*   [Configuration reference](https://docs.openclaw.ai/gateway/openshell#configuration-reference)
*   [Examples](https://docs.openclaw.ai/gateway/openshell#examples)
*   [Minimal remote setup](https://docs.openclaw.ai/gateway/openshell#minimal-remote-setup)
*   [Mirror mode with GPU](https://docs.openclaw.ai/gateway/openshell#mirror-mode-with-gpu)
*   [Per-agent OpenShell with custom gateway](https://docs.openclaw.ai/gateway/openshell#per-agent-openshell-with-custom-gateway)
*   [Lifecycle management](https://docs.openclaw.ai/gateway/openshell#lifecycle-management)
*   [When to recreate](https://docs.openclaw.ai/gateway/openshell#when-to-recreate)
*   [Current limitations](https://docs.openclaw.ai/gateway/openshell#current-limitations)
*   [How it works](https://docs.openclaw.ai/gateway/openshell#how-it-works)
*   [See also](https://docs.openclaw.ai/gateway/openshell#see-also)

Security and sandboxing

# OpenShell

# [​](https://docs.openclaw.ai/gateway/openshell#openshell)

OpenShell

OpenShell is a managed sandbox backend for OpenClaw. Instead of running Docker containers locally, OpenClaw delegates sandbox lifecycle to the `openshell` CLI, which provisions remote environments with SSH-based command execution.The OpenShell plugin reuses the same core SSH transport and remote filesystem bridge as the generic [SSH backend](https://docs.openclaw.ai/gateway/sandboxing#ssh-backend). It adds OpenShell-specific lifecycle (`sandbox create/get/delete`, `sandbox ssh-config`) and an optional `mirror` workspace mode.
## [​](https://docs.openclaw.ai/gateway/openshell#prerequisites)

Prerequisites

*   The `openshell` CLI installed and on `PATH` (or set a custom path via `plugins.entries.openshell.config.command`)
*   An OpenShell account with sandbox access
*   OpenClaw Gateway running on the host

## [​](https://docs.openclaw.ai/gateway/openshell#quick-start)

Quick start

1.   Enable the plugin and set the sandbox backend:

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "all",
        backend: "openshell",
        scope: "session",
        workspaceAccess: "rw",
      },
    },
  },
  plugins: {
    entries: {
      openshell: {
        enabled: true,
        config: {
          from: "openclaw",
          mode: "remote",
        },
      },
    },
  },
}
```

1.   Restart the Gateway. On the next agent turn, OpenClaw creates an OpenShell sandbox and routes tool execution through it.
2.   Verify:

```
openclaw sandbox list
openclaw sandbox explain
```

## [​](https://docs.openclaw.ai/gateway/openshell#workspace-modes)

Workspace modes

This is the most important decision when using OpenShell.
### [​](https://docs.openclaw.ai/gateway/openshell#mirror)

`mirror`

Use `plugins.entries.openshell.config.mode: "mirror"` when you want the **local workspace to stay canonical**.Behavior:
*   Before `exec`, OpenClaw syncs the local workspace into the OpenShell sandbox.
*   After `exec`, OpenClaw syncs the remote workspace back to the local workspace.
*   File tools still operate through the sandbox bridge, but the local workspace remains the source of truth between turns.

Best for:
*   You edit files locally outside OpenClaw and want those changes visible in the sandbox automatically.
*   You want the OpenShell sandbox to behave as much like the Docker backend as possible.
*   You want the host workspace to reflect sandbox writes after each exec turn.

Tradeoff: extra sync cost before and after each exec.
### [​](https://docs.openclaw.ai/gateway/openshell#remote)

`remote`

Use `plugins.entries.openshell.config.mode: "remote"` when you want the **OpenShell workspace to become canonical**.Behavior:
*   When the sandbox is first created, OpenClaw seeds the remote workspace from the local workspace once.
*   After that, `exec`, `read`, `write`, `edit`, and `apply_patch` operate directly against the remote OpenShell workspace.
*   OpenClaw does **not** sync remote changes back into the local workspace.
*   Prompt-time media reads still work because file and media tools read through the sandbox bridge.

Best for:
*   The sandbox should live primarily on the remote side.
*   You want lower per-turn sync overhead.
*   You do not want host-local edits to silently overwrite remote sandbox state.

Important: if you edit files on the host outside OpenClaw after the initial seed, the remote sandbox does **not** see those changes. Use `openclaw sandbox recreate` to re-seed.
### [​](https://docs.openclaw.ai/gateway/openshell#choosing-a-mode)

Choosing a mode

|  | `mirror` | `remote` |
| --- | --- | --- |
| **Canonical workspace** | Local host | Remote OpenShell |
| **Sync direction** | Bidirectional (each exec) | One-time seed |
| **Per-turn overhead** | Higher (upload + download) | Lower (direct remote ops) |
| **Local edits visible?** | Yes, on next exec | No, until recreate |
| **Best for** | Development workflows | Long-running agents, CI |

## [​](https://docs.openclaw.ai/gateway/openshell#configuration-reference)

Configuration reference

All OpenShell config lives under `plugins.entries.openshell.config`:

| Key | Type | Default | Description |
| --- | --- | --- | --- |
| `mode` | `"mirror"` or `"remote"` | `"mirror"` | Workspace sync mode |
| `command` | `string` | `"openshell"` | Path or name of the `openshell` CLI |
| `from` | `string` | `"openclaw"` | Sandbox source for first-time create |
| `gateway` | `string` | — | OpenShell gateway name (`--gateway`) |
| `gatewayEndpoint` | `string` | — | OpenShell gateway endpoint URL (`--gateway-endpoint`) |
| `policy` | `string` | — | OpenShell policy ID for sandbox creation |
| `providers` | `string[]` | `[]` | Provider names to attach when sandbox is created |
| `gpu` | `boolean` | `false` | Request GPU resources |
| `autoProviders` | `boolean` | `true` | Pass `--auto-providers` during sandbox create |
| `remoteWorkspaceDir` | `string` | `"/sandbox"` | Primary writable workspace inside the sandbox |
| `remoteAgentWorkspaceDir` | `string` | `"/agent"` | Agent workspace mount path (for read-only access) |
| `timeoutSeconds` | `number` | `120` | Timeout for `openshell` CLI operations |

Sandbox-level settings (`mode`, `scope`, `workspaceAccess`) are configured under `agents.defaults.sandbox` as with any backend. See [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing) for the full matrix.
## [​](https://docs.openclaw.ai/gateway/openshell#examples)

Examples

### [​](https://docs.openclaw.ai/gateway/openshell#minimal-remote-setup)

Minimal remote setup

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "all",
        backend: "openshell",
      },
    },
  },
  plugins: {
    entries: {
      openshell: {
        enabled: true,
        config: {
          from: "openclaw",
          mode: "remote",
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/openshell#mirror-mode-with-gpu)

Mirror mode with GPU

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "all",
        backend: "openshell",
        scope: "agent",
        workspaceAccess: "rw",
      },
    },
  },
  plugins: {
    entries: {
      openshell: {
        enabled: true,
        config: {
          from: "openclaw",
          mode: "mirror",
          gpu: true,
          providers: ["openai"],
          timeoutSeconds: 180,
        },
      },
    },
  },
}
```

### [​](https://docs.openclaw.ai/gateway/openshell#per-agent-openshell-with-custom-gateway)

Per-agent OpenShell with custom gateway

```
{
  agents: {
    defaults: {
      sandbox: { mode: "off" },
    },
    list: [
      {
        id: "researcher",
        sandbox: {
          mode: "all",
          backend: "openshell",
          scope: "agent",
          workspaceAccess: "rw",
        },
      },
    ],
  },
  plugins: {
    entries: {
      openshell: {
        enabled: true,
        config: {
          from: "openclaw",
          mode: "remote",
          gateway: "lab",
          gatewayEndpoint: "https://lab.example",
          policy: "strict",
        },
      },
    },
  },
}
```

## [​](https://docs.openclaw.ai/gateway/openshell#lifecycle-management)

Lifecycle management

OpenShell sandboxes are managed through the normal sandbox CLI:

```
# List all sandbox runtimes (Docker + OpenShell)
openclaw sandbox list

# Inspect effective policy
openclaw sandbox explain

# Recreate (deletes remote workspace, re-seeds on next use)
openclaw sandbox recreate --all
```

For `remote` mode, **recreate is especially important**: it deletes the canonical remote workspace for that scope. The next use seeds a fresh remote workspace from the local workspace.For `mirror` mode, recreate mainly resets the remote execution environment because the local workspace remains canonical.
### [​](https://docs.openclaw.ai/gateway/openshell#when-to-recreate)

When to recreate

Recreate after changing any of these:
*   `agents.defaults.sandbox.backend`
*   `plugins.entries.openshell.config.from`
*   `plugins.entries.openshell.config.mode`
*   `plugins.entries.openshell.config.policy`

```
openclaw sandbox recreate --all
```

## [​](https://docs.openclaw.ai/gateway/openshell#current-limitations)

Current limitations

*   Sandbox browser is not supported on the OpenShell backend.
*   `sandbox.docker.binds` does not apply to OpenShell.
*   Docker-specific runtime knobs under `sandbox.docker.*` apply only to the Docker backend.

## [​](https://docs.openclaw.ai/gateway/openshell#how-it-works)

How it works

1.   OpenClaw calls `openshell sandbox create` (with `--from`, `--gateway`, `--policy`, `--providers`, `--gpu` flags as configured).
2.   OpenClaw calls `openshell sandbox ssh-config <name>` to get SSH connection details for the sandbox.
3.   Core writes the SSH config to a temp file and opens an SSH session using the same remote filesystem bridge as the generic SSH backend.
4.   In `mirror` mode: sync local to remote before exec, run, sync back after exec.
5.   In `remote` mode: seed once on create, then operate directly on the remote workspace.

## [​](https://docs.openclaw.ai/gateway/openshell#see-also)

See also

*   [Sandboxing](https://docs.openclaw.ai/gateway/sandboxing) — modes, scopes, and backend comparison
*   [Sandbox vs Tool Policy vs Elevated](https://docs.openclaw.ai/gateway/sandbox-vs-tool-policy-vs-elevated) — debugging blocked tools
*   [Multi-Agent Sandbox and Tools](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools) — per-agent overrides
*   [Sandbox CLI](https://docs.openclaw.ai/cli/sandbox) — `openclaw sandbox` commands

[Sandboxing](https://docs.openclaw.ai/gateway/sandboxing)[Sandbox vs Tool Policy vs Elevated](https://docs.openclaw.ai/gateway/sandbox-vs-tool-policy-vs-elevated)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
