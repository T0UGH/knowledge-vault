# authentication

Source: https://code.claude.com/docs/en/authentication

Title: Authentication - Claude Code Docs

URL Source: https://code.claude.com/docs/en/authentication

Markdown Content:
# Authentication - Claude Code Docs

[Skip to main content](https://code.claude.com/docs/en/authentication#content-area)

[Claude Code Docs home page![Image 1: light logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/light.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=78fd01ff4f4340295a4f66e2ea54903c)![Image 2: dark logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/dark.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=1298a0c3b3a1da603b190d0de0e31712)](https://code.claude.com/docs/en/overview)

![Image 3: US](https://d3gk2c5xim1je2.cloudfront.net/flags/US.svg)

English

Search...

⌘K Ask AI

*   [Claude Developer Platform](https://platform.claude.com/)
*   [Claude Code on the Web](https://claude.ai/code)
*   [Claude Code on the Web](https://claude.ai/code)

Search...

Navigation

Administration

Authentication

[Getting started](https://code.claude.com/docs/en/overview)[Build with Claude Code](https://code.claude.com/docs/en/sub-agents)[Deployment](https://code.claude.com/docs/en/third-party-integrations)[Administration](https://code.claude.com/docs/en/setup)[Configuration](https://code.claude.com/docs/en/settings)[Reference](https://code.claude.com/docs/en/cli-reference)[Resources](https://code.claude.com/docs/en/legal-and-compliance)

##### Administration

*   [Advanced setup](https://code.claude.com/docs/en/setup)
*   [Authentication](https://code.claude.com/docs/en/authentication)
*   [Security](https://code.claude.com/docs/en/security)
*   [Server-managed settings (beta)](https://code.claude.com/docs/en/server-managed-settings)
*   [Data usage](https://code.claude.com/docs/en/data-usage)
*   [Zero data retention](https://code.claude.com/docs/en/zero-data-retention)
*   [Monitoring](https://code.claude.com/docs/en/monitoring-usage)
*   [Costs](https://code.claude.com/docs/en/costs)
*   [Track team usage with analytics](https://code.claude.com/docs/en/analytics)
*   [Create and distribute a plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces)

On this page

*   [Log in to Claude Code](https://code.claude.com/docs/en/authentication#log-in-to-claude-code)
*   [Set up team authentication](https://code.claude.com/docs/en/authentication#set-up-team-authentication)
*   [Claude for Teams or Enterprise](https://code.claude.com/docs/en/authentication#claude-for-teams-or-enterprise)
*   [Claude Console authentication](https://code.claude.com/docs/en/authentication#claude-console-authentication)
*   [Cloud provider authentication](https://code.claude.com/docs/en/authentication#cloud-provider-authentication)
*   [Credential management](https://code.claude.com/docs/en/authentication#credential-management)
*   [Authentication precedence](https://code.claude.com/docs/en/authentication#authentication-precedence)

Administration

# Authentication

Copy page

Log in to Claude Code and configure authentication for individuals, teams, and organizations.

Copy page

Claude Code supports multiple authentication methods depending on your setup. Individual users can log in with a Claude.ai account, while teams can use Claude for Teams or Enterprise, the Claude Console, or a cloud provider like Amazon Bedrock, Google Vertex AI, or Microsoft Foundry.
## [​](https://code.claude.com/docs/en/authentication#log-in-to-claude-code)

Log in to Claude Code

After [installing Claude Code](https://code.claude.com/docs/en/setup#install-claude-code), run `claude` in your terminal. On first launch, Claude Code opens a browser window for you to log in.If the browser doesn’t open automatically, press `c` to copy the login URL to your clipboard, then paste it into your browser.You can authenticate with any of these account types:
*   **Claude Pro or Max subscription**: log in with your Claude.ai account. Subscribe at [claude.com/pricing](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=authentication_pro_max).
*   **Claude for Teams or Enterprise**: log in with the Claude.ai account your team admin invited you to.
*   **Claude Console**: log in with your Console credentials. Your admin must have [invited you](https://code.claude.com/docs/en/authentication#claude-console-authentication) first.
*   **Cloud providers**: if your organization uses [Amazon Bedrock](https://code.claude.com/docs/en/amazon-bedrock), [Google Vertex AI](https://code.claude.com/docs/en/google-vertex-ai), or [Microsoft Foundry](https://code.claude.com/docs/en/microsoft-foundry), set the required environment variables before running `claude`. No browser login is needed.

To log out and re-authenticate, type `/logout` at the Claude Code prompt.If you’re having trouble logging in, see [authentication troubleshooting](https://code.claude.com/docs/en/troubleshooting#authentication-issues).
## [​](https://code.claude.com/docs/en/authentication#set-up-team-authentication)

Set up team authentication

For teams and organizations, you can configure Claude Code access in one of these ways:
*   [Claude for Teams or Enterprise](https://code.claude.com/docs/en/authentication#claude-for-teams-or-enterprise), recommended for most teams
*   [Claude Console](https://code.claude.com/docs/en/authentication#claude-console-authentication)
*   [Amazon Bedrock](https://code.claude.com/docs/en/amazon-bedrock)
*   [Google Vertex AI](https://code.claude.com/docs/en/google-vertex-ai)
*   [Microsoft Foundry](https://code.claude.com/docs/en/microsoft-foundry)

### [​](https://code.claude.com/docs/en/authentication#claude-for-teams-or-enterprise)

Claude for Teams or Enterprise

[Claude for Teams](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=authentication_teams#team-&-enterprise) and [Claude for Enterprise](https://anthropic.com/contact-sales?utm_source=claude_code&utm_medium=docs&utm_content=authentication_enterprise) provide the best experience for organizations using Claude Code. Team members get access to both Claude Code and Claude on the web with centralized billing and team management.
*   **Claude for Teams**: self-service plan with collaboration features, admin tools, and billing management. Best for smaller teams.
*   **Claude for Enterprise**: adds SSO, domain capture, role-based permissions, compliance API, and managed policy settings for organization-wide Claude Code configurations. Best for larger organizations with security and compliance requirements.

1

[](https://code.claude.com/docs/en/authentication#)

Subscribe

Subscribe to [Claude for Teams](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=authentication_teams_step#team-&-enterprise) or contact sales for [Claude for Enterprise](https://anthropic.com/contact-sales?utm_source=claude_code&utm_medium=docs&utm_content=authentication_enterprise_step).

2

[](https://code.claude.com/docs/en/authentication#)

Invite team members

Invite team members from the admin dashboard.

3

[](https://code.claude.com/docs/en/authentication#)

Install and log in

Team members install Claude Code and log in with their Claude.ai accounts.

### [​](https://code.claude.com/docs/en/authentication#claude-console-authentication)

Claude Console authentication

For organizations that prefer API-based billing, you can set up access through the Claude Console.

1

[](https://code.claude.com/docs/en/authentication#)

Create or use a Console account

Use your existing Claude Console account or create a new one.

2

[](https://code.claude.com/docs/en/authentication#)

Add users

You can add users through either method:
*   Bulk invite users from within the Console: Settings -> Members -> Invite
*   [Set up SSO](https://support.claude.com/en/articles/13132885-setting-up-single-sign-on-sso)

3

[](https://code.claude.com/docs/en/authentication#)

Assign roles

When inviting users, assign one of:
*   **Claude Code** role: users can only create Claude Code API keys
*   **Developer** role: users can create any kind of API key

4

[](https://code.claude.com/docs/en/authentication#)

Users complete setup

Each invited user needs to:
*   Accept the Console invite
*   [Check system requirements](https://code.claude.com/docs/en/setup#system-requirements)
*   [Install Claude Code](https://code.claude.com/docs/en/setup#install-claude-code)
*   Log in with Console account credentials

### [​](https://code.claude.com/docs/en/authentication#cloud-provider-authentication)

Cloud provider authentication

For teams using Amazon Bedrock, Google Vertex AI, or Microsoft Foundry:

1

[](https://code.claude.com/docs/en/authentication#)

Follow provider setup

Follow the [Bedrock docs](https://code.claude.com/docs/en/amazon-bedrock), [Vertex docs](https://code.claude.com/docs/en/google-vertex-ai), or [Microsoft Foundry docs](https://code.claude.com/docs/en/microsoft-foundry).

2

[](https://code.claude.com/docs/en/authentication#)

Distribute configuration

Distribute the environment variables and instructions for generating cloud credentials to your users. Read more about how to [manage configuration here](https://code.claude.com/docs/en/settings).

3

[](https://code.claude.com/docs/en/authentication#)

Install Claude Code

Users can [install Claude Code](https://code.claude.com/docs/en/setup#install-claude-code).

## [​](https://code.claude.com/docs/en/authentication#credential-management)

Credential management

Claude Code securely manages your authentication credentials:
*   **Storage location**: on macOS, credentials are stored in the encrypted macOS Keychain. On Linux and Windows, credentials are stored in `~/.claude/.credentials.json`, or under `$CLAUDE_CONFIG_DIR` if that variable is set. On Linux, the file is written with mode `0600`; on Windows, it inherits the access controls of your user profile directory.
*   **Supported authentication types**: Claude.ai credentials, Claude API credentials, Azure Auth, Bedrock Auth, and Vertex Auth.
*   **Custom credential scripts**: the [`apiKeyHelper`](https://code.claude.com/docs/en/settings#available-settings) setting can be configured to run a shell script that returns an API key.
*   **Refresh intervals**: by default, `apiKeyHelper` is called after 5 minutes or on HTTP 401 response. Set `CLAUDE_CODE_API_KEY_HELPER_TTL_MS` environment variable for custom refresh intervals.
*   **Slow helper notice**: if `apiKeyHelper` takes longer than 10 seconds to return a key, Claude Code displays a warning notice in the prompt bar showing the elapsed time. If you see this notice regularly, check whether your credential script can be optimized.

`apiKeyHelper`, `ANTHROPIC_API_KEY`, and `ANTHROPIC_AUTH_TOKEN` apply to terminal CLI sessions only. Claude Desktop and remote sessions use OAuth exclusively and do not call `apiKeyHelper` or read API key environment variables.
### [​](https://code.claude.com/docs/en/authentication#authentication-precedence)

Authentication precedence

When multiple credentials are present, Claude Code chooses one in this order:
1.   Cloud provider credentials, when `CLAUDE_CODE_USE_BEDROCK`, `CLAUDE_CODE_USE_VERTEX`, or `CLAUDE_CODE_USE_FOUNDRY` is set. See [third-party integrations](https://code.claude.com/docs/en/third-party-integrations) for setup.
2.   `ANTHROPIC_AUTH_TOKEN` environment variable. Sent as the `Authorization: Bearer` header. Use this when routing through an [LLM gateway or proxy](https://code.claude.com/docs/en/llm-gateway) that authenticates with bearer tokens rather than Anthropic API keys.
3.   `ANTHROPIC_API_KEY` environment variable. Sent as the `X-Api-Key` header. Use this for direct Anthropic API access with a key from the [Claude Console](https://platform.claude.com/). In interactive mode, you are prompted once to approve or decline the key, and your choice is remembered. To change it later, use the “Use custom API key” toggle in `/config`. In non-interactive mode (`-p`), the key is always used when present.
4.   [`apiKeyHelper`](https://code.claude.com/docs/en/settings#available-settings) script output. Use this for dynamic or rotating credentials, such as short-lived tokens fetched from a vault.
5.   Subscription OAuth credentials from `/login`. This is the default for Claude Pro, Max, Team, and Enterprise users.

If you have an active Claude subscription but also have `ANTHROPIC_API_KEY` set in your environment, the API key takes precedence once approved. This can cause authentication failures if the key belongs to a disabled or expired organization. Run `unset ANTHROPIC_API_KEY` to fall back to your subscription, and check `/status` to confirm which method is active.[Claude Code on the Web](https://code.claude.com/docs/en/claude-code-on-the-web) always uses your subscription credentials. `ANTHROPIC_API_KEY` and `ANTHROPIC_AUTH_TOKEN` in the sandbox environment do not override them.

Was this page helpful?

Yes No

[Advanced setup](https://code.claude.com/docs/en/setup)[Security](https://code.claude.com/docs/en/security)

⌘I

[Claude Code Docs home page![Image 4: light logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/light.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=78fd01ff4f4340295a4f66e2ea54903c)![Image 5: dark logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/dark.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=1298a0c3b3a1da603b190d0de0e31712)](https://code.claude.com/docs/en/overview)

[x](https://x.com/AnthropicAI)[linkedin](https://www.linkedin.com/company/anthropicresearch)

Company

[Anthropic](https://www.anthropic.com/company)[Careers](https://www.anthropic.com/careers)[Economic Futures](https://www.anthropic.com/economic-futures)[Research](https://www.anthropic.com/research)[News](https://www.anthropic.com/news)[Trust center](https://trust.anthropic.com/)[Transparency](https://www.anthropic.com/transparency)

Help and security

[Availability](https://www.anthropic.com/supported-countries)[Status](https://status.anthropic.com/)[Support center](https://support.claude.com/)

Learn

[Courses](https://www.anthropic.com/learn)[MCP connectors](https://claude.com/partners/mcp)[Customer stories](https://www.claude.com/customers)[Engineering blog](https://www.anthropic.com/engineering)[Events](https://www.anthropic.com/events)[Powered by Claude](https://claude.com/partners/powered-by-claude)[Service partners](https://claude.com/partners/services)[Startups program](https://claude.com/programs/startups)

Terms and policies

[Privacy choices](https://code.claude.com/docs/en/authentication#)[Privacy policy](https://www.anthropic.com/legal/privacy)[Disclosure policy](https://www.anthropic.com/responsible-disclosure-policy)[Usage policy](https://www.anthropic.com/legal/aup)[Commercial terms](https://www.anthropic.com/legal/commercial-terms)[Consumer terms](https://www.anthropic.com/legal/consumer-terms)

Assistant

Responses are generated using AI and may contain mistakes.
