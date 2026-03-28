# tools/browser-linux-troubleshooting

Source: https://docs.openclaw.ai/tools/browser-linux-troubleshooting

Title: Browser Troubleshooting - OpenClaw

URL Source: https://docs.openclaw.ai/tools/browser-linux-troubleshooting

Markdown Content:
# Browser Troubleshooting - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#content-area)

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

Web Browser

Browser Troubleshooting

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
    *   [Browser (OpenClaw-managed)](https://docs.openclaw.ai/tools/browser)
    *   [Browser Login](https://docs.openclaw.ai/tools/browser-login)
    *   [Browser Troubleshooting](https://docs.openclaw.ai/tools/browser-linux-troubleshooting)
    *   [WSL2 + Windows + remote Chrome CDP troubleshooting](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting)

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

*   [Browser Troubleshooting (Linux)](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#browser-troubleshooting-linux)
*   [Problem: “Failed to start Chrome CDP on port 18800”](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#problem-%E2%80%9Cfailed-to-start-chrome-cdp-on-port-18800%E2%80%9D)
*   [Root Cause](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#root-cause)
*   [Solution 1: Install Google Chrome (Recommended)](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#solution-1-install-google-chrome-recommended)
*   [Solution 2: Use Snap Chromium with Attach-Only Mode](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#solution-2-use-snap-chromium-with-attach-only-mode)
*   [Verifying the Browser Works](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#verifying-the-browser-works)
*   [Config Reference](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#config-reference)
*   [Problem: “No Chrome tabs found for profile=“user""](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#problem-%E2%80%9Cno-chrome-tabs-found-for-profile%3D%E2%80%9Cuser)

Web Browser

# Browser Troubleshooting

# [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#browser-troubleshooting-linux)

Browser Troubleshooting (Linux)

## [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#problem-%E2%80%9Cfailed-to-start-chrome-cdp-on-port-18800%E2%80%9D)

Problem: “Failed to start Chrome CDP on port 18800”

OpenClaw’s browser control server fails to launch Chrome/Brave/Edge/Chromium with the error:

```
{"error":"Error: Failed to start Chrome CDP on port 18800 for profile \"openclaw\"."}
```

### [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#root-cause)

Root Cause

On Ubuntu (and many Linux distros), the default Chromium installation is a **snap package**. Snap’s AppArmor confinement interferes with how OpenClaw spawns and monitors the browser process.The `apt install chromium` command installs a stub package that redirects to snap:

```
Note, selecting 'chromium-browser' instead of 'chromium'
chromium-browser is already the newest version (2:1snap1-0ubuntu2).
```

This is NOT a real browser - it’s just a wrapper.
### [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#solution-1-install-google-chrome-recommended)

Solution 1: Install Google Chrome (Recommended)

Install the official Google Chrome `.deb` package, which is not sandboxed by snap:

```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
sudo apt --fix-broken install -y  # if there are dependency errors
```

Then update your OpenClaw config (`~/.openclaw/openclaw.json`):

```
{
  "browser": {
    "enabled": true,
    "executablePath": "/usr/bin/google-chrome-stable",
    "headless": true,
    "noSandbox": true
  }
}
```

### [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#solution-2-use-snap-chromium-with-attach-only-mode)

Solution 2: Use Snap Chromium with Attach-Only Mode

If you must use snap Chromium, configure OpenClaw to attach to a manually-started browser:
1.   Update config:

```
{
  "browser": {
    "enabled": true,
    "attachOnly": true,
    "headless": true,
    "noSandbox": true
  }
}
```

1.   Start Chromium manually:

```
chromium-browser --headless --no-sandbox --disable-gpu \
  --remote-debugging-port=18800 \
  --user-data-dir=$HOME/.openclaw/browser/openclaw/user-data \
  about:blank &
```

1.   Optionally create a systemd user service to auto-start Chrome:

```
# ~/.config/systemd/user/openclaw-browser.service
[Unit]
Description=OpenClaw Browser (Chrome CDP)
After=network.target

[Service]
ExecStart=/snap/bin/chromium --headless --no-sandbox --disable-gpu --remote-debugging-port=18800 --user-data-dir=%h/.openclaw/browser/openclaw/user-data about:blank
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
```

Enable with: `systemctl --user enable --now openclaw-browser.service`
### [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#verifying-the-browser-works)

Verifying the Browser Works

Check status:

```
curl -s http://127.0.0.1:18791/ | jq '{running, pid, chosenBrowser}'
```

Test browsing:

```
curl -s -X POST http://127.0.0.1:18791/start
curl -s http://127.0.0.1:18791/tabs
```

### [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#config-reference)

Config Reference

| Option | Description | Default |
| --- | --- | --- |
| `browser.enabled` | Enable browser control | `true` |
| `browser.executablePath` | Path to a Chromium-based browser binary (Chrome/Brave/Edge/Chromium) | auto-detected (prefers default browser when Chromium-based) |
| `browser.headless` | Run without GUI | `false` |
| `browser.noSandbox` | Add `--no-sandbox` flag (needed for some Linux setups) | `false` |
| `browser.attachOnly` | Don’t launch browser, only attach to existing | `false` |
| `browser.cdpPort` | Chrome DevTools Protocol port | `18800` |

### [​](https://docs.openclaw.ai/tools/browser-linux-troubleshooting#problem-%E2%80%9Cno-chrome-tabs-found-for-profile=%E2%80%9Cuser)

Problem: “No Chrome tabs found for profile=“user""

You’re using an `existing-session` / Chrome MCP profile. OpenClaw can see local Chrome, but there are no open tabs available to attach to.Fix options:
1.   **Use the managed browser:**`openclaw browser start --browser-profile openclaw` (or set `browser.defaultProfile: "openclaw"`).
2.   **Use Chrome MCP:** make sure local Chrome is running with at least one open tab, then retry with `--browser-profile user`.

Notes:
*   `user` is host-only. For Linux servers, containers, or remote hosts, prefer CDP profiles.
*   Local `openclaw` profiles auto-assign `cdpPort`/`cdpUrl`; only set those for remote CDP.

[Browser Login](https://docs.openclaw.ai/tools/browser-login)[WSL2 + Windows + remote Chrome CDP troubleshooting](https://docs.openclaw.ai/tools/browser-wsl2-windows-remote-cdp-troubleshooting)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
