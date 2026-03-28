# desktop-quickstart

Source: https://code.claude.com/docs/en/desktop-quickstart

Title: Get started with the desktop app - Claude Code Docs

URL Source: https://code.claude.com/docs/en/desktop-quickstart

Markdown Content:
# Get started with the desktop app - Claude Code Docs

[Skip to main content](https://code.claude.com/docs/en/desktop-quickstart#content-area)

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

Claude Code on desktop

Get started with the desktop app

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
    *   [Get started](https://code.claude.com/docs/en/desktop-quickstart)
    *   [Use Desktop](https://code.claude.com/docs/en/desktop)

*   [Chrome extension (beta)](https://code.claude.com/docs/en/chrome)
*   [Visual Studio Code](https://code.claude.com/docs/en/vs-code)
*   [JetBrains IDEs](https://code.claude.com/docs/en/jetbrains)
*   Code review & CI/CD 
*   [Claude Code in Slack](https://code.claude.com/docs/en/slack)

On this page

*   [Install](https://code.claude.com/docs/en/desktop-quickstart#install)
*   [Start your first session](https://code.claude.com/docs/en/desktop-quickstart#start-your-first-session)
*   [Now what?](https://code.claude.com/docs/en/desktop-quickstart#now-what)
*   [Coming from the CLI?](https://code.claude.com/docs/en/desktop-quickstart#coming-from-the-cli)
*   [What’s next](https://code.claude.com/docs/en/desktop-quickstart#what%E2%80%99s-next)

Claude Code on desktop

# Get started with the desktop app

Copy page

Install Claude Code on desktop and start your first coding session

Copy page

The desktop app gives you Claude Code with a graphical interface: visual diff review, live app preview, GitHub PR monitoring with auto-merge, parallel sessions with Git worktree isolation, scheduled tasks, and the ability to run tasks remotely. No terminal required.This page walks through installing the app and starting your first session. If you’re already set up, see [Use Claude Code Desktop](https://code.claude.com/docs/en/desktop) for the full reference.

![Image 4: The Claude Code Desktop interface showing the Code tab selected, with a prompt box, permission mode selector set to Ask permissions, model picker, folder selector, and Local environment option](https://mintcdn.com/claude-code/CNLUpFGiXoc9mhvD/images/desktop-code-tab-light.png?fit=max&auto=format&n=CNLUpFGiXoc9mhvD&q=85&s=9a36a7a27b9f4c6f2e1c83bdb34f69ce)![Image 5: The Claude Code Desktop interface in dark mode showing the Code tab selected, with a prompt box, permission mode selector set to Ask permissions, model picker, folder selector, and Local environment option](https://mintcdn.com/claude-code/CNLUpFGiXoc9mhvD/images/desktop-code-tab-dark.png?fit=max&auto=format&n=CNLUpFGiXoc9mhvD&q=85&s=5463defe81c459fb9b1f91f6a958cfb8)

The desktop app has three tabs:
*   **Chat**: General conversation with no file access, similar to claude.ai.
*   **Cowork**: An autonomous background agent that works on tasks in a cloud VM with its own environment. It can run independently while you do other work.
*   **Code**: An interactive coding assistant with direct access to your local files. You review and approve each change in real time.

Chat and Cowork are covered in the [Claude Desktop support articles](https://support.claude.com/en/collections/16163169-claude-desktop). This page focuses on the **Code** tab.

Claude Code requires a [Pro, Max, Teams, or Enterprise subscription](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=desktop_quickstart_pricing).

## [​](https://code.claude.com/docs/en/desktop-quickstart#install)

Install

1

[](https://code.claude.com/docs/en/desktop-quickstart#)

Download the app

Download Claude for your platform.

## macOS

Universal build for Intel and Apple Silicon

## Windows

For x64 processors

For Windows ARM64, [download here](https://claude.ai/api/desktop/win32/arm64/exe/latest/redirect?utm_source=claude_code&utm_medium=docs).Linux is not currently supported.

2

[](https://code.claude.com/docs/en/desktop-quickstart#)

Sign in

Launch Claude from your Applications folder (macOS) or Start menu (Windows). Sign in with your Anthropic account.

3

[](https://code.claude.com/docs/en/desktop-quickstart#)

Open the Code tab

Click the **Code** tab at the top center. If clicking Code prompts you to upgrade, you need to [subscribe to a paid plan](https://claude.com/pricing?utm_source=claude_code&utm_medium=docs&utm_content=desktop_quickstart_upgrade) first. If it prompts you to sign in online, complete the sign-in and restart the app. If you see a 403 error, see [authentication troubleshooting](https://code.claude.com/docs/en/desktop#403-or-authentication-errors-in-the-code-tab).

The desktop app includes Claude Code. You don’t need to install Node.js or the CLI separately. To use `claude` from the terminal, install the CLI separately. See [Get started with the CLI](https://code.claude.com/docs/en/quickstart).
## [​](https://code.claude.com/docs/en/desktop-quickstart#start-your-first-session)

Start your first session

With the Code tab open, choose a project and give Claude something to do.

1

[](https://code.claude.com/docs/en/desktop-quickstart#)

Choose an environment and folder

Select **Local** to run Claude on your machine using your files directly. Click **Select folder** and choose your project directory.

Start with a small project you know well. It’s the fastest way to see what Claude Code can do. On Windows, [Git](https://git-scm.com/downloads/win) must be installed for local sessions to work. Most Macs include Git by default.

You can also select:
*   **Remote**: Run sessions on Anthropic’s cloud infrastructure that continue even if you close the app. Remote sessions use the same infrastructure as [Claude Code on the web](https://code.claude.com/docs/en/claude-code-on-the-web).
*   **SSH**: Connect to a remote machine over SSH (your own servers, cloud VMs, or dev containers). Claude Code must be installed on the remote machine.

2

[](https://code.claude.com/docs/en/desktop-quickstart#)

Choose a model

Select a model from the dropdown next to the send button. See [models](https://code.claude.com/docs/en/model-config#available-models) for a comparison of Opus, Sonnet, and Haiku. You cannot change the model after the session starts.

3

[](https://code.claude.com/docs/en/desktop-quickstart#)

Tell Claude what to do

Type what you want Claude to do:
*   `Find a TODO comment and fix it`
*   `Add tests for the main function`
*   `Create a CLAUDE.md with instructions for this codebase`

A [session](https://code.claude.com/docs/en/desktop#work-in-parallel-with-sessions) is a conversation with Claude about your code. Each session tracks its own context and changes, so you can work on multiple tasks without them interfering with each other.

4

[](https://code.claude.com/docs/en/desktop-quickstart#)

Review and accept changes

By default, the Code tab starts in [Ask permissions mode](https://code.claude.com/docs/en/desktop#choose-a-permission-mode), where Claude proposes changes and waits for your approval before applying them. You’ll see:
1.   A [diff view](https://code.claude.com/docs/en/desktop#review-changes-with-diff-view) showing exactly what will change in each file
2.   Accept/Reject buttons to approve or decline each change
3.   Real-time updates as Claude works through your request

If you reject a change, Claude will ask how you’d like to proceed differently. Your files aren’t modified until you accept.

## [​](https://code.claude.com/docs/en/desktop-quickstart#now-what)

Now what?

You’ve made your first edit. For the full reference on everything Desktop can do, see [Use Claude Code Desktop](https://code.claude.com/docs/en/desktop). Here are some things to try next.**Interrupt and steer.** You can interrupt Claude at any point. If it’s going down the wrong path, click the stop button or type your correction and press **Enter**. Claude stops what it’s doing and adjusts based on your input. You don’t have to wait for it to finish or start over.**Give Claude more context.** Type `@filename` in the prompt box to pull a specific file into the conversation, attach images and PDFs using the attachment button, or drag and drop files directly into the prompt. The more context Claude has, the better the results. See [Add files and context](https://code.claude.com/docs/en/desktop#add-files-and-context-to-prompts).**Use skills for repeatable tasks.** Type `/` or click **+** → **Slash commands** to browse [built-in commands](https://code.claude.com/docs/en/commands), [custom skills](https://code.claude.com/docs/en/skills), and plugin skills. Skills are reusable prompts you can invoke whenever you need them, like code review checklists or deployment steps.**Review changes before committing.** After Claude edits files, a `+12 -1` indicator appears. Click it to open the [diff view](https://code.claude.com/docs/en/desktop#review-changes-with-diff-view), review modifications file by file, and comment on specific lines. Claude reads your comments and revises. Click **Review code** to have Claude evaluate the diffs itself and leave inline suggestions.**Adjust how much control you have.** Your [permission mode](https://code.claude.com/docs/en/desktop#choose-a-permission-mode) controls the balance. Ask permissions (default) requires approval before every edit. Auto accept edits auto-accepts file edits for faster iteration. Plan mode lets Claude map out an approach without touching any files, which is useful before a large refactor.**Add plugins for more capabilities.** Click the **+** button next to the prompt box and select **Plugins** to browse and install [plugins](https://code.claude.com/docs/en/desktop#install-plugins) that add skills, agents, MCP servers, and more.**Preview your app.** Click the **Preview** dropdown to run your dev server directly in the desktop. Claude can view the running app, test endpoints, inspect logs, and iterate on what it sees. See [Preview your app](https://code.claude.com/docs/en/desktop#preview-your-app).**Track your pull request.** After opening a PR, Claude Code monitors CI check results and can automatically fix failures or merge the PR once all checks pass. See [Monitor pull request status](https://code.claude.com/docs/en/desktop#monitor-pull-request-status).**Put Claude on a schedule.** Set up [scheduled tasks](https://code.claude.com/docs/en/desktop#schedule-recurring-tasks) to run Claude automatically on a recurring basis: a daily code review every morning, a weekly dependency audit, or a briefing that pulls from your connected tools.**Scale up when you’re ready.** Open [parallel sessions](https://code.claude.com/docs/en/desktop#work-in-parallel-with-sessions) from the sidebar to work on multiple tasks at once, each in its own Git worktree. Send [long-running work to the cloud](https://code.claude.com/docs/en/desktop#run-long-running-tasks-remotely) so it continues even if you close the app, or [continue a session on the web or in your IDE](https://code.claude.com/docs/en/desktop#continue-in-another-surface) if a task takes longer than expected. [Connect external tools](https://code.claude.com/docs/en/desktop#extend-claude-code) like GitHub, Slack, and Linear to bring your workflow together.
## [​](https://code.claude.com/docs/en/desktop-quickstart#coming-from-the-cli)

Coming from the CLI?

Desktop runs the same engine as the CLI with a graphical interface. You can run both simultaneously on the same project, and they share configuration (CLAUDE.md files, MCP servers, hooks, skills, and settings). For a full comparison of features, flag equivalents, and what’s not available in Desktop, see [CLI comparison](https://code.claude.com/docs/en/desktop#coming-from-the-cli).
## [​](https://code.claude.com/docs/en/desktop-quickstart#what%E2%80%99s-next)

What’s next

*   [Use Claude Code Desktop](https://code.claude.com/docs/en/desktop): permission modes, parallel sessions, diff view, connectors, and enterprise configuration
*   [Troubleshooting](https://code.claude.com/docs/en/desktop#troubleshooting): solutions to common errors and setup issues
*   [Best practices](https://code.claude.com/docs/en/best-practices): tips for writing effective prompts and getting the most out of Claude Code
*   [Common workflows](https://code.claude.com/docs/en/common-workflows): tutorials for debugging, refactoring, testing, and more

Was this page helpful?

Yes No

[Schedule tasks on the web](https://code.claude.com/docs/en/web-scheduled-tasks)[Use Desktop](https://code.claude.com/docs/en/desktop)

⌘I

[Claude Code Docs home page![Image 6: light logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/light.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=78fd01ff4f4340295a4f66e2ea54903c)![Image 7: dark logo](https://mintcdn.com/claude-code/c5r9_6tjPMzFdDDT/logo/dark.svg?fit=max&auto=format&n=c5r9_6tjPMzFdDDT&q=85&s=1298a0c3b3a1da603b190d0de0e31712)](https://code.claude.com/docs/en/overview)

[x](https://x.com/AnthropicAI)[linkedin](https://www.linkedin.com/company/anthropicresearch)

Company

[Anthropic](https://www.anthropic.com/company)[Careers](https://www.anthropic.com/careers)[Economic Futures](https://www.anthropic.com/economic-futures)[Research](https://www.anthropic.com/research)[News](https://www.anthropic.com/news)[Trust center](https://trust.anthropic.com/)[Transparency](https://www.anthropic.com/transparency)

Help and security

[Availability](https://www.anthropic.com/supported-countries)[Status](https://status.anthropic.com/)[Support center](https://support.claude.com/)

Learn

[Courses](https://www.anthropic.com/learn)[MCP connectors](https://claude.com/partners/mcp)[Customer stories](https://www.claude.com/customers)[Engineering blog](https://www.anthropic.com/engineering)[Events](https://www.anthropic.com/events)[Powered by Claude](https://claude.com/partners/powered-by-claude)[Service partners](https://claude.com/partners/services)[Startups program](https://claude.com/programs/startups)

Terms and policies

[Privacy choices](https://code.claude.com/docs/en/desktop-quickstart#)[Privacy policy](https://www.anthropic.com/legal/privacy)[Disclosure policy](https://www.anthropic.com/responsible-disclosure-policy)[Usage policy](https://www.anthropic.com/legal/aup)[Commercial terms](https://www.anthropic.com/legal/commercial-terms)[Consumer terms](https://www.anthropic.com/legal/consumer-terms)

Assistant

Responses are generated using AI and may contain mistakes.
