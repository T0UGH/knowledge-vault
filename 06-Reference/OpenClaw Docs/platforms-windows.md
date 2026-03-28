# platforms/windows

Source: https://docs.openclaw.ai/platforms/windows

Title: Windows - OpenClaw

URL Source: https://docs.openclaw.ai/platforms/windows

Markdown Content:
# Windows - OpenClaw

[Skip to main content](https://docs.openclaw.ai/platforms/windows#content-area)

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

Platforms overview

Windows

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Platforms overview

*   [Platforms](https://docs.openclaw.ai/platforms)
*   [macOS App](https://docs.openclaw.ai/platforms/macos)
*   [Linux App](https://docs.openclaw.ai/platforms/linux)
*   [Windows](https://docs.openclaw.ai/platforms/windows)
*   [Android App](https://docs.openclaw.ai/platforms/android)
*   [iOS App](https://docs.openclaw.ai/platforms/ios)

##### macOS companion app

*   [macOS Dev Setup](https://docs.openclaw.ai/platforms/mac/dev-setup)
*   [Menu Bar](https://docs.openclaw.ai/platforms/mac/menu-bar)
*   [Voice Wake (macOS)](https://docs.openclaw.ai/platforms/mac/voicewake)
*   [Voice Overlay](https://docs.openclaw.ai/platforms/mac/voice-overlay)
*   [WebChat (macOS)](https://docs.openclaw.ai/platforms/mac/webchat)
*   [Canvas](https://docs.openclaw.ai/platforms/mac/canvas)
*   [Gateway Lifecycle](https://docs.openclaw.ai/platforms/mac/child-process)
*   [Health Checks (macOS)](https://docs.openclaw.ai/platforms/mac/health)
*   [Menu Bar Icon](https://docs.openclaw.ai/platforms/mac/icon)
*   [macOS Logging](https://docs.openclaw.ai/platforms/mac/logging)
*   [macOS Permissions](https://docs.openclaw.ai/platforms/mac/permissions)
*   [Remote Control](https://docs.openclaw.ai/platforms/mac/remote)
*   [macOS Signing](https://docs.openclaw.ai/platforms/mac/signing)
*   [Gateway on macOS](https://docs.openclaw.ai/platforms/mac/bundled-gateway)
*   [macOS IPC](https://docs.openclaw.ai/platforms/mac/xpc)
*   [Skills (macOS)](https://docs.openclaw.ai/platforms/mac/skills)
*   [Peekaboo Bridge](https://docs.openclaw.ai/platforms/mac/peekaboo)

On this page

*   [Windows](https://docs.openclaw.ai/platforms/windows#windows)
*   [WSL2 (recommended)](https://docs.openclaw.ai/platforms/windows#wsl2-recommended)
*   [Native Windows status](https://docs.openclaw.ai/platforms/windows#native-windows-status)
*   [Gateway](https://docs.openclaw.ai/platforms/windows#gateway)
*   [Gateway service install (CLI)](https://docs.openclaw.ai/platforms/windows#gateway-service-install-cli)
*   [Gateway auto-start before Windows login](https://docs.openclaw.ai/platforms/windows#gateway-auto-start-before-windows-login)
*   [1) Keep user services running without login](https://docs.openclaw.ai/platforms/windows#1-keep-user-services-running-without-login)
*   [2) Install the OpenClaw gateway user service](https://docs.openclaw.ai/platforms/windows#2-install-the-openclaw-gateway-user-service)
*   [3) Start WSL automatically at Windows boot](https://docs.openclaw.ai/platforms/windows#3-start-wsl-automatically-at-windows-boot)
*   [Verify startup chain](https://docs.openclaw.ai/platforms/windows#verify-startup-chain)
*   [Advanced: expose WSL services over LAN (portproxy)](https://docs.openclaw.ai/platforms/windows#advanced-expose-wsl-services-over-lan-portproxy)
*   [Step-by-step WSL2 install](https://docs.openclaw.ai/platforms/windows#step-by-step-wsl2-install)
*   [1) Install WSL2 + Ubuntu](https://docs.openclaw.ai/platforms/windows#1-install-wsl2-%2B-ubuntu)
*   [2) Enable systemd (required for gateway install)](https://docs.openclaw.ai/platforms/windows#2-enable-systemd-required-for-gateway-install)
*   [3) Install OpenClaw (inside WSL)](https://docs.openclaw.ai/platforms/windows#3-install-openclaw-inside-wsl)
*   [Windows companion app](https://docs.openclaw.ai/platforms/windows#windows-companion-app)

Platforms overview

# Windows

# [​](https://docs.openclaw.ai/platforms/windows#windows)

Windows

OpenClaw supports both **native Windows** and **WSL2**. WSL2 is the more stable path and recommended for the full experience — the CLI, Gateway, and tooling run inside Linux with full compatibility. Native Windows works for core CLI and Gateway use, with some caveats noted below.Native Windows companion apps are planned.
## [​](https://docs.openclaw.ai/platforms/windows#wsl2-recommended)

WSL2 (recommended)

*   [Getting Started](https://docs.openclaw.ai/start/getting-started) (use inside WSL)
*   [Install & updates](https://docs.openclaw.ai/install/updating)
*   Official WSL2 guide (Microsoft): [https://learn.microsoft.com/windows/wsl/install](https://learn.microsoft.com/windows/wsl/install)

## [​](https://docs.openclaw.ai/platforms/windows#native-windows-status)

Native Windows status

Native Windows CLI flows are improving, but WSL2 is still the recommended path.What works well on native Windows today:
*   website installer via `install.ps1`
*   local CLI use such as `openclaw --version`, `openclaw doctor`, and `openclaw plugins list --json`
*   embedded local-agent/provider smoke such as:

```
openclaw agent --local --agent main --thinking low -m "Reply with exactly WINDOWS-HATCH-OK."
```

Current caveats:
*   `openclaw onboard --non-interactive` still expects a reachable local gateway unless you pass `--skip-health`
*   `openclaw onboard --non-interactive --install-daemon` and `openclaw gateway install` try Windows Scheduled Tasks first
*   if Scheduled Task creation is denied, OpenClaw falls back to a per-user Startup-folder login item and starts the gateway immediately
*   if `schtasks` itself wedges or stops responding, OpenClaw now aborts that path quickly and falls back instead of hanging forever
*   Scheduled Tasks are still preferred when available because they provide better supervisor status

If you want the native CLI only, without gateway service install, use one of these:

```
openclaw onboard --non-interactive --skip-health
openclaw gateway run
```

If you do want managed startup on native Windows:

```
openclaw gateway install
openclaw gateway status --json
```

If Scheduled Task creation is blocked, the fallback service mode still auto-starts after login through the current user’s Startup folder.
## [​](https://docs.openclaw.ai/platforms/windows#gateway)

Gateway

*   [Gateway runbook](https://docs.openclaw.ai/gateway)
*   [Configuration](https://docs.openclaw.ai/gateway/configuration)

## [​](https://docs.openclaw.ai/platforms/windows#gateway-service-install-cli)

Gateway service install (CLI)

Inside WSL2:

```
openclaw onboard --install-daemon
```

Or:

```
openclaw gateway install
```

Or:

```
openclaw configure
```

Select **Gateway service** when prompted.Repair/migrate:

```
openclaw doctor
```

## [​](https://docs.openclaw.ai/platforms/windows#gateway-auto-start-before-windows-login)

Gateway auto-start before Windows login

For headless setups, ensure the full boot chain runs even when no one logs into Windows.
### [​](https://docs.openclaw.ai/platforms/windows#1-keep-user-services-running-without-login)

1) Keep user services running without login

Inside WSL:

```
sudo loginctl enable-linger "$(whoami)"
```

### [​](https://docs.openclaw.ai/platforms/windows#2-install-the-openclaw-gateway-user-service)

2) Install the OpenClaw gateway user service

Inside WSL:

```
openclaw gateway install
```

### [​](https://docs.openclaw.ai/platforms/windows#3-start-wsl-automatically-at-windows-boot)

3) Start WSL automatically at Windows boot

In PowerShell as Administrator:

```
schtasks /create /tn "WSL Boot" /tr "wsl.exe -d Ubuntu --exec /bin/true" /sc onstart /ru SYSTEM
```

Replace `Ubuntu` with your distro name from:

```
wsl --list --verbose
```

### [​](https://docs.openclaw.ai/platforms/windows#verify-startup-chain)

Verify startup chain

After a reboot (before Windows sign-in), check from WSL:

```
systemctl --user is-enabled openclaw-gateway
systemctl --user status openclaw-gateway --no-pager
```

## [​](https://docs.openclaw.ai/platforms/windows#advanced-expose-wsl-services-over-lan-portproxy)

Advanced: expose WSL services over LAN (portproxy)

WSL has its own virtual network. If another machine needs to reach a service running **inside WSL** (SSH, a local TTS server, or the Gateway), you must forward a Windows port to the current WSL IP. The WSL IP changes after restarts, so you may need to refresh the forwarding rule.Example (PowerShell **as Administrator**):

```
$Distro = "Ubuntu-24.04"
$ListenPort = 2222
$TargetPort = 22

$WslIp = (wsl -d $Distro -- hostname -I).Trim().Split(" ")[0]
if (-not $WslIp) { throw "WSL IP not found." }

netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=$ListenPort `
  connectaddress=$WslIp connectport=$TargetPort
```

Allow the port through Windows Firewall (one-time):

```
New-NetFirewallRule -DisplayName "WSL SSH $ListenPort" -Direction Inbound `
  -Protocol TCP -LocalPort $ListenPort -Action Allow
```

Refresh the portproxy after WSL restarts:

```
netsh interface portproxy delete v4tov4 listenport=$ListenPort listenaddress=0.0.0.0 | Out-Null
netsh interface portproxy add v4tov4 listenport=$ListenPort listenaddress=0.0.0.0 `
  connectaddress=$WslIp connectport=$TargetPort | Out-Null
```

Notes:
*   SSH from another machine targets the **Windows host IP** (example: `ssh user@windows-host -p 2222`).
*   Remote nodes must point at a **reachable** Gateway URL (not `127.0.0.1`); use `openclaw status --all` to confirm.
*   Use `listenaddress=0.0.0.0` for LAN access; `127.0.0.1` keeps it local only.
*   If you want this automatic, register a Scheduled Task to run the refresh step at login.

## [​](https://docs.openclaw.ai/platforms/windows#step-by-step-wsl2-install)

Step-by-step WSL2 install

### [​](https://docs.openclaw.ai/platforms/windows#1-install-wsl2-+-ubuntu)

1) Install WSL2 + Ubuntu

Open PowerShell (Admin):

```
wsl --install
# Or pick a distro explicitly:
wsl --list --online
wsl --install -d Ubuntu-24.04
```

Reboot if Windows asks.
### [​](https://docs.openclaw.ai/platforms/windows#2-enable-systemd-required-for-gateway-install)

2) Enable systemd (required for gateway install)

In your WSL terminal:

```
sudo tee /etc/wsl.conf >/dev/null <<'EOF'
[boot]
systemd=true
EOF
```

Then from PowerShell:

```
wsl --shutdown
```

Re-open Ubuntu, then verify:

```
systemctl --user status
```

### [​](https://docs.openclaw.ai/platforms/windows#3-install-openclaw-inside-wsl)

3) Install OpenClaw (inside WSL)

Follow the Linux Getting Started flow inside WSL:

```
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
pnpm ui:build # auto-installs UI deps on first run
pnpm build
openclaw onboard
```

Full guide: [Getting Started](https://docs.openclaw.ai/start/getting-started)
## [​](https://docs.openclaw.ai/platforms/windows#windows-companion-app)

Windows companion app

We do not have a Windows companion app yet. Contributions are welcome if you want contributions to make it happen.

[Linux App](https://docs.openclaw.ai/platforms/linux)[Android App](https://docs.openclaw.ai/platforms/android)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
