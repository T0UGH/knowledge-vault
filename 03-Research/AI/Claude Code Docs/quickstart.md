# quickstart

Source: https://code.claude.com/docs/en/quickstart

Title: Quickstart - Claude Code Docs

URL Source: https://code.claude.com/docs/en/quickstart

Markdown Content:
# Quickstart - Claude Code Docs

[Skip to main content](https://code.claude.com/docs/en/quickstart#content-area)

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

Getting started

Quickstart

[Getting started](https://code.claude.com/docs/en/overview)[Build with Claude Code](https://code.claude.com/docs/en/sub-agents)[Deployment](https://code.claude.com/docs/en/third-party-integrations)[Administration](https://code.claude.com/docs/en/setup)[Configuration](https://code.claude.com/docs/en/settings)[Reference](https://code.claude.com/docs/en/cli-reference)[Resources](https://code.claude.com/docs/en/legal-and-compliance)

##### Getting started

*   [Overview](https://code.claude.com/docs/en/overview)
*   [Quickstart](https://code.claude.com/docs/en/quickstart)
*   [Changelog](https://code.claude.com/docs/en/changelog)

##### Core concepts

*   [How Claude Code works](https://code.claude.com/docs/en/how-claude-code-works)
*   [Extend Claude Code](https://code.claude.com/docs/en/features-overview)
*   [Explore the .claude directory](https://code.claude.com/docs/en/claude-directory)
*   [Explore the context window](https://code.claude.com/docs/en/context-window)

##### Use Claude Code

*   [Store instructions and memories](https://code.claude.com/docs/en/memory)
*   [Permission modes](https://code.claude.com/docs/en/permission-modes)
*   [Common workflows](https://code.claude.com/docs/en/common-workflows)
*   [Best practices](https://code.claude.com/docs/en/best-practices)

##### Platforms and integrations

*   [Overview](https://code.claude.com/docs/en/platforms)
*   [Remote Control](https://code.claude.com/docs/en/remote-control)
*   Claude Code on the web 
*   Claude Code on desktop 
*   [Chrome extension (beta)](https://code.claude.com/docs/en/chrome)
*   [Visual Studio Code](https://code.claude.com/docs/en/vs-code)
*   [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains)
*   Code review & CI/CD 
*   [Claude Code in Slack](https://code.claude.com/docs/en/slack)

On this page

*   [Before you begin](https://code.claude.com/docs/en/quickstart#before-you-begin)
*   [Step 1: Install Claude Code](https://code.claude.com/docs/en/quickstart#step-1-install-claude-code)
*   [Step 2: Log in to your account](https://code.claude.com/docs/en/quickstart#step-2-log-in-to-your-account)
*   [Step 3: Start your first session](https://code.claude.com/docs/en/quickstart#step-3-start-your-first-session)
*   [Step 4: Ask your first question](https://code.claude.com/docs/en/quickstart#step-4-ask-your-first-question)
*   [Step 5: Make your first code change](https://code.claude.com/docs/en/quickstart#step-5-make-your-first-code-change)
*   [Step 6: Use Git with Claude Code](https://code.claude.com/docs/en/quickstart#step-6-use-git-with-claude-code)
*   [Step 7: Fix a bug or add a feature](https://code.claude.com/docs/en/quickstart#step-7-fix-a-bug-or-add-a-feature)
*   [Step 8: Test out other common workflows](https://code.claude.com/docs/en/quickstart#step-8-test-out-other-common-workflows)
*   [Essential commands](https://code.claude.com/docs/en/quickstart#essential-commands)
*   [Pro tips for beginners](https://code.claude.com/docs/en/quickstart#pro-tips-for-beginners)
*   [What’s next?](https://code.claude.com/docs/en/quickstart#what%E2%80%99s-next)
*   [Getting help](https://code.claude.com/docs/en/quickstart#getting-help)

Getting started

# Quickstart

Copy page

Welcome to Claude Code!

Copy page

This quickstart guide will have you using AI-powered coding assistance in a few minutes. By the end, you’ll understand how to use Claude Code for common development tasks.
## [​](https://code.claude.com/docs/en/quickstart#before-you-begin)

Before you begin

Make sure you have:
*   A terminal or command prompt open
    *   If you’ve never used the terminal before, check out the [terminal guide](https://code.claude.com/docs/en/terminal-guide)

*   A code project to work with
*   A [Claude subscription](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=quickstart_prereq) (Pro, Max, Teams, or Enterprise), [Claude Console](https://console.anthropic.com/) account, or access through a [supported cloud provider](https://code.claude.com/docs/en/third-party-integrations)

This guide covers the terminal CLI. Claude Code is also available on the [web](https://claude.ai/code), as a [desktop app](https://code.claude.com/docs/en/desktop), in [VS Code](https://code.claude.com/docs/en/vs-code) and [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains), in [Slack](https://code.claude.com/docs/en/slack), and in CI/CD with [GitHub Actions](https://code.claude.com/docs/en/github-actions) and [GitLab](https://code.claude.com/docs/en/gitlab-ci-cd). See [all interfaces](https://code.claude.com/docs/en/overview#use-claude-code-everywhere).

## [​](https://code.claude.com/docs/en/quickstart#step-1-install-claude-code)

Step 1: Install Claude Code

To install Claude Code, use one of the following methods:

*   Native Install (Recommended) 
*   Homebrew 
*   WinGet 

**macOS, Linux, WSL:**

```
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell:**

```
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD:**

```
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

**Windows requires [Git for Windows](https://git-scm.com/downloads/win).** Install it first if you don’t have it.

Native installations automatically update in the background to keep you on the latest version.

```
brew install --cask claude-code
```

Homebrew installations do not auto-update. Run `brew upgrade claude-code` periodically to get the latest features and security fixes.

```
winget install Anthropic.ClaudeCode
```

WinGet installations do not auto-update. Run `winget upgrade Anthropic.ClaudeCode` periodically to get the latest features and security fixes.

## [​](https://code.claude.com/docs/en/quickstart#step-2-log-in-to-your-account)

Step 2: Log in to your account

Claude Code requires an account to use. When you start an interactive session with the `claude` command, you’ll need to log in:

```
claude
# You'll be prompted to log in on first use
```

```
/login
# Follow the prompts to log in with your account
```

You can log in using any of these account types:
*   [Claude Pro, Max, Teams, or Enterprise](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=quickstart_login) (recommended)
*   [Claude Console](https://console.anthropic.com/) (API access with pre-paid credits). On first login, a “Claude Code” workspace is automatically created in the Console for centralized cost tracking.
*   [Amazon Bedrock, Google Vertex AI, or Microsoft Foundry](https://code.claude.com/docs/en/third-party-integrations) (enterprise cloud providers)

Once logged in, your credentials are stored and you won’t need to log in again. To switch accounts later, use the `/login` command.
## [​](https://code.claude.com/docs/en/quickstart#step-3-start-your-first-session)

Step 3: Start your first session

Open your terminal in any project directory and start Claude Code:

```
cd /path/to/your/project
claude
```

You’ll see the Claude Code welcome screen with your session information, recent conversations, and latest updates. Type `/help` for available commands or `/resume` to continue a previous conversation.

After logging in (Step 2), your credentials are stored on your system. Learn more in [Credential Management](https://code.claude.com/docs/en/authentication#credential-management).

## [​](https://code.claude.com/docs/en/quickstart#step-4-ask-your-first-question)

Step 4: Ask your first question

Let’s start with understanding your codebase. Try one of these commands:

```
what does this project do?
```

Claude will analyze your files and provide a summary. You can also ask more specific questions:

```
what technologies does this project use?
```

```
where is the main entry point?
```

```
explain the folder structure
```

You can also ask Claude about its own capabilities:

```
what can Claude Code do?
```

```
how do I create custom skills in Claude Code?
```

```
can Claude Code work with Docker?
```

Claude Code reads your project files as needed. You don’t have to manually add context.

## [​](https://code.claude.com/docs/en/quickstart#step-5-make-your-first-code-change)

Step 5: Make your first code change

Now let’s make Claude Code do some actual coding. Try a simple task:

```
add a hello world function to the main file
```

Claude Code will:
1.   Find the appropriate file
2.   Show you the proposed changes
3.   Ask for your approval
4.   Make the edit

Claude Code always asks for permission before modifying files. You can approve individual changes or enable “Accept all” mode for a session.

## [​](https://code.claude.com/docs/en/quickstart#step-6-use-git-with-claude-code)

Step 6: Use Git with Claude Code

Claude Code makes Git operations conversational:

```
what files have I changed?
```

```
commit my changes with a descriptive message
```

You can also prompt for more complex Git operations:

```
create a new branch called feature/quickstart
```

```
show me the last 5 commits
```

```
help me resolve merge conflicts
```

## [​](https://code.claude.com/docs/en/quickstart#step-7-fix-a-bug-or-add-a-feature)

Step 7: Fix a bug or add a feature

Claude is proficient at debugging and feature implementation.Describe what you want in natural language:

```
add input validation to the user registration form
```

Or fix existing issues:

```
there's a bug where users can submit empty forms - fix it
```

Claude Code will:
*   Locate the relevant code
*   Understand the context
*   Implement a solution
*   Run tests if available

## [​](https://code.claude.com/docs/en/quickstart#step-8-test-out-other-common-workflows)

Step 8: Test out other common workflows

There are a number of ways to work with Claude:**Refactor code**

```
refactor the authentication module to use async/await instead of callbacks
```

**Write tests**

```
write unit tests for the calculator functions
```

**Update documentation**

```
update the README with installation instructions
```

**Code review**

```
review my changes and suggest improvements
```

Talk to Claude like you would a helpful colleague. Describe what you want to achieve, and it will help you get there.

## [​](https://code.claude.com/docs/en/quickstart#essential-commands)

Essential commands

Here are the most important commands for daily use:

| Command | What it does | Example |
| --- | --- | --- |
| `claude` | Start interactive mode | `claude` |
| `claude "task"` | Run a one-time task | `claude "fix the build error"` |
| `claude -p "query"` | Run one-off query, then exit | `claude -p "explain this function"` |
| `claude -c` | Continue most recent conversation in current directory | `claude -c` |
| `claude -r` | Resume a previous conversation | `claude -r` |
| `claude commit` | Create a Git commit | `claude commit` |
| `/clear` | Clear conversation history | `/clear` |
| `/help` | Show available commands | `/help` |
| `exit` or Ctrl+C | Exit Claude Code | `exit` |

See the [CLI reference](https://code.claude.com/docs/en/cli-reference) for a complete list of commands.
## [​](https://code.claude.com/docs/en/quickstart#pro-tips-for-beginners)

Pro tips for beginners

For more, see [best practices](https://code.claude.com/docs/en/best-practices) and [common workflows](https://code.claude.com/docs/en/common-workflows).

Be specific with your requests

Instead of: “fix the bug”Try: “fix the login bug where users see a blank screen after entering wrong credentials”

Use step-by-step instructions

Break complex tasks into steps:

```
1. create a new database table for user profiles
2. create an API endpoint to get and update user profiles
3. build a webpage that allows users to see and edit their information
```

Let Claude explore first

Before making changes, let Claude understand your code:

```
analyze the database schema
```

```
build a dashboard showing products that are most frequently returned by our UK customers
```

Save time with shortcuts

*   Press `?` to see all available keyboard shortcuts
*   Use Tab for command completion
*   Press ↑ for command history
*   Type `/` to see all commands and skills

## [​](https://code.claude.com/docs/en/quickstart#what%E2%80%99s-next)

What’s next?

Now that you’ve learned the basics, explore more advanced features:

## How Claude Code works

Understand the agentic loop, built-in tools, and how Claude Code interacts with your project

## Best practices

Get better results with effective prompting and project setup

## Common workflows

Step-by-step guides for common tasks

## Extend Claude Code

Customize with CLAUDE.md, skills, hooks, MCP, and more

## [​](https://code.claude.com/docs/en/quickstart#getting-help)

Getting help

*   **In Claude Code**: Type `/help` or ask “how do I…”
*   **Documentation**: You’re here! Browse other guides
*   **Community**: Join our [Discord](https://www.anthropic.com/discord) for tips and support

Was this page helpful?

Yes No

[Overview](https://code.claude.com/docs/en/overview)[Changelog](https://code.claude.com/docs/en/changelog)

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

[Privacy choices](https://code.claude.com/docs/en/quickstart#)[Privacy policy](https://www.anthropic.com/legal/privacy)[Disclosure policy](https://www.anthropic.com/responsible-disclosure-policy)[Usage policy](https://www.anthropic.com/legal/aup)[Commercial terms](https://www.anthropic.com/legal/commercial-terms)[Consumer terms](https://www.anthropic.com/legal/consumer-terms)

Assistant

Responses are generated using AI and may contain mistakes.
