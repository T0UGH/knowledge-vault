# automation/hooks

Source: https://docs.openclaw.ai/automation/hooks

Title: Hooks - OpenClaw

URL Source: https://docs.openclaw.ai/automation/hooks

Markdown Content:
# Hooks - OpenClaw

[Skip to main content](https://docs.openclaw.ai/automation/hooks#content-area)

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

Hooks

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

*   [Hooks](https://docs.openclaw.ai/automation/hooks#hooks)
*   [Getting Oriented](https://docs.openclaw.ai/automation/hooks#getting-oriented)
*   [Overview](https://docs.openclaw.ai/automation/hooks#overview)
*   [Getting Started](https://docs.openclaw.ai/automation/hooks#getting-started)
*   [Bundled Hooks](https://docs.openclaw.ai/automation/hooks#bundled-hooks)
*   [Onboarding](https://docs.openclaw.ai/automation/hooks#onboarding)
*   [Trust Boundary](https://docs.openclaw.ai/automation/hooks#trust-boundary)
*   [Hook Discovery](https://docs.openclaw.ai/automation/hooks#hook-discovery)
*   [Hook Packs (npm/archives)](https://docs.openclaw.ai/automation/hooks#hook-packs-npm%2Farchives)
*   [Hook Structure](https://docs.openclaw.ai/automation/hooks#hook-structure)
*   [HOOK.md Format](https://docs.openclaw.ai/automation/hooks#hook-md-format)
*   [Metadata Fields](https://docs.openclaw.ai/automation/hooks#metadata-fields)
*   [Handler Implementation](https://docs.openclaw.ai/automation/hooks#handler-implementation)
*   [Event Context](https://docs.openclaw.ai/automation/hooks#event-context)
*   [Event Types](https://docs.openclaw.ai/automation/hooks#event-types)
*   [Command Events](https://docs.openclaw.ai/automation/hooks#command-events)
*   [Session Events](https://docs.openclaw.ai/automation/hooks#session-events)
*   [Agent Events](https://docs.openclaw.ai/automation/hooks#agent-events)
*   [Gateway Events](https://docs.openclaw.ai/automation/hooks#gateway-events)
*   [Session Patch Events](https://docs.openclaw.ai/automation/hooks#session-patch-events)
*   [Session Event Context](https://docs.openclaw.ai/automation/hooks#session-event-context)
*   [Example: Session Patch Logger Hook](https://docs.openclaw.ai/automation/hooks#example-session-patch-logger-hook)
*   [Message Events](https://docs.openclaw.ai/automation/hooks#message-events)
*   [Message Event Context](https://docs.openclaw.ai/automation/hooks#message-event-context)
*   [Example: Message Logger Hook](https://docs.openclaw.ai/automation/hooks#example-message-logger-hook)
*   [Tool Result Hooks (Plugin API)](https://docs.openclaw.ai/automation/hooks#tool-result-hooks-plugin-api)
*   [Plugin Hook Events](https://docs.openclaw.ai/automation/hooks#plugin-hook-events)
*   [before_tool_call](https://docs.openclaw.ai/automation/hooks#before_tool_call)
*   [Compaction lifecycle](https://docs.openclaw.ai/automation/hooks#compaction-lifecycle)
*   [Future Events](https://docs.openclaw.ai/automation/hooks#future-events)
*   [Creating Custom Hooks](https://docs.openclaw.ai/automation/hooks#creating-custom-hooks)
*   [1. Choose Location](https://docs.openclaw.ai/automation/hooks#1-choose-location)
*   [2. Create Directory Structure](https://docs.openclaw.ai/automation/hooks#2-create-directory-structure)
*   [3. Create HOOK.md](https://docs.openclaw.ai/automation/hooks#3-create-hook-md)
*   [4. Create handler.ts](https://docs.openclaw.ai/automation/hooks#4-create-handler-ts)
*   [5. Enable and Test](https://docs.openclaw.ai/automation/hooks#5-enable-and-test)
*   [Configuration](https://docs.openclaw.ai/automation/hooks#configuration)
*   [New Config Format (Recommended)](https://docs.openclaw.ai/automation/hooks#new-config-format-recommended)
*   [Per-Hook Configuration](https://docs.openclaw.ai/automation/hooks#per-hook-configuration)
*   [Extra Directories](https://docs.openclaw.ai/automation/hooks#extra-directories)
*   [Legacy Config Format (Still Supported)](https://docs.openclaw.ai/automation/hooks#legacy-config-format-still-supported)
*   [CLI Commands](https://docs.openclaw.ai/automation/hooks#cli-commands)
*   [List Hooks](https://docs.openclaw.ai/automation/hooks#list-hooks)
*   [Hook Information](https://docs.openclaw.ai/automation/hooks#hook-information)
*   [Check Eligibility](https://docs.openclaw.ai/automation/hooks#check-eligibility)
*   [Enable/Disable](https://docs.openclaw.ai/automation/hooks#enable%2Fdisable)
*   [Bundled hook reference](https://docs.openclaw.ai/automation/hooks#bundled-hook-reference)
*   [session-memory](https://docs.openclaw.ai/automation/hooks#session-memory)
*   [bootstrap-extra-files](https://docs.openclaw.ai/automation/hooks#bootstrap-extra-files)
*   [command-logger](https://docs.openclaw.ai/automation/hooks#command-logger)
*   [boot-md](https://docs.openclaw.ai/automation/hooks#boot-md)
*   [Best Practices](https://docs.openclaw.ai/automation/hooks#best-practices)
*   [Keep Handlers Fast](https://docs.openclaw.ai/automation/hooks#keep-handlers-fast)
*   [Handle Errors Gracefully](https://docs.openclaw.ai/automation/hooks#handle-errors-gracefully)
*   [Filter Events Early](https://docs.openclaw.ai/automation/hooks#filter-events-early)
*   [Use Specific Event Keys](https://docs.openclaw.ai/automation/hooks#use-specific-event-keys)
*   [Debugging](https://docs.openclaw.ai/automation/hooks#debugging)
*   [Enable Hook Logging](https://docs.openclaw.ai/automation/hooks#enable-hook-logging)
*   [Check Discovery](https://docs.openclaw.ai/automation/hooks#check-discovery)
*   [Check Registration](https://docs.openclaw.ai/automation/hooks#check-registration)
*   [Verify Eligibility](https://docs.openclaw.ai/automation/hooks#verify-eligibility)
*   [Testing](https://docs.openclaw.ai/automation/hooks#testing)
*   [Gateway Logs](https://docs.openclaw.ai/automation/hooks#gateway-logs)
*   [Test Hooks Directly](https://docs.openclaw.ai/automation/hooks#test-hooks-directly)
*   [Architecture](https://docs.openclaw.ai/automation/hooks#architecture)
*   [Core Components](https://docs.openclaw.ai/automation/hooks#core-components)
*   [Discovery Flow](https://docs.openclaw.ai/automation/hooks#discovery-flow)
*   [Event Flow](https://docs.openclaw.ai/automation/hooks#event-flow)
*   [Troubleshooting](https://docs.openclaw.ai/automation/hooks#troubleshooting)
*   [Hook Not Discovered](https://docs.openclaw.ai/automation/hooks#hook-not-discovered)
*   [Hook Not Eligible](https://docs.openclaw.ai/automation/hooks#hook-not-eligible)
*   [Hook Not Executing](https://docs.openclaw.ai/automation/hooks#hook-not-executing)
*   [Handler Errors](https://docs.openclaw.ai/automation/hooks#handler-errors)
*   [Migration Guide](https://docs.openclaw.ai/automation/hooks#migration-guide)
*   [From Legacy Config to Discovery](https://docs.openclaw.ai/automation/hooks#from-legacy-config-to-discovery)
*   [See Also](https://docs.openclaw.ai/automation/hooks#see-also)

Automation

# Hooks

# [​](https://docs.openclaw.ai/automation/hooks#hooks)

Hooks

Hooks provide an extensible event-driven system for automating actions in response to agent commands and events. Hooks are automatically discovered from directories and can be inspected with `openclaw hooks`, while hook-pack installation and updates now go through `openclaw plugins`.
## [​](https://docs.openclaw.ai/automation/hooks#getting-oriented)

Getting Oriented

Hooks are small scripts that run when something happens. There are two kinds:
*   **Hooks** (this page): run inside the Gateway when agent events fire, like `/new`, `/reset`, `/stop`, or lifecycle events.
*   **Webhooks**: external HTTP webhooks that let other systems trigger work in OpenClaw. See [Webhook Hooks](https://docs.openclaw.ai/automation/webhook) or use `openclaw webhooks` for Gmail helper commands.

Hooks can also be bundled inside plugins; see [Plugin hooks](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks). `openclaw hooks list` shows both standalone hooks and plugin-managed hooks.Common uses:
*   Save a memory snapshot when you reset a session
*   Keep an audit trail of commands for troubleshooting or compliance
*   Trigger follow-up automation when a session starts or ends
*   Write files into the agent workspace or call external APIs when events fire

If you can write a small TypeScript function, you can write a hook. Managed and bundled hooks are trusted local code. Workspace hooks are discovered automatically, but OpenClaw keeps them disabled until you explicitly enable them via the CLI or config.
## [​](https://docs.openclaw.ai/automation/hooks#overview)

Overview

The hooks system allows you to:
*   Save session context to memory when `/new` is issued
*   Log all commands for auditing
*   Trigger custom automations on agent lifecycle events
*   Extend OpenClaw’s behavior without modifying core code

## [​](https://docs.openclaw.ai/automation/hooks#getting-started)

Getting Started

### [​](https://docs.openclaw.ai/automation/hooks#bundled-hooks)

Bundled Hooks

OpenClaw ships with four bundled hooks that are automatically discovered:
*   **💾 session-memory**: Saves session context to your agent workspace (default `~/.openclaw/workspace/memory/`) when you issue `/new` or `/reset`
*   **📎 bootstrap-extra-files**: Injects additional workspace bootstrap files from configured glob/path patterns during `agent:bootstrap`
*   **📝 command-logger**: Logs all command events to `~/.openclaw/logs/commands.log`
*   **🚀 boot-md**: Runs `BOOT.md` when the gateway starts (requires internal hooks enabled)

List available hooks:

```
openclaw hooks list
```

Enable a hook:

```
openclaw hooks enable session-memory
```

Check hook status:

```
openclaw hooks check
```

Get detailed information:

```
openclaw hooks info session-memory
```

### [​](https://docs.openclaw.ai/automation/hooks#onboarding)

Onboarding

During onboarding (`openclaw onboard`), you’ll be prompted to enable recommended hooks. The wizard automatically discovers eligible hooks and presents them for selection.
### [​](https://docs.openclaw.ai/automation/hooks#trust-boundary)

Trust Boundary

Hooks run inside the Gateway process. Treat bundled hooks, managed hooks, and `hooks.internal.load.extraDirs` as trusted local code. Workspace hooks under `<workspace>/hooks/` are repo-local code, so OpenClaw requires an explicit enable step before loading them.
## [​](https://docs.openclaw.ai/automation/hooks#hook-discovery)

Hook Discovery

Hooks are automatically discovered from these directories, in order of increasing override precedence:
1.   **Bundled hooks**: shipped with OpenClaw; located at `<openclaw>/dist/hooks/bundled/` for npm installs (or a sibling `hooks/bundled/` for compiled binaries)
2.   **Plugin hooks**: hooks bundled inside installed plugins (see [Plugin hooks](https://docs.openclaw.ai/plugins/architecture#provider-runtime-hooks))
3.   **Managed hooks**: `~/.openclaw/hooks/` (user-installed, shared across workspaces; can override bundled and plugin hooks). **Extra hook directories** configured via `hooks.internal.load.extraDirs` are also treated as managed hooks and share the same override precedence.
4.   **Workspace hooks**: `<workspace>/hooks/` (per-agent, disabled by default until explicitly enabled; cannot override hooks from other sources)

Workspace hooks can add new hook names for a repo, but they cannot override bundled, managed, or plugin-provided hooks with the same name.Managed hook directories can be either a **single hook** or a **hook pack** (package directory).Each hook is a directory containing:

```
my-hook/
├── HOOK.md          # Metadata + documentation
└── handler.ts       # Handler implementation
```

## [​](https://docs.openclaw.ai/automation/hooks#hook-packs-npm/archives)

Hook Packs (npm/archives)

Hook packs are standard npm packages that export one or more hooks via `openclaw.hooks` in `package.json`. Install them with:

```
openclaw plugins install <path-or-spec>
```

Npm specs are registry-only (package name + optional exact version or dist-tag). Git/URL/file specs and semver ranges are rejected.Bare specs and `@latest` stay on the stable track. If npm resolves either of those to a prerelease, OpenClaw stops and asks you to opt in explicitly with a prerelease tag such as `@beta`/`@rc` or an exact prerelease version.Example `package.json`:

```
{
  "name": "@acme/my-hooks",
  "version": "0.1.0",
  "openclaw": {
    "hooks": ["./hooks/my-hook", "./hooks/other-hook"]
  }
}
```

Each entry points to a hook directory containing `HOOK.md` and `handler.ts` (or `index.ts`). Hook packs can ship dependencies; they will be installed under `~/.openclaw/hooks/<id>`. Each `openclaw.hooks` entry must stay inside the package directory after symlink resolution; entries that escape are rejected.Security note: `openclaw plugins install` installs hook-pack dependencies with `npm install --ignore-scripts` (no lifecycle scripts). Keep hook pack dependency trees “pure JS/TS” and avoid packages that rely on `postinstall` builds.
## [​](https://docs.openclaw.ai/automation/hooks#hook-structure)

Hook Structure

### [​](https://docs.openclaw.ai/automation/hooks#hook-md-format)

HOOK.md Format

The `HOOK.md` file contains metadata in YAML frontmatter plus Markdown documentation:

```
---
name: my-hook
description: "Short description of what this hook does"
homepage: https://docs.openclaw.ai/automation/hooks#my-hook
metadata:
  { "openclaw": { "emoji": "🔗", "events": ["command:new"], "requires": { "bins": ["node"] } } }
---

# My Hook

Detailed documentation goes here...

## What It Does

- Listens for `/new` commands
- Performs some action
- Logs the result

## Requirements

- Node.js must be installed

## Configuration

No configuration needed.
```

### [​](https://docs.openclaw.ai/automation/hooks#metadata-fields)

Metadata Fields

The `metadata.openclaw` object supports:
*   **`emoji`**: Display emoji for CLI (e.g., `"💾"`)
*   **`events`**: Array of events to listen for (e.g., `["command:new", "command:reset"]`)
*   **`export`**: Named export to use (defaults to `"default"`)
*   **`homepage`**: Documentation URL
*   **`os`**: Required platforms (e.g., `["darwin", "linux"]`)
*   **`requires`**: Optional requirements
    *   **`bins`**: Required binaries on PATH (e.g., `["git", "node"]`)
    *   **`anyBins`**: At least one of these binaries must be present
    *   **`env`**: Required environment variables
    *   **`config`**: Required config paths (e.g., `["workspace.dir"]`)

*   **`always`**: Bypass eligibility checks (boolean)
*   **`install`**: Installation methods (for bundled hooks: `[{"id":"bundled","kind":"bundled"}]`)

### [​](https://docs.openclaw.ai/automation/hooks#handler-implementation)

Handler Implementation

The `handler.ts` file exports a `HookHandler` function:

```
const myHandler = async (event) => {
  // Only trigger on 'new' command
  if (event.type !== "command" || event.action !== "new") {
    return;
  }

  console.log(`[my-hook] New command triggered`);
  console.log(`  Session: ${event.sessionKey}`);
  console.log(`  Timestamp: ${event.timestamp.toISOString()}`);

  // Your custom logic here

  // Optionally send message to user
  event.messages.push("✨ My hook executed!");
};

export default myHandler;
```

#### [​](https://docs.openclaw.ai/automation/hooks#event-context)

Event Context

Each event includes:

```
{
  type: 'command' | 'session' | 'agent' | 'gateway' | 'message',
  action: string,              // e.g., 'new', 'reset', 'stop', 'received', 'sent'
  sessionKey: string,          // Session identifier
  timestamp: Date,             // When the event occurred
  messages: string[],          // Push messages here to send to user
  context: {
    // Command events (command:new, command:reset):
    sessionEntry?: SessionEntry,       // current session entry
    previousSessionEntry?: SessionEntry, // pre-reset entry (preferred for session-memory)
    commandSource?: string,            // e.g., 'whatsapp', 'telegram'
    senderId?: string,
    workspaceDir?: string,
    cfg?: OpenClawConfig,
    // Command events (command:stop only):
    sessionId?: string,
    // Agent bootstrap events (agent:bootstrap):
    bootstrapFiles?: WorkspaceBootstrapFile[],
    // Message events (see Message Events section for full details):
    from?: string,             // message:received
    to?: string,               // message:sent
    content?: string,
    channelId?: string,
    success?: boolean,         // message:sent
  }
}
```

## [​](https://docs.openclaw.ai/automation/hooks#event-types)

Event Types

### [​](https://docs.openclaw.ai/automation/hooks#command-events)

Command Events

Triggered when agent commands are issued:
*   **`command`**: All command events (general listener)
*   **`command:new`**: When `/new` command is issued
*   **`command:reset`**: When `/reset` command is issued
*   **`command:stop`**: When `/stop` command is issued

### [​](https://docs.openclaw.ai/automation/hooks#session-events)

Session Events

*   **`session:compact:before`**: Right before compaction summarizes history
*   **`session:compact:after`**: After compaction completes with summary metadata

Internal hook payloads emit these as `type: "session"` with `action: "compact:before"` / `action: "compact:after"`; listeners subscribe with the combined keys above. Specific handler registration uses the literal key format `${type}:${action}`. For these events, register `session:compact:before` and `session:compact:after`.
### [​](https://docs.openclaw.ai/automation/hooks#agent-events)

Agent Events

*   **`agent:bootstrap`**: Before workspace bootstrap files are injected (hooks may mutate `context.bootstrapFiles`)

### [​](https://docs.openclaw.ai/automation/hooks#gateway-events)

Gateway Events

Triggered when the gateway starts:
*   **`gateway:startup`**: After channels start and hooks are loaded

### [​](https://docs.openclaw.ai/automation/hooks#session-patch-events)

Session Patch Events

Triggered when session properties are modified:
*   **`session:patch`**: When a session is updated

#### [​](https://docs.openclaw.ai/automation/hooks#session-event-context)

Session Event Context

Session events include rich context about the session and changes:

```
{
  sessionEntry: SessionEntry, // The complete updated session entry
  patch: {                    // The patch object (only changed fields)
    // Session identity & labeling
    label?: string | null,           // Human-readable session label

    // AI model configuration
    model?: string | null,           // Model override (e.g., "claude-opus-4-5")
    thinkingLevel?: string | null,   // Thinking level ("off"|"low"|"med"|"high")
    verboseLevel?: string | null,    // Verbose output level
    reasoningLevel?: string | null,  // Reasoning mode override
    elevatedLevel?: string | null,   // Elevated mode override
    responseUsage?: "off" | "tokens" | "full" | null, // Usage display mode

    // Tool execution settings
    execHost?: string | null,        // Exec host (sandbox|gateway|node)
    execSecurity?: string | null,    // Security mode (deny|allowlist|full)
    execAsk?: string | null,         // Approval mode (off|on-miss|always)
    execNode?: string | null,        // Node ID for host=node

    // Subagent coordination
    spawnedBy?: string | null,       // Parent session key (for subagents)
    spawnDepth?: number | null,      // Nesting depth (0 = root)

    // Communication policies
    sendPolicy?: "allow" | "deny" | null,          // Message send policy
    groupActivation?: "mention" | "always" | null, // Group chat activation
  },
  cfg: OpenClawConfig            // Current gateway config
}
```

**Security note:** Only privileged clients (including the Control UI) can trigger `session:patch` events. Standard WebChat clients are blocked from patching sessions (see PR #20800), so the hook will not fire from those connections.See `SessionsPatchParamsSchema` in `src/gateway/protocol/schema/sessions.ts` for the complete type definition.
#### [​](https://docs.openclaw.ai/automation/hooks#example-session-patch-logger-hook)

Example: Session Patch Logger Hook

```
const handler = async (event) => {
  if (event.type !== "session" || event.action !== "patch") {
    return;
  }
  const { patch } = event.context;
  console.log(`[session-patch] Session updated: ${event.sessionKey}`);
  console.log(`[session-patch] Changes:`, patch);
};

export default handler;
```

### [​](https://docs.openclaw.ai/automation/hooks#message-events)

Message Events

Triggered when messages are received or sent:
*   **`message`**: All message events (general listener)
*   **`message:received`**: When an inbound message is received from any channel. Fires early in processing before media understanding. Content may contain raw placeholders like `<media:audio>` for media attachments that haven’t been processed yet.
*   **`message:transcribed`**: When a message has been fully processed, including audio transcription and link understanding. At this point, `transcript` contains the full transcript text for audio messages. Use this hook when you need access to transcribed audio content.
*   **`message:preprocessed`**: Fires for every message after all media + link understanding completes, giving hooks access to the fully enriched body (transcripts, image descriptions, link summaries) before the agent sees it.
*   **`message:sent`**: When an outbound message is successfully sent

#### [​](https://docs.openclaw.ai/automation/hooks#message-event-context)

Message Event Context

Message events include rich context about the message:

```
// message:received context
{
  from: string,           // Sender identifier (phone number, user ID, etc.)
  content: string,        // Message content
  timestamp?: number,     // Unix timestamp when received
  channelId: string,      // Channel (e.g., "whatsapp", "telegram", "discord")
  accountId?: string,     // Provider account ID for multi-account setups
  conversationId?: string, // Chat/conversation ID
  messageId?: string,     // Message ID from the provider
  metadata?: {            // Additional provider-specific data
    to?: string,
    provider?: string,
    surface?: string,
    threadId?: string | number,
    senderId?: string,
    senderName?: string,
    senderUsername?: string,
    senderE164?: string,
    guildId?: string,     // Discord guild / server ID
    channelName?: string, // Channel name (e.g., Discord channel name)
  }
}

// message:sent context
{
  to: string,             // Recipient identifier
  content: string,        // Message content that was sent
  success: boolean,       // Whether the send succeeded
  error?: string,         // Error message if sending failed
  channelId: string,      // Channel (e.g., "whatsapp", "telegram", "discord")
  accountId?: string,     // Provider account ID
  conversationId?: string, // Chat/conversation ID
  messageId?: string,     // Message ID returned by the provider
  isGroup?: boolean,      // Whether this outbound message belongs to a group/channel context
  groupId?: string,       // Group/channel identifier for correlation with message:received
}

// message:transcribed context
{
  from?: string,          // Sender identifier
  to?: string,            // Recipient identifier
  body?: string,          // Raw inbound body before enrichment
  bodyForAgent?: string,  // Enriched body visible to the agent
  transcript: string,     // Audio transcript text
  timestamp?: number,     // Unix timestamp when received
  channelId: string,      // Channel (e.g., "telegram", "whatsapp")
  conversationId?: string,
  messageId?: string,
  senderId?: string,      // Sender user ID
  senderName?: string,    // Sender display name
  senderUsername?: string,
  provider?: string,      // Provider name
  surface?: string,       // Surface name
  mediaPath?: string,     // Path to the media file that was transcribed
  mediaType?: string,     // MIME type of the media
}

// message:preprocessed context
{
  from?: string,          // Sender identifier
  to?: string,            // Recipient identifier
  body?: string,          // Raw inbound body
  bodyForAgent?: string,  // Final enriched body after media/link understanding
  transcript?: string,    // Transcript when audio was present
  timestamp?: number,     // Unix timestamp when received
  channelId: string,      // Channel (e.g., "telegram", "whatsapp")
  conversationId?: string,
  messageId?: string,
  senderId?: string,      // Sender user ID
  senderName?: string,    // Sender display name
  senderUsername?: string,
  provider?: string,      // Provider name
  surface?: string,       // Surface name
  mediaPath?: string,     // Path to the media file
  mediaType?: string,     // MIME type of the media
  isGroup?: boolean,
  groupId?: string,
}
```

#### [​](https://docs.openclaw.ai/automation/hooks#example-message-logger-hook)

Example: Message Logger Hook

```
const isMessageReceivedEvent = (event: { type: string; action: string }) =>
  event.type === "message" && event.action === "received";
const isMessageSentEvent = (event: { type: string; action: string }) =>
  event.type === "message" && event.action === "sent";

const handler = async (event) => {
  if (isMessageReceivedEvent(event as { type: string; action: string })) {
    console.log(`[message-logger] Received from ${event.context.from}: ${event.context.content}`);
  } else if (isMessageSentEvent(event as { type: string; action: string })) {
    console.log(`[message-logger] Sent to ${event.context.to}: ${event.context.content}`);
  }
};

export default handler;
```

### [​](https://docs.openclaw.ai/automation/hooks#tool-result-hooks-plugin-api)

Tool Result Hooks (Plugin API)

These hooks are not event-stream listeners; they let plugins synchronously adjust tool results before OpenClaw persists them.
*   **`tool_result_persist`**: transform tool results before they are written to the session transcript. Must be synchronous; return the updated tool result payload or `undefined` to keep it as-is. See [Agent Loop](https://docs.openclaw.ai/concepts/agent-loop).

### [​](https://docs.openclaw.ai/automation/hooks#plugin-hook-events)

Plugin Hook Events

#### [​](https://docs.openclaw.ai/automation/hooks#before_tool_call)

before_tool_call

Runs before each tool call. Plugins can modify parameters, block the call, or request user approval.Return fields:
*   **`params`**: Override tool parameters (merged with original params)
*   **`block`**: Set to `true` to block the tool call
*   **`blockReason`**: Reason shown to the agent when blocked
*   **`requireApproval`**: Pause execution and wait for user approval via channels

The `requireApproval` field triggers native platform approval (Telegram buttons, Discord components, `/approve` command) instead of relying on the agent to cooperate:

```
{
  requireApproval: {
    title: "Sensitive operation",
    description: "This tool call modifies production data",
    severity: "warning",       // "info" | "warning" | "critical"
    timeoutMs: 120000,         // default: 120s
    timeoutBehavior: "deny",   // "allow" | "deny" (default)
    onResolution: async (decision) => {
      // Called after the user resolves: "allow-once", "allow-always", "deny", "timeout", or "cancelled"
    },
  }
}
```

The `onResolution` callback is invoked with the final decision string after the approval resolves, times out, or is cancelled. It runs in-process within the plugin (not sent to the gateway). Use it to persist decisions, update caches, or perform cleanup.The `pluginId` field is stamped automatically by the hook runner from the plugin registration. When multiple plugins return `requireApproval`, the first one (highest priority) wins.`block` takes precedence over `requireApproval`: if the merged hook result has both `block: true` and a `requireApproval` field, the tool call is blocked immediately without triggering the approval flow. This ensures a higher-priority plugin’s block cannot be overridden by a lower-priority plugin’s approval request.If the gateway is unavailable or does not support plugin approvals, the tool call falls back to a soft block using the `description` as the block reason.
#### [​](https://docs.openclaw.ai/automation/hooks#compaction-lifecycle)

Compaction lifecycle

Compaction lifecycle hooks exposed through the plugin hook runner:
*   **`before_compaction`**: Runs before compaction with count/token metadata
*   **`after_compaction`**: Runs after compaction with compaction summary metadata

### [​](https://docs.openclaw.ai/automation/hooks#future-events)

Future Events

Planned event types:
*   **`session:start`**: When a new session begins
*   **`session:end`**: When a session ends
*   **`agent:error`**: When an agent encounters an error

## [​](https://docs.openclaw.ai/automation/hooks#creating-custom-hooks)

Creating Custom Hooks

### [​](https://docs.openclaw.ai/automation/hooks#1-choose-location)

1. Choose Location

*   **Workspace hooks** (`<workspace>/hooks/`): Per-agent; can add new hook names but cannot override bundled, managed, or plugin hooks with the same name
*   **Managed hooks** (`~/.openclaw/hooks/`): Shared across workspaces; can override bundled and plugin hooks

### [​](https://docs.openclaw.ai/automation/hooks#2-create-directory-structure)

2. Create Directory Structure

```
mkdir -p ~/.openclaw/hooks/my-hook
cd ~/.openclaw/hooks/my-hook
```

### [​](https://docs.openclaw.ai/automation/hooks#3-create-hook-md)

3. Create HOOK.md

```
---
name: my-hook
description: "Does something useful"
metadata: { "openclaw": { "emoji": "🎯", "events": ["command:new"] } }
---

# My Custom Hook

This hook does something useful when you issue `/new`.
```

### [​](https://docs.openclaw.ai/automation/hooks#4-create-handler-ts)

4. Create handler.ts

```
const handler = async (event) => {
  if (event.type !== "command" || event.action !== "new") {
    return;
  }

  console.log("[my-hook] Running!");
  // Your logic here
};

export default handler;
```

### [​](https://docs.openclaw.ai/automation/hooks#5-enable-and-test)

5. Enable and Test

```
# Verify hook is discovered
openclaw hooks list

# Enable it
openclaw hooks enable my-hook

# Restart your gateway process (menu bar app restart on macOS, or restart your dev process)

# Trigger the event
# Send /new via your messaging channel
```

## [​](https://docs.openclaw.ai/automation/hooks#configuration)

Configuration

### [​](https://docs.openclaw.ai/automation/hooks#new-config-format-recommended)

New Config Format (Recommended)

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "session-memory": { "enabled": true },
        "command-logger": { "enabled": false }
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/automation/hooks#per-hook-configuration)

Per-Hook Configuration

Hooks can have custom configuration:

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "my-hook": {
          "enabled": true,
          "env": {
            "MY_CUSTOM_VAR": "value"
          }
        }
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/automation/hooks#extra-directories)

Extra Directories

Load hooks from additional directories (treated as managed hooks, same override precedence):

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "load": {
        "extraDirs": ["/path/to/more/hooks"]
      }
    }
  }
}
```

### [​](https://docs.openclaw.ai/automation/hooks#legacy-config-format-still-supported)

Legacy Config Format (Still Supported)

The old config format still works for backwards compatibility:

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "handlers": [
        {
          "event": "command:new",
          "module": "./hooks/handlers/my-handler.ts",
          "export": "default"
        }
      ]
    }
  }
}
```

Note: `module` must be a workspace-relative path. Absolute paths and traversal outside the workspace are rejected.**Migration**: Use the new discovery-based system for new hooks. Legacy handlers are loaded after directory-based hooks.
## [​](https://docs.openclaw.ai/automation/hooks#cli-commands)

CLI Commands

### [​](https://docs.openclaw.ai/automation/hooks#list-hooks)

List Hooks

```
# List all hooks
openclaw hooks list

# Show only eligible hooks
openclaw hooks list --eligible

# Verbose output (show missing requirements)
openclaw hooks list --verbose

# JSON output
openclaw hooks list --json
```

### [​](https://docs.openclaw.ai/automation/hooks#hook-information)

Hook Information

```
# Show detailed info about a hook
openclaw hooks info session-memory

# JSON output
openclaw hooks info session-memory --json
```

### [​](https://docs.openclaw.ai/automation/hooks#check-eligibility)

Check Eligibility

```
# Show eligibility summary
openclaw hooks check

# JSON output
openclaw hooks check --json
```

### [​](https://docs.openclaw.ai/automation/hooks#enable/disable)

Enable/Disable

```
# Enable a hook
openclaw hooks enable session-memory

# Disable a hook
openclaw hooks disable command-logger
```

## [​](https://docs.openclaw.ai/automation/hooks#bundled-hook-reference)

Bundled hook reference

### [​](https://docs.openclaw.ai/automation/hooks#session-memory)

session-memory

Saves session context to memory when you issue `/new` or `/reset`.**Events**: `command:new`, `command:reset`**Requirements**: `workspace.dir` must be configured**Output**: `<workspace>/memory/YYYY-MM-DD-slug.md` (defaults to `~/.openclaw/workspace`)**What it does**:
1.   Uses the pre-reset session entry to locate the correct transcript
2.   Extracts the last 15 user/assistant messages from the conversation (configurable)
3.   Uses LLM to generate a descriptive filename slug
4.   Saves session metadata to a dated memory file

**Example output**:

```
# Session: 2026-01-16 14:30:00 UTC

- **Session Key**: agent:main:main
- **Session ID**: abc123def456
- **Source**: telegram

## Conversation Summary

user: Can you help me design the API?
assistant: Sure! Let's start with the endpoints...
```

**Filename examples**:
*   `2026-01-16-vendor-pitch.md`
*   `2026-01-16-api-design.md`
*   `2026-01-16-1430.md` (fallback timestamp if slug generation fails)

**Enable**:

```
openclaw hooks enable session-memory
```

### [​](https://docs.openclaw.ai/automation/hooks#bootstrap-extra-files)

bootstrap-extra-files

Injects additional bootstrap files (for example monorepo-local `AGENTS.md` / `TOOLS.md`) during `agent:bootstrap`.**Events**: `agent:bootstrap`**Requirements**: `workspace.dir` must be configured**Output**: No files written; bootstrap context is modified in-memory only.**Config**:

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "bootstrap-extra-files": {
          "enabled": true,
          "paths": ["packages/*/AGENTS.md", "packages/*/TOOLS.md"]
        }
      }
    }
  }
}
```

**Config options**:
*   `paths` (string[]): glob/path patterns to resolve from the workspace.
*   `patterns` (string[]): alias of `paths`.
*   `files` (string[]): alias of `paths`.

**Notes**:
*   Paths are resolved relative to workspace.
*   Files must stay inside workspace (realpath-checked).
*   Only recognized bootstrap basenames are loaded (`AGENTS.md`, `SOUL.md`, `TOOLS.md`, `IDENTITY.md`, `USER.md`, `HEARTBEAT.md`, `BOOTSTRAP.md`, `MEMORY.md`, `memory.md`).
*   For subagent/cron sessions a narrower allowlist applies (`AGENTS.md`, `TOOLS.md`, `SOUL.md`, `IDENTITY.md`, `USER.md`).

**Enable**:

```
openclaw hooks enable bootstrap-extra-files
```

### [​](https://docs.openclaw.ai/automation/hooks#command-logger)

command-logger

Logs all command events to a centralized audit file.**Events**: `command`**Requirements**: None**Output**: `~/.openclaw/logs/commands.log`**What it does**:
1.   Captures event details (command action, timestamp, session key, sender ID, source)
2.   Appends to log file in JSONL format
3.   Runs silently in the background

**Example log entries**:

```
{"timestamp":"2026-01-16T14:30:00.000Z","action":"new","sessionKey":"agent:main:main","senderId":"+1234567890","source":"telegram"}
{"timestamp":"2026-01-16T15:45:22.000Z","action":"stop","sessionKey":"agent:main:main","senderId":"user@example.com","source":"whatsapp"}
```

**View logs**:

```
# View recent commands
tail -n 20 ~/.openclaw/logs/commands.log

# Pretty-print with jq
cat ~/.openclaw/logs/commands.log | jq .

# Filter by action
grep '"action":"new"' ~/.openclaw/logs/commands.log | jq .
```

**Enable**:

```
openclaw hooks enable command-logger
```

### [​](https://docs.openclaw.ai/automation/hooks#boot-md)

boot-md

Runs `BOOT.md` when the gateway starts (after channels start). Internal hooks must be enabled for this to run.**Events**: `gateway:startup`**Requirements**: `workspace.dir` must be configured**What it does**:
1.   Reads `BOOT.md` from your workspace
2.   Runs the instructions via the agent runner
3.   Sends any requested outbound messages via the message tool

**Enable**:

```
openclaw hooks enable boot-md
```

## [​](https://docs.openclaw.ai/automation/hooks#best-practices)

Best Practices

### [​](https://docs.openclaw.ai/automation/hooks#keep-handlers-fast)

Keep Handlers Fast

Hooks run during command processing. Keep them lightweight:

```
// ✓ Good - async work, returns immediately
const handler: HookHandler = async (event) => {
  void processInBackground(event); // Fire and forget
};

// ✗ Bad - blocks command processing
const handler: HookHandler = async (event) => {
  await slowDatabaseQuery(event);
  await evenSlowerAPICall(event);
};
```

### [​](https://docs.openclaw.ai/automation/hooks#handle-errors-gracefully)

Handle Errors Gracefully

Always wrap risky operations:

```
const handler: HookHandler = async (event) => {
  try {
    await riskyOperation(event);
  } catch (err) {
    console.error("[my-handler] Failed:", err instanceof Error ? err.message : String(err));
    // Don't throw - let other handlers run
  }
};
```

### [​](https://docs.openclaw.ai/automation/hooks#filter-events-early)

Filter Events Early

Return early if the event isn’t relevant:

```
const handler: HookHandler = async (event) => {
  // Only handle 'new' commands
  if (event.type !== "command" || event.action !== "new") {
    return;
  }

  // Your logic here
};
```

### [​](https://docs.openclaw.ai/automation/hooks#use-specific-event-keys)

Use Specific Event Keys

Specify exact events in metadata when possible:

```
metadata: { "openclaw": { "events": ["command:new"] } } # Specific
```

Rather than:

```
metadata: { "openclaw": { "events": ["command"] } } # General - more overhead
```

## [​](https://docs.openclaw.ai/automation/hooks#debugging)

Debugging

### [​](https://docs.openclaw.ai/automation/hooks#enable-hook-logging)

Enable Hook Logging

The gateway logs hook loading at startup:

```
Registered hook: session-memory -> command:new
Registered hook: bootstrap-extra-files -> agent:bootstrap
Registered hook: command-logger -> command
Registered hook: boot-md -> gateway:startup
```

### [​](https://docs.openclaw.ai/automation/hooks#check-discovery)

Check Discovery

List all discovered hooks:

```
openclaw hooks list --verbose
```

### [​](https://docs.openclaw.ai/automation/hooks#check-registration)

Check Registration

In your handler, log when it’s called:

```
const handler: HookHandler = async (event) => {
  console.log("[my-handler] Triggered:", event.type, event.action);
  // Your logic
};
```

### [​](https://docs.openclaw.ai/automation/hooks#verify-eligibility)

Verify Eligibility

Check why a hook isn’t eligible:

```
openclaw hooks info my-hook
```

Look for missing requirements in the output.
## [​](https://docs.openclaw.ai/automation/hooks#testing)

Testing

### [​](https://docs.openclaw.ai/automation/hooks#gateway-logs)

Gateway Logs

Monitor gateway logs to see hook execution:

```
# macOS
./scripts/clawlog.sh -f

# Other platforms
tail -f ~/.openclaw/gateway.log
```

### [​](https://docs.openclaw.ai/automation/hooks#test-hooks-directly)

Test Hooks Directly

Test your handlers in isolation:

```
import { test } from "vitest";
import myHandler from "./hooks/my-hook/handler.js";

test("my handler works", async () => {
  const event = {
    type: "command",
    action: "new",
    sessionKey: "test-session",
    timestamp: new Date(),
    messages: [],
    context: { foo: "bar" },
  };

  await myHandler(event);

  // Assert side effects
});
```

## [​](https://docs.openclaw.ai/automation/hooks#architecture)

Architecture

### [​](https://docs.openclaw.ai/automation/hooks#core-components)

Core Components

*   **`src/hooks/types.ts`**: Type definitions
*   **`src/hooks/workspace.ts`**: Directory scanning and loading
*   **`src/hooks/frontmatter.ts`**: HOOK.md metadata parsing
*   **`src/hooks/config.ts`**: Eligibility checking
*   **`src/hooks/hooks-status.ts`**: Status reporting
*   **`src/hooks/loader.ts`**: Dynamic module loader
*   **`src/cli/hooks-cli.ts`**: CLI commands
*   **`src/gateway/server-startup.ts`**: Loads hooks at gateway start
*   **`src/auto-reply/reply/commands-core.ts`**: Triggers command events

### [​](https://docs.openclaw.ai/automation/hooks#discovery-flow)

Discovery Flow

```
Gateway startup
    ↓
Scan directories (bundled → plugin → managed + extra dirs → workspace)
    ↓
Parse HOOK.md files
    ↓
Sort by override precedence (bundled < plugin < managed < workspace)
    ↓
Check eligibility (bins, env, config, os)
    ↓
Load handlers from eligible hooks
    ↓
Register handlers for events
```

### [​](https://docs.openclaw.ai/automation/hooks#event-flow)

Event Flow

```
User sends /new
    ↓
Command validation
    ↓
Create hook event
    ↓
Trigger hook (all registered handlers)
    ↓
Command processing continues
    ↓
Session reset
```

## [​](https://docs.openclaw.ai/automation/hooks#troubleshooting)

Troubleshooting

### [​](https://docs.openclaw.ai/automation/hooks#hook-not-discovered)

Hook Not Discovered

1.   Check directory structure:  ```
ls -la ~/.openclaw/hooks/my-hook/
# Should show: HOOK.md, handler.ts
```    
2.   Verify HOOK.md format:  ```
cat ~/.openclaw/hooks/my-hook/HOOK.md
# Should have YAML frontmatter with name and metadata
```    
3.   List all discovered hooks:  ```
openclaw hooks list
```    

### [​](https://docs.openclaw.ai/automation/hooks#hook-not-eligible)

Hook Not Eligible

Check requirements:

```
openclaw hooks info my-hook
```

Look for missing:
*   Binaries (check PATH)
*   Environment variables
*   Config values
*   OS compatibility

### [​](https://docs.openclaw.ai/automation/hooks#hook-not-executing)

Hook Not Executing

1.   Verify hook is enabled:  ```
openclaw hooks list
# Should show ✓ next to enabled hooks
```    
2.   Restart your gateway process so hooks reload.
3.   Check gateway logs for errors:  ```
./scripts/clawlog.sh | grep hook
```    

### [​](https://docs.openclaw.ai/automation/hooks#handler-errors)

Handler Errors

Check for TypeScript/import errors:

```
# Test import directly
node -e "import('./path/to/handler.ts').then(console.log)"
```

## [​](https://docs.openclaw.ai/automation/hooks#migration-guide)

Migration Guide

### [​](https://docs.openclaw.ai/automation/hooks#from-legacy-config-to-discovery)

From Legacy Config to Discovery

**Before**:

```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "handlers": [
        {
          "event": "command:new",
          "module": "./hooks/handlers/my-handler.ts"
        }
      ]
    }
  }
}
```

**After**:
1.   Create hook directory:  ```
mkdir -p ~/.openclaw/hooks/my-hook
mv ./hooks/handlers/my-handler.ts ~/.openclaw/hooks/my-hook/handler.ts
```    
2.   Create HOOK.md:  ```
---
name: my-hook
description: "My custom hook"
metadata: { "openclaw": { "emoji": "🎯", "events": ["command:new"] } }
---

# My Hook

Does something useful.
```    
3.   Update config:  ```
{
  "hooks": {
    "internal": {
      "enabled": true,
      "entries": {
        "my-hook": { "enabled": true }
      }
    }
  }
}
```    
4.   Verify and restart your gateway process:  ```
openclaw hooks list
# Should show: 🎯 my-hook ✓
```    

**Benefits of migration**:
*   Automatic discovery
*   CLI management
*   Eligibility checking
*   Better documentation
*   Consistent structure

## [​](https://docs.openclaw.ai/automation/hooks#see-also)

See Also

*   [CLI Reference: hooks](https://docs.openclaw.ai/cli/hooks)
*   [Bundled Hooks README](https://github.com/openclaw/openclaw/tree/main/src/hooks/bundled)
*   [Webhook Hooks](https://docs.openclaw.ai/automation/webhook)
*   [Configuration](https://docs.openclaw.ai/gateway/configuration-reference#hooks)

[OpenProse](https://docs.openclaw.ai/prose)[Standing Orders](https://docs.openclaw.ai/automation/standing-orders)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
