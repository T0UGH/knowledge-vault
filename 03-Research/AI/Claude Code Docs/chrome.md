# chrome

Source: https://code.claude.com/docs/en/chrome

Title: Use Claude Code with Chrome (beta) - Claude Code Docs

URL Source: https://code.claude.com/docs/en/chrome

Markdown Content:
# Use Claude Code with Chrome (beta) - Claude Code Docs

[Skip to main content](https://code.claude.com/docs/en/chrome#content-area)

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

Platforms and integrations

Use Claude Code with Chrome (beta)

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

*   [Capabilities](https://code.claude.com/docs/en/chrome#capabilities)
*   [Prerequisites](https://code.claude.com/docs/en/chrome#prerequisites)
*   [Get started in the CLI](https://code.claude.com/docs/en/chrome#get-started-in-the-cli)
*   [Enable Chrome by default](https://code.claude.com/docs/en/chrome#enable-chrome-by-default)
*   [Manage site permissions](https://code.claude.com/docs/en/chrome#manage-site-permissions)
*   [Example workflows](https://code.claude.com/docs/en/chrome#example-workflows)
*   [Test a local web application](https://code.claude.com/docs/en/chrome#test-a-local-web-application)
*   [Debug with console logs](https://code.claude.com/docs/en/chrome#debug-with-console-logs)
*   [Automate form filling](https://code.claude.com/docs/en/chrome#automate-form-filling)
*   [Draft content in Google Docs](https://code.claude.com/docs/en/chrome#draft-content-in-google-docs)
*   [Extract data from web pages](https://code.claude.com/docs/en/chrome#extract-data-from-web-pages)
*   [Run multi-site workflows](https://code.claude.com/docs/en/chrome#run-multi-site-workflows)
*   [Record a demo GIF](https://code.claude.com/docs/en/chrome#record-a-demo-gif)
*   [Troubleshooting](https://code.claude.com/docs/en/chrome#troubleshooting)
*   [Extension not detected](https://code.claude.com/docs/en/chrome#extension-not-detected)
*   [Browser not responding](https://code.claude.com/docs/en/chrome#browser-not-responding)
*   [Connection drops during long sessions](https://code.claude.com/docs/en/chrome#connection-drops-during-long-sessions)
*   [Windows-specific issues](https://code.claude.com/docs/en/chrome#windows-specific-issues)
*   [Common error messages](https://code.claude.com/docs/en/chrome#common-error-messages)
*   [See also](https://code.claude.com/docs/en/chrome#see-also)

Platforms and integrations

# Use Claude Code with Chrome (beta)

Copy page

Connect Claude Code to your Chrome browser to test web apps, debug with console logs, automate form filling, and extract data from web pages.

Copy page

Claude Code integrates with the Claude in Chrome browser extension to give you browser automation capabilities from the CLI or the [VS Code extension](https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome). Build your code, then test and debug in the browser without switching contexts.Claude opens new tabs for browser tasks and shares your browser’s login state, so it can access any site you’re already signed into. Browser actions run in a visible Chrome window in real time. When Claude encounters a login page or CAPTCHA, it pauses and asks you to handle it manually.

Chrome integration is in beta and currently works with Google Chrome and Microsoft Edge. It is not yet supported on Brave, Arc, or other Chromium-based browsers. WSL (Windows Subsystem for Linux) is also not supported.

## [​](https://code.claude.com/docs/en/chrome#capabilities)

Capabilities

With Chrome connected, you can chain browser actions with coding tasks in a single workflow:
*   **Live debugging**: read console errors and DOM state directly, then fix the code that caused them
*   **Design verification**: build a UI from a Figma mock, then open it in the browser to verify it matches
*   **Web app testing**: test form validation, check for visual regressions, or verify user flows
*   **Authenticated web apps**: interact with Google Docs, Gmail, Notion, or any app you’re logged into without API connectors
*   **Data extraction**: pull structured information from web pages and save it locally
*   **Task automation**: automate repetitive browser tasks like data entry, form filling, or multi-site workflows
*   **Session recording**: record browser interactions as GIFs to document or share what happened

## [​](https://code.claude.com/docs/en/chrome#prerequisites)

Prerequisites

Before using Claude Code with Chrome, you need:
*   [Google Chrome](https://www.google.com/chrome/) or [Microsoft Edge](https://www.microsoft.com/edge) browser
*   [Claude in Chrome extension](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn) version 1.0.36 or higher, available in the Chrome Web Store for both browsers
*   [Claude Code](https://code.claude.com/docs/en/quickstart#step-1-install-claude-code) version 2.0.73 or higher
*   A direct Anthropic plan (Pro, Max, Teams, or Enterprise)

Chrome integration is not available through third-party providers like Amazon Bedrock, Google Cloud Vertex AI, or Microsoft Foundry. If you access Claude exclusively through a third-party provider, you need a separate claude.ai account to use this feature.

## [​](https://code.claude.com/docs/en/chrome#get-started-in-the-cli)

Get started in the CLI

1

[](https://code.claude.com/docs/en/chrome#)

Launch Claude Code with Chrome

Start Claude Code with the `--chrome` flag:

```
claude --chrome
```

You can also enable Chrome from within an existing session by running `/chrome`.

2

[](https://code.claude.com/docs/en/chrome#)

Ask Claude to use the browser

This example navigates to a page, interacts with it, and reports what it finds, all from your terminal or editor:

```
Go to code.claude.com/docs, click on the search box,
type "hooks", and tell me what results appear
```

Run `/chrome` at any time to check the connection status, manage permissions, or reconnect the extension.For VS Code, see [browser automation in VS Code](https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome).
### [​](https://code.claude.com/docs/en/chrome#enable-chrome-by-default)

Enable Chrome by default

To avoid passing `--chrome` each session, run `/chrome` and select “Enabled by default”.In the [VS Code extension](https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome), Chrome is available whenever the Chrome extension is installed. No additional flag is needed.

Enabling Chrome by default in the CLI increases context usage since browser tools are always loaded. If you notice increased context consumption, disable this setting and use `--chrome` only when needed.

### [​](https://code.claude.com/docs/en/chrome#manage-site-permissions)

Manage site permissions

Site-level permissions are inherited from the Chrome extension. Manage permissions in the Chrome extension settings to control which sites Claude can browse, click, and type on.
## [​](https://code.claude.com/docs/en/chrome#example-workflows)

Example workflows

These examples show common ways to combine browser actions with coding tasks. Run `/mcp` and select `claude-in-chrome` to see the full list of available browser tools.
### [​](https://code.claude.com/docs/en/chrome#test-a-local-web-application)

Test a local web application

When developing a web app, ask Claude to verify your changes work correctly:

```
I just updated the login form validation. Can you open localhost:3000,
try submitting the form with invalid data, and check if the error
messages appear correctly?
```

Claude navigates to your local server, interacts with the form, and reports what it observes.
### [​](https://code.claude.com/docs/en/chrome#debug-with-console-logs)

Debug with console logs

Claude can read console output to help diagnose problems. Tell Claude what patterns to look for rather than asking for all console output, since logs can be verbose:

```
Open the dashboard page and check the console for any errors when
the page loads.
```

Claude reads the console messages and can filter for specific patterns or error types.
### [​](https://code.claude.com/docs/en/chrome#automate-form-filling)

Automate form filling

Speed up repetitive data entry tasks:

```
I have a spreadsheet of customer contacts in contacts.csv. For each row,
go to the CRM at crm.example.com, click "Add Contact", and fill in the
name, email, and phone fields.
```

Claude reads your local file, navigates the web interface, and enters the data for each record.
### [​](https://code.claude.com/docs/en/chrome#draft-content-in-google-docs)

Draft content in Google Docs

Use Claude to write directly in your documents without API setup:

```
Draft a project update based on the recent commits and add it to my
Google Doc at docs.google.com/document/d/abc123
```

Claude opens the document, clicks into the editor, and types the content. This works with any web app you’re logged into: Gmail, Notion, Sheets, and more.
### [​](https://code.claude.com/docs/en/chrome#extract-data-from-web-pages)

Extract data from web pages

Pull structured information from websites:

```
Go to the product listings page and extract the name, price, and
availability for each item. Save the results as a CSV file.
```

Claude navigates to the page, reads the content, and compiles the data into a structured format.
### [​](https://code.claude.com/docs/en/chrome#run-multi-site-workflows)

Run multi-site workflows

Coordinate tasks across multiple websites:

```
Check my calendar for meetings tomorrow, then for each meeting with
an external attendee, look up their company website and add a note
about what they do.
```

Claude works across tabs to gather information and complete the workflow.
### [​](https://code.claude.com/docs/en/chrome#record-a-demo-gif)

Record a demo GIF

Create shareable recordings of browser interactions:

```
Record a GIF showing how to complete the checkout flow, from adding
an item to the cart through to the confirmation page.
```

Claude records the interaction sequence and saves it as a GIF file.
## [​](https://code.claude.com/docs/en/chrome#troubleshooting)

Troubleshooting

### [​](https://code.claude.com/docs/en/chrome#extension-not-detected)

Extension not detected

If Claude Code shows “Chrome extension not detected”:
1.   Verify the Chrome extension is installed and enabled in `chrome://extensions`
2.   Verify Claude Code is up to date by running `claude --version`
3.   Check that Chrome is running
4.   Run `/chrome` and select “Reconnect extension” to re-establish the connection
5.   If the issue persists, restart both Claude Code and Chrome

The first time you enable Chrome integration, Claude Code installs a native messaging host configuration file. Chrome reads this file on startup, so if the extension isn’t detected on your first attempt, restart Chrome to pick up the new configuration.If the connection still fails, verify the host configuration file exists at:For Chrome:
*   **macOS**: `~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
*   **Linux**: `~/.config/google-chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
*   **Windows**: check `HKCU\Software\Google\Chrome\NativeMessagingHosts\` in the Windows Registry

For Edge:
*   **macOS**: `~/Library/Application Support/Microsoft Edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
*   **Linux**: `~/.config/microsoft-edge/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`
*   **Windows**: check `HKCU\Software\Microsoft\Edge\NativeMessagingHosts\` in the Windows Registry

### [​](https://code.claude.com/docs/en/chrome#browser-not-responding)

Browser not responding

If Claude’s browser commands stop working:
1.   Check if a modal dialog (alert, confirm, prompt) is blocking the page. JavaScript dialogs block browser events and prevent Claude from receiving commands. Dismiss the dialog manually, then tell Claude to continue.
2.   Ask Claude to create a new tab and try again
3.   Restart the Chrome extension by disabling and re-enabling it in `chrome://extensions`

### [​](https://code.claude.com/docs/en/chrome#connection-drops-during-long-sessions)

Connection drops during long sessions

The Chrome extension’s service worker can go idle during extended sessions, which breaks the connection. If browser tools stop working after a period of inactivity, run `/chrome` and select “Reconnect extension”.
### [​](https://code.claude.com/docs/en/chrome#windows-specific-issues)

Windows-specific issues

On Windows, you may encounter:
*   **Named pipe conflicts (EADDRINUSE)**: if another process is using the same named pipe, restart Claude Code. Close any other Claude Code sessions that might be using Chrome.
*   **Native messaging host errors**: if the native messaging host crashes on startup, try reinstalling Claude Code to regenerate the host configuration.

### [​](https://code.claude.com/docs/en/chrome#common-error-messages)

Common error messages

These are the most frequently encountered errors and how to resolve them:

| Error | Cause | Fix |
| --- | --- | --- |
| ”Browser extension is not connected” | Native messaging host cannot reach the extension | Restart Chrome and Claude Code, then run `/chrome` to reconnect |
| ”Extension not detected” | Chrome extension is not installed or is disabled | Install or enable the extension in `chrome://extensions` |
| ”No tab available” | Claude tried to act before a tab was ready | Ask Claude to create a new tab and retry |
| ”Receiving end does not exist” | Extension service worker went idle | Run `/chrome` and select “Reconnect extension” |

## [​](https://code.claude.com/docs/en/chrome#see-also)

See also

*   [Use Claude Code in VS Code](https://code.claude.com/docs/en/vs-code#automate-browser-tasks-with-chrome): browser automation in the VS Code extension
*   [CLI reference](https://code.claude.com/docs/en/cli-reference): command-line flags including `--chrome`
*   [Common workflows](https://code.claude.com/docs/en/common-workflows): more ways to use Claude Code
*   [Data and privacy](https://code.claude.com/docs/en/data-usage): how Claude Code handles your data
*   [Getting started with Claude in Chrome](https://support.claude.com/en/articles/12012173-getting-started-with-claude-in-chrome): full documentation for the Chrome extension, including shortcuts, scheduling, and permissions

Was this page helpful?

Yes No

[Use Desktop](https://code.claude.com/docs/en/desktop)[Visual Studio Code](https://code.claude.com/docs/en/vs-code)

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

[Privacy choices](https://code.claude.com/docs/en/chrome#)[Privacy policy](https://www.anthropic.com/legal/privacy)[Disclosure policy](https://www.anthropic.com/responsible-disclosure-policy)[Usage policy](https://www.anthropic.com/legal/aup)[Commercial terms](https://www.anthropic.com/legal/commercial-terms)[Consumer terms](https://www.anthropic.com/legal/consumer-terms)

Assistant

Responses are generated using AI and may contain mistakes.
