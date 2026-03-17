# cmux Browser Automation Skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that gives AI coding agents full control over [cmux](https://www.cmux.dev/)'s integrated WebKit browser — navigate pages, interact with DOM elements, inspect state, evaluate JavaScript, and more, all from the terminal.

Also works with [Codex](https://github.com/openai/codex) and any CLI-based agent that can run shell commands.

> **Requires [cmux](https://github.com/manaflow-ai/cmux)** — a free, open-source native macOS terminal with vertical tabs, split panes, notifications, and a built-in browser. [Download cmux here](https://github.com/manaflow-ai/cmux/releases).

## What's included

`cmux-browser.md` is a self-contained skill file with the complete CLI reference for:

- **Browser automation** — open, navigate, wait, click, fill, type, scroll, select
- **Inspection** — accessibility tree snapshots, screenshots, element queries
- **JavaScript** — eval, inject scripts and styles
- **Session management** — cookies, localStorage, full state save/restore
- **Tabs, frames, dialogs, downloads**
- **General cmux commands** — workspaces, splits, notifications, sidebar status

## Setup — Claude Code

### Option 1: Copy the skill file

```bash
# Create the skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy the skill
cp cmux-browser.md ~/.claude/skills/cmux-browser.md
```

Then invoke it in Claude Code with:

```
/cmux-browser
```

### Option 2: Add to your project

Copy `cmux-browser.md` into your project's `.claude/skills/` directory:

```bash
mkdir -p .claude/skills
cp cmux-browser.md .claude/skills/cmux-browser.md
```

This makes it available to anyone working on the project.

### Option 3: Reference in CLAUDE.md

Add a section to your project's `CLAUDE.md`:

```markdown
## Browser Testing

When asked to test or verify UI in the browser, read `.claude/skills/cmux-browser.md` for the full cmux browser CLI reference.
```

## Setup — Codex

Codex reads instructions from a `AGENTS.md` (or `CODEX.md`) file. Add the skill content directly or reference it:

### Option 1: Include directly

Append the contents of `cmux-browser.md` to your `AGENTS.md`:

```bash
cat cmux-browser.md >> AGENTS.md
```

### Option 2: Reference the file

Add this to your `AGENTS.md`:

```markdown
## Browser Automation

When you need to interact with a browser, read the file `cmux-browser.md` in the project root for the complete cmux browser CLI reference. Use these commands to open pages, interact with elements, and verify UI state.
```

Then copy the skill file to your project root:

```bash
cp cmux-browser.md /path/to/your/project/cmux-browser.md
```

## Use cases

These are real workflows where an agent uses the skill to get things done.

### "Build this component and verify it renders correctly"

The agent writes the code, starts the dev server, then opens the browser to check its own work:

> **You:** Build a signup form with email and password fields.
>
> **Agent:** _(writes the component, then)_ Let me verify it renders correctly...
> _(opens `cmux browser open-split http://localhost:3000/signup`, takes a snapshot, confirms the form fields are present, fills them in, submits, and checks for the success message)_

### "Fix this broken page"

The agent opens the broken page, reads console errors, and uses that to diagnose the bug:

> **You:** The dashboard page is showing a blank screen after the last deploy.
>
> **Agent:** Let me check what's happening...
> _(opens the page, runs `errors list` and `console list`, finds a `TypeError: Cannot read properties of undefined`, traces it to the code, fixes it, reloads the browser, and confirms it renders)_

### "Test the full login flow end to end"

The agent drives the browser through the entire flow like a user would:

> **You:** Make sure the login flow works after your auth changes.
>
> **Agent:** I'll walk through it...
> _(opens the login page, fills email and password, clicks submit, waits for the redirect, snapshots the dashboard to confirm the user is logged in)_

### "Check what this page looks like right now"

Quick visual check — the agent screenshots the page for you:

> **You:** What does the pricing page look like?
>
> **Agent:** _(opens `/pricing` in a split, takes a screenshot, and reads the accessibility tree to describe the layout and content)_

## Reference

For the full command reference, see [`cmux-browser.md`](cmux-browser.md).

For official cmux documentation, visit [cmux.dev/docs/browser-automation](https://www.cmux.dev/docs/browser-automation).

## License

MIT
