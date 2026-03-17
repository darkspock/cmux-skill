# cmux Browser Automation Skill

Give your AI coding agent eyes. Let it open a browser, see what's on the page, click buttons, fill forms, read console errors — and fix what's broken — all without leaving the terminal.

Works with [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Codex](https://github.com/openai/codex), and any CLI-based agent that can run shell commands.

> Requires [cmux](https://www.cmux.dev/) — a free, open-source macOS terminal with a built-in WebKit browser.
> [Download cmux](https://github.com/manaflow-ai/cmux/releases)

## What your agent can do

**Build and verify in one shot** — The agent writes a component, opens the browser, snapshots the page, and confirms it renders correctly. No more "it works on my machine" — the agent checks its own work.

**Debug visually** — Page blank after a deploy? The agent opens it, reads console errors and JS exceptions, traces the bug to the source, fixes it, reloads, and confirms the fix. All in one loop.

**Drive full user flows** — Login, checkout, onboarding — the agent fills forms, clicks through steps, waits for redirects, and verifies the final state. Like an E2E test, but ad hoc and in natural language.

**Inspect anything** — Accessibility tree snapshots, screenshots, element queries, computed styles, cookie values, localStorage — the agent can read whatever it needs from the page.

**Run JavaScript** — Eval expressions, inject scripts, add styles. The agent can poke at `window` state, trigger app-level functions, or hide debug banners.

## What's in the skill file

`browser/SKILL.md` is a single self-contained file with the complete CLI reference:

| Category | Commands |
|---|---|
| Navigation | `open`, `open-split`, `navigate`, `back`, `forward`, `reload` |
| Waiting | `wait --load-state`, `--selector`, `--text`, `--url-contains`, `--function` |
| DOM interaction | `click`, `dblclick`, `hover`, `fill`, `type`, `press`, `select`, `check`, `scroll` |
| Inspection | `snapshot`, `screenshot`, `get`, `is`, `find`, `highlight` |
| JavaScript | `eval`, `addscript`, `addinitscript`, `addstyle` |
| Session state | `cookies`, `storage`, `state save/load` |
| Tabs & frames | `tab list/new/switch/close`, `frame` |
| Console & errors | `console list`, `errors list` |
| Dialogs & downloads | `dialog accept/dismiss`, `download` |
| General cmux | workspaces, splits, notifications, sidebar status |

## Use cases

### "Build this component and verify it renders"

> **You:** Build a signup form with email and password fields.
>
> **Agent:** _(writes the component, then)_ Let me verify it renders correctly...
> _(opens `http://localhost:3000/signup` in a split, takes a snapshot, confirms the form fields are present, fills them in, submits, and checks for the success message)_

### "Fix this broken page"

> **You:** The dashboard is showing a blank screen after the last deploy.
>
> **Agent:** Let me check what's happening...
> _(opens the page, runs `errors list` and `console list`, finds a `TypeError: Cannot read properties of undefined`, traces it to the code, fixes it, reloads, and confirms it renders)_

### "Test the login flow end to end"

> **You:** Make sure the login flow works after your auth changes.
>
> **Agent:** I'll walk through it...
> _(opens the login page, fills email and password, clicks submit, waits for the redirect, snapshots the dashboard to confirm the user is logged in)_

### "Check what this page looks like"

> **You:** What does the pricing page look like?
>
> **Agent:** _(opens `/pricing` in a split, takes a screenshot, and reads the accessibility tree to describe the layout and content)_

## Setup

### Option 1: Ask your AI agent

Open Claude Code (or Codex, or any agent) and tell it:

> Download `https://raw.githubusercontent.com/darkspock/cmux-skill/main/browser.md` and save it to `~/.claude/skills/browser/SKILL.md`

For Codex, tell it to save it as `browser.md` in your project root and reference it in `AGENTS.md`.

**Important:** Restart your agent after installing. Skills are loaded at startup, so you need a fresh session for `/browser` to become available.

If you want it scoped to a single project (shared with your team), save it to `.claude/skills/browser/SKILL.md` in the project root and commit it.

### Option 2: Manual installation

Global (all projects):

```bash
mkdir -p ~/.claude/skills/browser && curl -fsSL https://raw.githubusercontent.com/darkspock/cmux-skill/main/browser.md -o ~/.claude/skills/browser/SKILL.md
```

Project-scoped (shared with your team):

```bash
mkdir -p .claude/skills/browser && curl -fsSL https://raw.githubusercontent.com/darkspock/cmux-skill/main/browser.md -o .claude/skills/browser/SKILL.md
```

**Important:** Restart your agent after installing for the skill to be available.

## Reference

Full command reference: [`SKILL.md`](browser.md)

Official cmux docs: [cmux.dev/docs/browser-automation](https://www.cmux.dev/docs/browser-automation)

## License

MIT
