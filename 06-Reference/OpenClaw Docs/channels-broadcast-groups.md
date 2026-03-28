# channels/broadcast-groups

Source: https://docs.openclaw.ai/channels/broadcast-groups

Title: Broadcast Groups - OpenClaw

URL Source: https://docs.openclaw.ai/channels/broadcast-groups

Markdown Content:
# Broadcast Groups - OpenClaw

[Skip to main content](https://docs.openclaw.ai/channels/broadcast-groups#content-area)

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

Broadcast Groups

[Get started](https://docs.openclaw.ai/)[Install](https://docs.openclaw.ai/install)[Channels](https://docs.openclaw.ai/channels)[Agents](https://docs.openclaw.ai/concepts/architecture)[Tools & Plugins](https://docs.openclaw.ai/tools)[Models](https://docs.openclaw.ai/providers)[Platforms](https://docs.openclaw.ai/platforms)[Gateway & Ops](https://docs.openclaw.ai/gateway)[Reference](https://docs.openclaw.ai/cli)[Help](https://docs.openclaw.ai/help)

##### Overview

*   [Chat Channels](https://docs.openclaw.ai/channels)

##### Messaging platforms

*   [BlueBubbles](https://docs.openclaw.ai/channels/bluebubbles)
*   [Discord](https://docs.openclaw.ai/channels/discord)
*   [Feishu](https://docs.openclaw.ai/channels/feishu)
*   [Google Chat](https://docs.openclaw.ai/channels/googlechat)
*   [iMessage](https://docs.openclaw.ai/channels/imessage)
*   [IRC](https://docs.openclaw.ai/channels/irc)
*   [LINE](https://docs.openclaw.ai/channels/line)
*   [Matrix](https://docs.openclaw.ai/channels/matrix)
*   [Mattermost](https://docs.openclaw.ai/channels/mattermost)
*   [Microsoft Teams](https://docs.openclaw.ai/channels/msteams)
*   [Nextcloud Talk](https://docs.openclaw.ai/channels/nextcloud-talk)
*   [Nostr](https://docs.openclaw.ai/channels/nostr)
*   [Signal](https://docs.openclaw.ai/channels/signal)
*   [Slack](https://docs.openclaw.ai/channels/slack)
*   [Synology Chat](https://docs.openclaw.ai/channels/synology-chat)
*   [Telegram](https://docs.openclaw.ai/channels/telegram)
*   [Tlon](https://docs.openclaw.ai/channels/tlon)
*   [Twitch](https://docs.openclaw.ai/channels/twitch)
*   [Voice Call Plugin](https://docs.openclaw.ai/plugins/voice-call)
*   [WhatsApp](https://docs.openclaw.ai/channels/whatsapp)
*   [Zalo](https://docs.openclaw.ai/channels/zalo)
*   [Zalo Personal](https://docs.openclaw.ai/channels/zalouser)

##### Configuration

*   [Pairing](https://docs.openclaw.ai/channels/pairing)
*   [Group Messages](https://docs.openclaw.ai/channels/group-messages)
*   [Groups](https://docs.openclaw.ai/channels/groups)
*   [Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups)
*   [Channel Routing](https://docs.openclaw.ai/channels/channel-routing)
*   [Channel Location Parsing](https://docs.openclaw.ai/channels/location)
*   [Channel Troubleshooting](https://docs.openclaw.ai/channels/troubleshooting)

On this page

*   [Broadcast Groups](https://docs.openclaw.ai/channels/broadcast-groups#broadcast-groups)
*   [Overview](https://docs.openclaw.ai/channels/broadcast-groups#overview)
*   [Use Cases](https://docs.openclaw.ai/channels/broadcast-groups#use-cases)
*   [1. Specialized Agent Teams](https://docs.openclaw.ai/channels/broadcast-groups#1-specialized-agent-teams)
*   [2. Multi-Language Support](https://docs.openclaw.ai/channels/broadcast-groups#2-multi-language-support)
*   [3. Quality Assurance Workflows](https://docs.openclaw.ai/channels/broadcast-groups#3-quality-assurance-workflows)
*   [4. Task Automation](https://docs.openclaw.ai/channels/broadcast-groups#4-task-automation)
*   [Configuration](https://docs.openclaw.ai/channels/broadcast-groups#configuration)
*   [Basic Setup](https://docs.openclaw.ai/channels/broadcast-groups#basic-setup)
*   [Processing Strategy](https://docs.openclaw.ai/channels/broadcast-groups#processing-strategy)
*   [Parallel (Default)](https://docs.openclaw.ai/channels/broadcast-groups#parallel-default)
*   [Sequential](https://docs.openclaw.ai/channels/broadcast-groups#sequential)
*   [Complete Example](https://docs.openclaw.ai/channels/broadcast-groups#complete-example)
*   [How It Works](https://docs.openclaw.ai/channels/broadcast-groups#how-it-works)
*   [Message Flow](https://docs.openclaw.ai/channels/broadcast-groups#message-flow)
*   [Session Isolation](https://docs.openclaw.ai/channels/broadcast-groups#session-isolation)
*   [Example: Isolated Sessions](https://docs.openclaw.ai/channels/broadcast-groups#example-isolated-sessions)
*   [Best Practices](https://docs.openclaw.ai/channels/broadcast-groups#best-practices)
*   [1. Keep Agents Focused](https://docs.openclaw.ai/channels/broadcast-groups#1-keep-agents-focused)
*   [2. Use Descriptive Names](https://docs.openclaw.ai/channels/broadcast-groups#2-use-descriptive-names)
*   [3. Configure Different Tool Access](https://docs.openclaw.ai/channels/broadcast-groups#3-configure-different-tool-access)
*   [4. Monitor Performance](https://docs.openclaw.ai/channels/broadcast-groups#4-monitor-performance)
*   [5. Handle Failures Gracefully](https://docs.openclaw.ai/channels/broadcast-groups#5-handle-failures-gracefully)
*   [Compatibility](https://docs.openclaw.ai/channels/broadcast-groups#compatibility)
*   [Providers](https://docs.openclaw.ai/channels/broadcast-groups#providers)
*   [Routing](https://docs.openclaw.ai/channels/broadcast-groups#routing)
*   [Troubleshooting](https://docs.openclaw.ai/channels/broadcast-groups#troubleshooting)
*   [Agents Not Responding](https://docs.openclaw.ai/channels/broadcast-groups#agents-not-responding)
*   [Only One Agent Responding](https://docs.openclaw.ai/channels/broadcast-groups#only-one-agent-responding)
*   [Performance Issues](https://docs.openclaw.ai/channels/broadcast-groups#performance-issues)
*   [Examples](https://docs.openclaw.ai/channels/broadcast-groups#examples)
*   [Example 1: Code Review Team](https://docs.openclaw.ai/channels/broadcast-groups#example-1-code-review-team)
*   [Example 2: Multi-Language Support](https://docs.openclaw.ai/channels/broadcast-groups#example-2-multi-language-support)
*   [API Reference](https://docs.openclaw.ai/channels/broadcast-groups#api-reference)
*   [Config Schema](https://docs.openclaw.ai/channels/broadcast-groups#config-schema)
*   [Fields](https://docs.openclaw.ai/channels/broadcast-groups#fields)
*   [Limitations](https://docs.openclaw.ai/channels/broadcast-groups#limitations)
*   [Future Enhancements](https://docs.openclaw.ai/channels/broadcast-groups#future-enhancements)
*   [See Also](https://docs.openclaw.ai/channels/broadcast-groups#see-also)

Configuration

# Broadcast Groups

# [​](https://docs.openclaw.ai/channels/broadcast-groups#broadcast-groups)

Broadcast Groups

**Status:** Experimental

**Version:** Added in 2026.1.9
## [​](https://docs.openclaw.ai/channels/broadcast-groups#overview)

Overview

Broadcast Groups enable multiple agents to process and respond to the same message simultaneously. This allows you to create specialized agent teams that work together in a single WhatsApp group or DM — all using one phone number.Current scope: **WhatsApp only** (web channel).Broadcast groups are evaluated after channel allowlists and group activation rules. In WhatsApp groups, this means broadcasts happen when OpenClaw would normally reply (for example: on mention, depending on your group settings).
## [​](https://docs.openclaw.ai/channels/broadcast-groups#use-cases)

Use Cases

### [​](https://docs.openclaw.ai/channels/broadcast-groups#1-specialized-agent-teams)

1. Specialized Agent Teams

Deploy multiple agents with atomic, focused responsibilities:

```
Group: "Development Team"
Agents:
  - CodeReviewer (reviews code snippets)
  - DocumentationBot (generates docs)
  - SecurityAuditor (checks for vulnerabilities)
  - TestGenerator (suggests test cases)
```

Each agent processes the same message and provides its specialized perspective.
### [​](https://docs.openclaw.ai/channels/broadcast-groups#2-multi-language-support)

2. Multi-Language Support

```
Group: "International Support"
Agents:
  - Agent_EN (responds in English)
  - Agent_DE (responds in German)
  - Agent_ES (responds in Spanish)
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#3-quality-assurance-workflows)

3. Quality Assurance Workflows

```
Group: "Customer Support"
Agents:
  - SupportAgent (provides answer)
  - QAAgent (reviews quality, only responds if issues found)
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#4-task-automation)

4. Task Automation

```
Group: "Project Management"
Agents:
  - TaskTracker (updates task database)
  - TimeLogger (logs time spent)
  - ReportGenerator (creates summaries)
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups#configuration)

Configuration

### [​](https://docs.openclaw.ai/channels/broadcast-groups#basic-setup)

Basic Setup

Add a top-level `broadcast` section (next to `bindings`). Keys are WhatsApp peer ids:
*   group chats: group JID (e.g. `120363403215116621@g.us`)
*   DMs: E.164 phone number (e.g. `+15551234567`)

```
{
  "broadcast": {
    "120363403215116621@g.us": ["alfred", "baerbel", "assistant3"]
  }
}
```

**Result:** When OpenClaw would reply in this chat, it will run all three agents.
### [​](https://docs.openclaw.ai/channels/broadcast-groups#processing-strategy)

Processing Strategy

Control how agents process messages:
#### [​](https://docs.openclaw.ai/channels/broadcast-groups#parallel-default)

Parallel (Default)

All agents process simultaneously:

```
{
  "broadcast": {
    "strategy": "parallel",
    "120363403215116621@g.us": ["alfred", "baerbel"]
  }
}
```

#### [​](https://docs.openclaw.ai/channels/broadcast-groups#sequential)

Sequential

Agents process in order (one waits for previous to finish):

```
{
  "broadcast": {
    "strategy": "sequential",
    "120363403215116621@g.us": ["alfred", "baerbel"]
  }
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#complete-example)

Complete Example

```
{
  "agents": {
    "list": [
      {
        "id": "code-reviewer",
        "name": "Code Reviewer",
        "workspace": "/path/to/code-reviewer",
        "sandbox": { "mode": "all" }
      },
      {
        "id": "security-auditor",
        "name": "Security Auditor",
        "workspace": "/path/to/security-auditor",
        "sandbox": { "mode": "all" }
      },
      {
        "id": "docs-generator",
        "name": "Documentation Generator",
        "workspace": "/path/to/docs-generator",
        "sandbox": { "mode": "all" }
      }
    ]
  },
  "broadcast": {
    "strategy": "parallel",
    "120363403215116621@g.us": ["code-reviewer", "security-auditor", "docs-generator"],
    "120363424282127706@g.us": ["support-en", "support-de"],
    "+15555550123": ["assistant", "logger"]
  }
}
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups#how-it-works)

How It Works

### [​](https://docs.openclaw.ai/channels/broadcast-groups#message-flow)

Message Flow

1.   **Incoming message** arrives in a WhatsApp group
2.   **Broadcast check**: System checks if peer ID is in `broadcast`
3.   **If in broadcast list**:
    *   All listed agents process the message
    *   Each agent has its own session key and isolated context
    *   Agents process in parallel (default) or sequentially

4.   **If not in broadcast list**:
    *   Normal routing applies (first matching binding)

Note: broadcast groups do not bypass channel allowlists or group activation rules (mentions/commands/etc). They only change _which agents run_ when a message is eligible for processing.
### [​](https://docs.openclaw.ai/channels/broadcast-groups#session-isolation)

Session Isolation

Each agent in a broadcast group maintains completely separate:
*   **Session keys** (`agent:alfred:whatsapp:group:120363...` vs `agent:baerbel:whatsapp:group:120363...`)
*   **Conversation history** (agent doesn’t see other agents’ messages)
*   **Workspace** (separate sandboxes if configured)
*   **Tool access** (different allow/deny lists)
*   **Memory/context** (separate IDENTITY.md, SOUL.md, etc.)
*   **Group context buffer** (recent group messages used for context) is shared per peer, so all broadcast agents see the same context when triggered

This allows each agent to have:
*   Different personalities
*   Different tool access (e.g., read-only vs. read-write)
*   Different models (e.g., opus vs. sonnet)
*   Different skills installed

### [​](https://docs.openclaw.ai/channels/broadcast-groups#example-isolated-sessions)

Example: Isolated Sessions

In group `120363403215116621@g.us` with agents `["alfred", "baerbel"]`:**Alfred’s context:**

```
Session: agent:alfred:whatsapp:group:120363403215116621@g.us
History: [user message, alfred's previous responses]
Workspace: /Users/pascal/openclaw-alfred/
Tools: read, write, exec
```

**Bärbel’s context:**

```
Session: agent:baerbel:whatsapp:group:120363403215116621@g.us
History: [user message, baerbel's previous responses]
Workspace: /Users/pascal/openclaw-baerbel/
Tools: read only
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups#best-practices)

Best Practices

### [​](https://docs.openclaw.ai/channels/broadcast-groups#1-keep-agents-focused)

1. Keep Agents Focused

Design each agent with a single, clear responsibility:

```
{
  "broadcast": {
    "DEV_GROUP": ["formatter", "linter", "tester"]
  }
}
```

✅ **Good:** Each agent has one job

❌ **Bad:** One generic “dev-helper” agent
### [​](https://docs.openclaw.ai/channels/broadcast-groups#2-use-descriptive-names)

2. Use Descriptive Names

Make it clear what each agent does:

```
{
  "agents": {
    "security-scanner": { "name": "Security Scanner" },
    "code-formatter": { "name": "Code Formatter" },
    "test-generator": { "name": "Test Generator" }
  }
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#3-configure-different-tool-access)

3. Configure Different Tool Access

Give agents only the tools they need:

```
{
  "agents": {
    "reviewer": {
      "tools": { "allow": ["read", "exec"] } // Read-only
    },
    "fixer": {
      "tools": { "allow": ["read", "write", "edit", "exec"] } // Read-write
    }
  }
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#4-monitor-performance)

4. Monitor Performance

With many agents, consider:
*   Using `"strategy": "parallel"` (default) for speed
*   Limiting broadcast groups to 5-10 agents
*   Using faster models for simpler agents

### [​](https://docs.openclaw.ai/channels/broadcast-groups#5-handle-failures-gracefully)

5. Handle Failures Gracefully

Agents fail independently. One agent’s error doesn’t block others:

```
Message → [Agent A ✓, Agent B ✗ error, Agent C ✓]
Result: Agent A and C respond, Agent B logs error
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups#compatibility)

Compatibility

### [​](https://docs.openclaw.ai/channels/broadcast-groups#providers)

Providers

Broadcast groups currently work with:
*   ✅ WhatsApp (implemented)
*   🚧 Telegram (planned)
*   🚧 Discord (planned)
*   🚧 Slack (planned)

### [​](https://docs.openclaw.ai/channels/broadcast-groups#routing)

Routing

Broadcast groups work alongside existing routing:

```
{
  "bindings": [
    {
      "match": { "channel": "whatsapp", "peer": { "kind": "group", "id": "GROUP_A" } },
      "agentId": "alfred"
    }
  ],
  "broadcast": {
    "GROUP_B": ["agent1", "agent2"]
  }
}
```

*   `GROUP_A`: Only alfred responds (normal routing)
*   `GROUP_B`: agent1 AND agent2 respond (broadcast)

**Precedence:**`broadcast` takes priority over `bindings`.
## [​](https://docs.openclaw.ai/channels/broadcast-groups#troubleshooting)

Troubleshooting

### [​](https://docs.openclaw.ai/channels/broadcast-groups#agents-not-responding)

Agents Not Responding

**Check:**
1.   Agent IDs exist in `agents.list`
2.   Peer ID format is correct (e.g., `120363403215116621@g.us`)
3.   Agents are not in deny lists

**Debug:**

```
tail -f ~/.openclaw/logs/gateway.log | grep broadcast
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#only-one-agent-responding)

Only One Agent Responding

**Cause:** Peer ID might be in `bindings` but not `broadcast`.**Fix:** Add to broadcast config or remove from bindings.
### [​](https://docs.openclaw.ai/channels/broadcast-groups#performance-issues)

Performance Issues

**If slow with many agents:**
*   Reduce number of agents per group
*   Use lighter models (sonnet instead of opus)
*   Check sandbox startup time

## [​](https://docs.openclaw.ai/channels/broadcast-groups#examples)

Examples

### [​](https://docs.openclaw.ai/channels/broadcast-groups#example-1-code-review-team)

Example 1: Code Review Team

```
{
  "broadcast": {
    "strategy": "parallel",
    "120363403215116621@g.us": [
      "code-formatter",
      "security-scanner",
      "test-coverage",
      "docs-checker"
    ]
  },
  "agents": {
    "list": [
      {
        "id": "code-formatter",
        "workspace": "~/agents/formatter",
        "tools": { "allow": ["read", "write"] }
      },
      {
        "id": "security-scanner",
        "workspace": "~/agents/security",
        "tools": { "allow": ["read", "exec"] }
      },
      {
        "id": "test-coverage",
        "workspace": "~/agents/testing",
        "tools": { "allow": ["read", "exec"] }
      },
      { "id": "docs-checker", "workspace": "~/agents/docs", "tools": { "allow": ["read"] } }
    ]
  }
}
```

**User sends:** Code snippet

**Responses:**
*   code-formatter: “Fixed indentation and added type hints”
*   security-scanner: “⚠️ SQL injection vulnerability in line 12”
*   test-coverage: “Coverage is 45%, missing tests for error cases”
*   docs-checker: “Missing docstring for function `process_data`”

### [​](https://docs.openclaw.ai/channels/broadcast-groups#example-2-multi-language-support)

Example 2: Multi-Language Support

```
{
  "broadcast": {
    "strategy": "sequential",
    "+15555550123": ["detect-language", "translator-en", "translator-de"]
  },
  "agents": {
    "list": [
      { "id": "detect-language", "workspace": "~/agents/lang-detect" },
      { "id": "translator-en", "workspace": "~/agents/translate-en" },
      { "id": "translator-de", "workspace": "~/agents/translate-de" }
    ]
  }
}
```

## [​](https://docs.openclaw.ai/channels/broadcast-groups#api-reference)

API Reference

### [​](https://docs.openclaw.ai/channels/broadcast-groups#config-schema)

Config Schema

```
interface OpenClawConfig {
  broadcast?: {
    strategy?: "parallel" | "sequential";
    [peerId: string]: string[];
  };
}
```

### [​](https://docs.openclaw.ai/channels/broadcast-groups#fields)

Fields

*   `strategy` (optional): How to process agents
    *   `"parallel"` (default): All agents process simultaneously
    *   `"sequential"`: Agents process in array order

*   `[peerId]`: WhatsApp group JID, E.164 number, or other peer ID
    *   Value: Array of agent IDs that should process messages

## [​](https://docs.openclaw.ai/channels/broadcast-groups#limitations)

Limitations

1.   **Max agents:** No hard limit, but 10+ agents may be slow
2.   **Shared context:** Agents don’t see each other’s responses (by design)
3.   **Message ordering:** Parallel responses may arrive in any order
4.   **Rate limits:** All agents count toward WhatsApp rate limits

## [​](https://docs.openclaw.ai/channels/broadcast-groups#future-enhancements)

Future Enhancements

Planned features:
*   - [x] Shared context mode (agents see each other’s responses)
*   - [x] Agent coordination (agents can signal each other)
*   - [x] Dynamic agent selection (choose agents based on message content)
*   - [x] Agent priorities (some agents respond before others)

## [​](https://docs.openclaw.ai/channels/broadcast-groups#see-also)

See Also

*   [Multi-Agent Configuration](https://docs.openclaw.ai/tools/multi-agent-sandbox-tools)
*   [Routing Configuration](https://docs.openclaw.ai/channels/channel-routing)
*   [Session Management](https://docs.openclaw.ai/concepts/session)

[Groups](https://docs.openclaw.ai/channels/groups)[Channel Routing](https://docs.openclaw.ai/channels/channel-routing)

⌘I

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=clawdhub)
