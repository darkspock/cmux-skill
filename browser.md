---
name: browser
description: Control the cmux integrated browser for navigating, inspecting, and interacting with web pages from the terminal. Use when the user asks to open, test, or verify a web page visually.
user_invocable: true
---

# cmux Browser Automation

You are running inside **cmux**, a native macOS terminal with an integrated WebKit browser.
Use the `cmux browser` CLI to open pages, interact with elements, and inspect state — all without leaving the terminal.

## Concepts

- **Surface**: Each split region has a surface ID (e.g. `surface:3`). Browser panes are surfaces.
- **Ref**: Accessibility-tree elements get a ref (e.g. `e9`). Use refs for `click`, `type`, `fill`, etc.
- **`--snapshot-after`**: Appended to mutating commands (`click`, `navigate`, `scroll`, `reload`) to return the accessibility tree after the action completes. Use this to verify results in one step.

## Workflow

1. **Open** a browser split: `cmux browser open-split <url>`
2. **Wait** for load: `cmux browser <surface> wait --load-state complete --timeout-ms 10000`
3. **Snapshot** to see elements: `cmux browser <surface> snapshot --interactive --compact`
4. **Interact** using refs or CSS selectors
5. **Verify** with `get`, `is`, `snapshot`, or `screenshot`

## Command Reference

### Opening & Targeting

```bash
# Open browser (new surface)
cmux browser open <url>

# Open browser in a split next to current terminal
cmux browser open-split <url>

# Discover surfaces
cmux browser identify

# Get current URL
cmux browser <surface> url
```

### Navigation

```bash
cmux browser <surface> navigate <url> [--snapshot-after]
cmux browser <surface> back
cmux browser <surface> forward
cmux browser <surface> reload [--snapshot-after]
cmux browser <surface> focus-webview
cmux browser <surface> is-webview-focused
```

### Waiting

Block until a condition is met. Always use `--timeout-ms`.

```bash
cmux browser <surface> wait --load-state complete --timeout-ms 15000
cmux browser <surface> wait --selector "#checkout" --timeout-ms 10000
cmux browser <surface> wait --text "Order confirmed"
cmux browser <surface> wait --url-contains "/dashboard"
cmux browser <surface> wait --function "window.__appReady === true"
```

### DOM Interaction

Use **refs** (from snapshot) or **CSS selectors**. Mutating commands support `--snapshot-after`.

```bash
cmux browser <surface> click <ref|selector> [--snapshot-after]
cmux browser <surface> dblclick <ref|selector>
cmux browser <surface> hover <ref|selector>
cmux browser <surface> focus <ref|selector>
cmux browser <surface> check <ref|selector>
cmux browser <surface> uncheck <ref|selector>
cmux browser <surface> scroll-into-view <ref|selector>

# Type character by character (triggers input events)
cmux browser <surface> type <ref|selector> "text"

# Set value directly (faster, for form fields)
cmux browser <surface> fill <ref|selector> --text "value"
cmux browser <surface> fill <ref|selector> --text ""  # clear field

# Keyboard
cmux browser <surface> press Enter
cmux browser <surface> keydown Shift
cmux browser <surface> keyup Shift

# Dropdowns
cmux browser <surface> select <ref|selector> "option-value"

# Scroll
cmux browser <surface> scroll --dy 800 [--snapshot-after]
cmux browser <surface> scroll --selector "#log-view" --dx 0 --dy 400
```

### Inspection

```bash
# Accessibility tree (primary tool for agents)
cmux browser <surface> snapshot --interactive --compact
cmux browser <surface> snapshot --selector "main" --max-depth 5

# Visual screenshot
cmux browser <surface> screenshot --out /tmp/cmux-page.png

# Get specific data
cmux browser <surface> get title
cmux browser <surface> get url
cmux browser <surface> get text "<selector>"
cmux browser <surface> get html "<selector>"
cmux browser <surface> get value "<selector>"
cmux browser <surface> get attr "<selector>" --attr href
cmux browser <surface> get count "<selector>"
cmux browser <surface> get box "<selector>"
cmux browser <surface> get styles "<selector>" --property color

# Check element state
cmux browser <surface> is visible "<selector>"
cmux browser <surface> is enabled "<selector>"
cmux browser <surface> is checked "<selector>"

# Find elements by role, text, label, etc.
cmux browser <surface> find role button --name "Continue"
cmux browser <surface> find text "Order confirmed"
cmux browser <surface> find label "Email"
cmux browser <surface> find placeholder "Search"
cmux browser <surface> find testid "save-btn"
cmux browser <surface> find first "<selector>"
cmux browser <surface> find last "<selector>"
cmux browser <surface> find nth 2 "<selector>"

# Highlight element visually
cmux browser <surface> highlight "<selector>"
```

### JavaScript Evaluation

```bash
cmux browser <surface> eval "document.title"
cmux browser <surface> eval --script "window.location.href"
cmux browser <surface> addinitscript "window.__cmuxReady = true;"
cmux browser <surface> addscript "document.querySelector('#name')?.focus()"
cmux browser <surface> addstyle "#debug-banner { display: none !important; }"
```

### Cookies, Storage & Session State

```bash
# Cookies
cmux browser <surface> cookies get
cmux browser <surface> cookies get --name session_id
cmux browser <surface> cookies set <name> <value> --domain <domain> --path /
cmux browser <surface> cookies clear --name <name>
cmux browser <surface> cookies clear --all

# Local/session storage
cmux browser <surface> storage local set <key> <value>
cmux browser <surface> storage local get <key>
cmux browser <surface> storage local clear
cmux browser <surface> storage session set <key> <value>
cmux browser <surface> storage session get <key>

# Full session save/restore
cmux browser <surface> state save /tmp/cmux-browser-state.json
cmux browser <surface> state load /tmp/cmux-browser-state.json
```

### Tabs

```bash
cmux browser <surface> tab list
cmux browser <surface> tab new <url>
cmux browser <surface> tab switch <index|surface:N>
cmux browser <surface> tab close [surface:N]
```

### Console & Errors

```bash
cmux browser <surface> console list
cmux browser <surface> console clear
cmux browser <surface> errors list
cmux browser <surface> errors clear
```

### Dialogs

```bash
cmux browser <surface> dialog accept ["prompt text"]
cmux browser <surface> dialog dismiss
```

### Frames (iframes)

```bash
cmux browser <surface> frame "<iframe-selector>"   # enter iframe
# ... interact with iframe content ...
cmux browser <surface> frame main                    # exit iframe
```

### Downloads

```bash
cmux browser <surface> click "a#download-report"
cmux browser <surface> download --path /tmp/report.csv --timeout-ms 30000
```

## Common Patterns

### Open dev server and verify

```bash
cmux browser open-split http://localhost:3000
# note the surface ID from output, e.g. surface:3
cmux browser surface:3 wait --load-state complete --timeout-ms 10000
cmux browser surface:3 snapshot --interactive --compact
cmux browser surface:3 get title
```

### Fill a form and submit

```bash
cmux browser <surface> fill "#email" --text "user@example.com"
cmux browser <surface> fill "#password" --text "secret"
cmux browser <surface> click "button[type='submit']" --snapshot-after
cmux browser <surface> wait --text "Welcome"
```

### Debug a failure

```bash
cmux browser <surface> console list
cmux browser <surface> errors list
cmux browser <surface> screenshot --out /tmp/failure.png
cmux browser <surface> snapshot --interactive --compact
```

### Save and restore session

```bash
cmux browser <surface> state save /tmp/session.json
# ... later ...
cmux browser <surface> state load /tmp/session.json
cmux browser <surface> reload
```

## General cmux Commands (non-browser)

These are useful for managing workspaces and splits alongside browser automation.

```bash
# Workspaces
cmux list-workspaces
cmux new-workspace [--name NAME]
cmux select-workspace <id>
cmux close-workspace [<id>]

# Splits & surfaces
cmux new-split [--direction left|right|up|down]
cmux list-surfaces
cmux focus-surface <id>

# Send input to surfaces
cmux send "text"
cmux send-surface <surface> "text"
cmux send-key enter|tab|escape

# Notifications
cmux notify --title "Title" --body "Body"
cmux list-notifications

# Sidebar status & progress
cmux set-status --key <key> --value <value>
cmux set-progress 0.75    # 0.0 to 1.0
cmux log --level success "Done!"
cmux sidebar-state
```

## Tips

- Always use `snapshot --interactive --compact` to discover refs before interacting
- Use `--snapshot-after` on mutating commands to avoid a separate snapshot call
- Use `wait` before inspecting — pages may not be fully loaded
- Refs change after navigation or DOM mutations — re-snapshot if stale
- `type` sends keystrokes one by one (triggers events); `fill` sets the value directly (faster)
- `screenshot` saves a PNG for visual verification; `snapshot` returns structured text
