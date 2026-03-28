# tools/browser-login

Source: https://docs.openclaw.ai/tools/browser-login

Title: Browser Login - OpenClaw

URL Source: https://docs.openclaw.ai/tools/browser-login

Markdown Content:
# Browser Login - OpenClaw

[Skip to main content](https://docs.openclaw.ai/tools/browser-login#content-area)

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

Browser Login

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

*   [Browser login + X/Twitter posting](https://docs.openclaw.ai/tools/browser-login#browser-login-%2B-x%2Ftwitter-posting)
*   [Manual login (recommended)](https://docs.openclaw.ai/tools/browser-login#manual-login-recommended)
*   [Which Chrome profile is used?](https://docs.openclaw.ai/tools/browser-login#which-chrome-profile-is-used)
*   [X/Twitter: recommended flow](https://docs.openclaw.ai/tools/browser-login#x%2Ftwitter-recommended-flow)
*   [Sandboxing + host browser access](https://docs.openclaw.ai/tools/browser-login#sandboxing-%2B-host-browser-access)

Web Browser

# Browser Login

# [​](https://docs.openclaw.ai/tools/browser-login#browser-login-+-x/twitter-posting)

Browser login + X/Twitter posting

## [​](https://docs.openclaw.ai/tools/browser-login#manual-login-recommended)

Manual login (recommended)

When a site requires login, **sign in manually** in the **host** browser profile (the openclaw browser).Do **not** give the model your credentials. Automated logins often trigger anti‑bot defenses and can lock the account.Back to the main browser docs: [Browser](https://docs.openclaw.ai/tools/browser).
## [​](https://docs.openclaw.ai/tools/browser-login#which-chrome-profile-is-used)

Which Chrome profile is used?

OpenClaw controls a **dedicated Chrome profile** (named `openclaw`, orange‑tinted UI). This is separate from your daily browser profile.For agent browser tool calls:
*   Default choice: the agent should use its isolated `openclaw` browser.
*   Use `profile="user"` only when existing logged-in sessions matter and the user is at the computer to click/approve any attach prompt.
*   If you have multiple user-browser profiles, specify the profile explicitly instead of guessing.

Two easy ways to access it:
1.   **Ask the agent to open the browser** and then log in yourself.
2.   **Open it via CLI**:

```
openclaw browser start
openclaw browser open https://x.com
```

If you have multiple profiles, pass `--browser-profile <name>` (the default is `openclaw`).
## [​](https://docs.openclaw.ai/tools/browser-login#x/twitter-recommended-flow)

X/Twitter: recommended flow

*   **Read/search/threads:** use the **host** browser (manual login).
*   **Post updates:** use the **host** browser (manual login).

## [​](https://docs.openclaw.ai/tools/browser-login#sandboxing-+-host-browser-access)

Sandboxing + host browser access

Sandboxed browser sessions are **more likely** to trigger bot detection. For X/Twitter (and other strict sites), prefer the **host** browser.If the agent is sandboxed, the browser tool defaults to the sandbox. To allow host control:

```
{
  agents: {
    defaults: {
      sandbox: {
        mode: "non-main",
        browser: {
          allowHostControl: true,
        },
      },
    },
  },
}
```

Then target the host browser:

```
openclaw browser open https://x.com --browser-profile openclaw --target host
```

Or disable sandboxing for the agent that posts updates.

[Browser (OpenClaw-managed)](https://docs.openclaw.ai/tools/browser)[Browser Troubleshooting](https://docs.openclaw.ai/tools/browser-linux-troubleshooting)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
