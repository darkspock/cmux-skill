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

## Examples

### Open your dev server and verify the page title

```bash
cmux browser open-split http://localhost:3000
# Output: OK surface=surface:3 pane=pane:2 placement=split

cmux browser surface:3 wait --load-state complete --timeout-ms 10000
cmux browser surface:3 get title
# Output: My App - Dashboard
```

### Snapshot the accessibility tree and click a button

```bash
cmux browser surface:3 snapshot --interactive --compact
# Output:
# - document "My App"
#     - button "Sign In" [ref=e5]
#     - link "Register" [ref=e6]

cmux browser surface:3 click e5 --snapshot-after
# Clicks "Sign In" and returns the updated accessibility tree
```

### Fill and submit a login form

```bash
cmux browser surface:3 fill "#email" --text "user@example.com"
cmux browser surface:3 fill "#password" --text "password123"
cmux browser surface:3 click "button[type='submit']" --snapshot-after
cmux browser surface:3 wait --text "Welcome"
```

### Search Google

```bash
cmux browser open-split https://www.google.com
cmux browser surface:3 wait --load-state complete --timeout-ms 5000
cmux browser surface:3 snapshot --interactive --compact
# Find the search combobox ref, e.g. e9
cmux browser surface:3 click e9
cmux browser surface:3 type e9 "cmux terminal"
cmux browser surface:3 press Enter
cmux browser surface:3 wait --load-state complete --timeout-ms 10000
cmux browser surface:3 snapshot --interactive --compact
```

### Debug JS errors on a page

```bash
cmux browser surface:3 errors list
cmux browser surface:3 console list
cmux browser surface:3 screenshot --out /tmp/debug.png
```

### Evaluate JavaScript

```bash
cmux browser surface:3 eval "document.querySelectorAll('.item').length"
# Output: 42

cmux browser surface:3 eval "JSON.stringify(window.__APP_STATE__)"
```

### Save and restore a browser session

```bash
# Save current state (cookies, storage, URL)
cmux browser surface:3 state save /tmp/session.json

# Later, restore it
cmux browser surface:3 state load /tmp/session.json
cmux browser surface:3 reload
```

## Reference

For the full command reference, see [`cmux-browser.md`](cmux-browser.md).

For official cmux documentation, visit [cmux.dev/docs/browser-automation](https://www.cmux.dev/docs/browser-automation).

## License

MIT
