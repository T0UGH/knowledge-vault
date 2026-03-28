# automation/gmail-pubsub

Source: https://docs.openclaw.ai/automation/gmail-pubsub

Title: Gmail PubSub - OpenClaw

URL Source: https://docs.openclaw.ai/automation/gmail-pubsub

Markdown Content:
# Gmail PubSub - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/gmail-pubsub#content-area)

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

Automation

Gmail PubSub

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

*   [Gmail Pub/Sub -> OpenClaw](https://docs.openclaw.ai/automation/gmail-pubsub#gmail-pub%2Fsub-%3E-openclaw)
*   [Prereqs](https://docs.openclaw.ai/automation/gmail-pubsub#prereqs)
*   [Wizard (recommended)](https://docs.openclaw.ai/automation/gmail-pubsub#wizard-recommended)
*   [One-time setup](https://docs.openclaw.ai/automation/gmail-pubsub#one-time-setup)
*   [Start the watch](https://docs.openclaw.ai/automation/gmail-pubsub#start-the-watch)
*   [Run the push handler](https://docs.openclaw.ai/automation/gmail-pubsub#run-the-push-handler)
*   [Expose the handler (advanced, unsupported)](https://docs.openclaw.ai/automation/gmail-pubsub#expose-the-handler-advanced-unsupported)
*   [Test](https://docs.openclaw.ai/automation/gmail-pubsub#test)
*   [Troubleshooting](https://docs.openclaw.ai/automation/gmail-pubsub#troubleshooting)
*   [Cleanup](https://docs.openclaw.ai/automation/gmail-pubsub#cleanup)

Automation

# Gmail PubSub

# [​](https://docs.openclaw.ai/automation/gmail-pubsub#gmail-pub/sub-%3E-openclaw)

Gmail Pub/Sub -> OpenClaw

Goal: Gmail watch -> Pub/Sub push ->`gog gmail watch serve` -> OpenClaw webhook.
## [​](https://docs.openclaw.ai/automation/gmail-pubsub#prereqs)

Prereqs

*   `gcloud` installed and logged in ([install guide](https://docs.cloud.google.com/sdk/docs/install-sdk)).
*   `gog` (gogcli) installed and authorized for the Gmail account ([gogcli.sh](https://gogcli.sh/)).
*   OpenClaw hooks enabled (see [Webhooks](https://docs.openclaw.ai/automation/webhook)).
*   `tailscale` logged in ([tailscale.com](https://tailscale.com/)). Supported setup uses Tailscale Funnel for the public HTTPS endpoint. Other tunnel services can work, but are DIY/unsupported and require manual wiring. Right now, Tailscale is what we support.

Example hook config (enable Gmail preset mapping):

```
{
  hooks: {
    enabled: true,
    token: "OPENCLAW_HOOK_TOKEN",
    path: "/hooks",
    presets: ["gmail"],
  },
}
```

To deliver the Gmail summary to a chat surface, override the preset with a mapping that sets `deliver` + optional `channel`/`to`:

```
{
  hooks: {
    enabled: true,
    token: "OPENCLAW_HOOK_TOKEN",
    presets: ["gmail"],
    mappings: [
      {
        match: { path: "gmail" },
        action: "agent",
        wakeMode: "now",
        name: "Gmail",
        sessionKey: "hook:gmail:{{messages[0].id}}",
        messageTemplate: "New email from {{messages[0].from}}\nSubject: {{messages[0].subject}}\n{{messages[0].snippet}}\n{{messages[0].body}}",
        model: "openai/gpt-5.2-mini",
        deliver: true,
        channel: "last",
        // to: "+15551234567"
      },
    ],
  },
}
```

If you want a fixed channel, set `channel` + `to`. Otherwise `channel: "last"` uses the last delivery route (falls back to WhatsApp).To force a cheaper model for Gmail runs, set `model` in the mapping (`provider/model` or alias). If you enforce `agents.defaults.models`, include it there.To set a default model and thinking level specifically for Gmail hooks, add `hooks.gmail.model` / `hooks.gmail.thinking` in your config:

```
{
  hooks: {
    gmail: {
      model: "openrouter/meta-llama/llama-3.3-70b-instruct:free",
      thinking: "off",
    },
  },
}
```

Notes:
*   Per-hook `model`/`thinking` in the mapping still overrides these defaults.
*   Fallback order: `hooks.gmail.model` → `agents.defaults.model.fallbacks` → primary (auth/rate-limit/timeouts).
*   If `agents.defaults.models` is set, the Gmail model must be in the allowlist.
*   Gmail hook content is wrapped with external-content safety boundaries by default. To disable (dangerous), set `hooks.gmail.allowUnsafeExternalContent: true`.

To customize payload handling further, add `hooks.mappings` or a JS/TS transform module under `~/.openclaw/hooks/transforms` (see [Webhooks](https://docs.openclaw.ai/automation/webhook)).
## [​](https://docs.openclaw.ai/automation/gmail-pubsub#wizard-recommended)

Wizard (recommended)

Use the OpenClaw helper to wire everything together (installs deps on macOS via brew):

```
openclaw webhooks gmail setup \
  --account openclaw@gmail.com
```

Defaults:
*   Uses Tailscale Funnel for the public push endpoint.
*   Writes `hooks.gmail` config for `openclaw webhooks gmail run`.
*   Enables the Gmail hook preset (`hooks.presets: ["gmail"]`).

Path note: when `tailscale.mode` is enabled, OpenClaw automatically sets `hooks.gmail.serve.path` to `/` and keeps the public path at `hooks.gmail.tailscale.path` (default `/gmail-pubsub`) because Tailscale strips the set-path prefix before proxying. If you need the backend to receive the prefixed path, set `hooks.gmail.tailscale.target` (or `--tailscale-target`) to a full URL like `http://127.0.0.1:8788/gmail-pubsub` and match `hooks.gmail.serve.path`.Want a custom endpoint? Use `--push-endpoint <url>` or `--tailscale off`.Platform note: on macOS the wizard installs `gcloud`, `gogcli`, and `tailscale` via Homebrew; on Linux install them manually first.Gateway auto-start (recommended):
*   When `hooks.enabled=true` and `hooks.gmail.account` is set, the Gateway starts `gog gmail watch serve` on boot and auto-renews the watch.
*   Set `OPENCLAW_SKIP_GMAIL_WATCHER=1` to opt out (useful if you run the daemon yourself).
*   Do not run the manual daemon at the same time, or you will hit `listen tcp 127.0.0.1:8788: bind: address already in use`.

Manual daemon (starts `gog gmail watch serve` + auto-renew):

```
openclaw webhooks gmail run
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub#one-time-setup)

One-time setup

1.   Select the GCP project **that owns the OAuth client** used by `gog`.

```
gcloud auth login
gcloud config set project <project-id>
```

Note: Gmail watch requires the Pub/Sub topic to live in the same project as the OAuth client.
1.   Enable APIs:

```
gcloud services enable gmail.googleapis.com pubsub.googleapis.com
```

1.   Create a topic:

```
gcloud pubsub topics create gog-gmail-watch
```

1.   Allow Gmail push to publish:

```
gcloud pubsub topics add-iam-policy-binding gog-gmail-watch \
  --member=serviceAccount:gmail-api-push@system.gserviceaccount.com \
  --role=roles/pubsub.publisher
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub#start-the-watch)

Start the watch

```
gog gmail watch start \
  --account openclaw@gmail.com \
  --label INBOX \
  --topic projects/<project-id>/topics/gog-gmail-watch
```

Save the `history_id` from the output (for debugging).
## [​](https://docs.openclaw.ai/automation/gmail-pubsub#run-the-push-handler)

Run the push handler

Local example (shared token auth):

```
gog gmail watch serve \
  --account openclaw@gmail.com \
  --bind 127.0.0.1 \
  --port 8788 \
  --path /gmail-pubsub \
  --token <shared> \
  --hook-url http://127.0.0.1:18789/hooks/gmail \
  --hook-token OPENCLAW_HOOK_TOKEN \
  --include-body \
  --max-bytes 20000
```

Notes:
*   `--token` protects the push endpoint (`x-gog-token` or `?token=`).
*   `--hook-url` points to OpenClaw `/hooks/gmail` (mapped; isolated run + summary to main).
*   `--include-body` and `--max-bytes` control the body snippet sent to OpenClaw.

Recommended: `openclaw webhooks gmail run` wraps the same flow and auto-renews the watch.
## [​](https://docs.openclaw.ai/automation/gmail-pubsub#expose-the-handler-advanced-unsupported)

Expose the handler (advanced, unsupported)

If you need a non-Tailscale tunnel, wire it manually and use the public URL in the push subscription (unsupported, no guardrails):

```
cloudflared tunnel --url http://127.0.0.1:8788 --no-autoupdate
```

Use the generated URL as the push endpoint:

```
gcloud pubsub subscriptions create gog-gmail-watch-push \
  --topic gog-gmail-watch \
  --push-endpoint "https://<public-url>/gmail-pubsub?token=<shared>"
```

Production: use a stable HTTPS endpoint and configure Pub/Sub OIDC JWT, then run:

```
gog gmail watch serve --verify-oidc --oidc-email <svc@...>
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub#test)

Test

Send a message to the watched inbox:

```
gog gmail send \
  --account openclaw@gmail.com \
  --to openclaw@gmail.com \
  --subject "watch test" \
  --body "ping"
```

Check watch state and history:

```
gog gmail watch status --account openclaw@gmail.com
gog gmail history --account openclaw@gmail.com --since <historyId>
```

## [​](https://docs.openclaw.ai/automation/gmail-pubsub#troubleshooting)

Troubleshooting

*   `Invalid topicName`: project mismatch (topic not in the OAuth client project).
*   `User not authorized`: missing `roles/pubsub.publisher` on the topic.
*   Empty messages: Gmail push only provides `historyId`; fetch via `gog gmail history`.

## [​](https://docs.openclaw.ai/automation/gmail-pubsub#cleanup)

Cleanup

```
gog gmail watch stop --account openclaw@gmail.com
gcloud pubsub subscriptions delete gog-gmail-watch-push
gcloud pubsub topics delete gog-gmail-watch
```

[Webhooks](https://docs.openclaw.ai/automation/webhook)[Polls](https://docs.openclaw.ai/automation/poll)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
