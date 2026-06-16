---
name: web-builder
description: Build self-contained static web deliverables — doc sites, landing pages, dashboards, interactive tools. Also supports live consoles with a local Python bridge (Tier 2). Zero framework, zero build step. Fully self-contained. Triggers: 网页, 文档站, 落地页, 仪表盘, 表单, 控制台, 终端, SSH
type: skill
---

# Web Builder

Builds two tiers of web deliverables. Pick the tier first, then follow the matching section below.

## Quick Reference

| Item | Location |
|------|----------|
| Visual baseline | [Shared Visual Baseline](#shared-visual-baseline) |
| Tier 1 types | [Doc Site](#tier-1-type-doc-site) · [Landing Page](#tier-1-type-landing-page) · [Dashboard](#tier-1-type-dashboard) · [Interactive Tool](#tier-1-type-interactive-tool) |
| Tier 2 live console | [Tier 2 Live Console](#tier-2-live-console) |
| Failure modes | [Failure Modes](#failure-modes) |
| What NOT to do | [What NOT to do](#what-not-to-do) |

---

## Workflow

Apply these steps in order. Each 🔴 checkpoint is a mandatory stop — do not proceed until the condition is met.

**1. Clarify** — Ask the user: deliverable type? content source? live data needed? deploy target?
   🔴 **Checkpoint**: Rephrase your understanding to the user and confirm before building. If any answer is unclear, ask one question at a time (do not dump all questions at once).

**2. Decide tier + type**
   - Tier 1 (static): doc site / landing page / dashboard / interactive tool
   - Tier 2 (live console): requires Python + paramiko or sshpass, binds to `127.0.0.1:9099`, **NO authentication** — dev/debug use only
   🔴 **Checkpoint**: State the chosen tier to the user explicitly. Never default to Tier 2 without user confirmation — Tier 2 involves a local process and security caveats.

**3. Scaffold** — Create the deliverable files (single HTML, or HTML + separate JS/CSS if complex)
   🔴 **Checkpoint**: All files exist before writing page logic.

**4. Build page** — Apply visual baseline + type-specific patterns
   🔴 **Checkpoint**: For iterative changes, re-read existing files first — do not rebuild from scratch.

**5. Preview locally** — Run `python3 -m http.server 8077 --bind 127.0.0.1`
   🔴 **Checkpoint**: If `python3` not found — try `python`. If port 8077 occupied — try 8078, 8079.

**6. Self-check** — Walk the [Pre-Delivery Self-Check](#pre-delivery-self-check) list
   🔴 **Checkpoint**: All items checked before delivery.

**7. Deliver** — Save files to the user's workspace and present via `present_files`
   🔴 **Final Checkpoint**: Confirm with user the deliverable is ready.

---

## Shared Visual Baseline

Every deliverable inherits these defaults unless the user specifies otherwise.

### CSS Custom Properties

```css
:root {
  --font-sans: 'Inter', 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  --font-mono: 'JetBrains Mono', 'SF Mono', 'Fira Code', 'Cascadia Code', monospace;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;
  --font-size-xl: 1.5rem;
  --font-size-2xl: 2rem;
  --line-height-relaxed: 1.7;
  --color-bg: #fafafa;
  --color-surface: #ffffff;
  --color-text: #171717;
  --color-text-secondary: #737373;
  --color-border: #e5e5e5;
  --color-accent: #2563eb;
  --color-accent-hover: #1d4ed8;
  --radius-sm: 6px;
  --radius-md: 8px;
  --radius-lg: 12px;
  --shadow-sm: 0 1px 2px rgba(0,0,0,.05);
  --shadow-md: 0 4px 6px -1px rgba(0,0,0,.07);
  --content-max-width: 960px;
}
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #0a0a0a;
    --color-surface: #171717;
    --color-text: #f5f5f5;
    --color-text-secondary: #a3a3a3;
    --color-border: #262626;
    --color-accent: #3b82f6;
    --color-accent-hover: #60a5fa;
  }
}
```

### Layout Skeleton

```css
body {
  font-family: var(--font-sans);
  background: var(--color-bg);
  color: var(--color-text);
  line-height: var(--line-height-relaxed);
  margin: 0;
  padding: 0;
}
```

For content pages, use max-width + centered layout:
```css
.content { max-width: var(--content-max-width); margin: 0 auto; padding: 2rem 1.5rem; }
```

### Scroll-Spy Sidebar Navigation

For doc sites: a sticky sidebar with scroll-spy highlighting. Add `data-section` attributes to headings and use `IntersectionObserver` to highlight the corresponding nav link.

### Accessibility

- All interactive elements are keyboard-focusable and have visible focus rings
- Images have `alt` text
- Forms have `<label>` elements (not placeholders as labels)
- Color contrast ratio ≥ 4.5:1
- Use semantic HTML (`<nav>`, `<main>`, `<article>`, `<aside>`, `<footer>`, `<button>`, `<table>`)

### Pre-Delivery Self-Check

- [ ] Opens correctly from `file://` or `http://localhost`
- [ ] No 404s on assets, fonts, or icons
- [ ] Responsive: check at 375px, 768px, 1280px
- [ ] Dark mode renders correctly if supported
- [ ] All links / buttons navigate or perform an action
- [ ] No JS console errors
- [ ] Content is scrollable (no hidden overflow clipping content)
- [ ] Fonts render without FOIT (use `font-display: swap`)

---

## Tier 1: Type-Specific Patterns

### Tier 1 Type: Doc Site

**Structure**: Sidebar navigation (left) + content area. Sidebar is sticky, collapsible on mobile via hamburger toggle.

**Sidebar**: `<nav>` with links, nested lists for hierarchy. Active page highlighted. On mobile: overlay panel triggered by hamburger button.

**Content**: Markdown-rendered sections. Add `data-section="section-name"` to `<section>` elements for scroll-spy.

**Tables**: Striped rows, left-aligned headers, `overflow-x: auto` wrapper.

**Code blocks**: Syntax-highlighted `<pre><code>`, copy-to-clipboard button on hover, monospace font stack.

### Tier 1 Type: Landing Page

**Structure**: Hero → Features → How it works → Testimonials/Social proof → CTA → Footer.

**Hero**: Large heading (2.5-3rem), supporting subtitle (1.25rem), primary CTA button, optional background gradient or subtle pattern.

**Features**: Three-column grid (responsive → 2-col → 1-col). Icon + heading + description per card.

**CTA**: Full-width section with contrasting background, large heading, single prominent button.

**Footer**: Minimal — links, copyright, social icons.

### Tier 1 Type: Dashboard

**Structure**: Full-bleed layout with toolbar (top) + card grid.

**Toolbar**: Title left, time-range selector right, auto-refresh toggle.

**Cards**: `display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr))` with gap. Each card: header (title + optional status indicator) + body (chart / stat / list) + optional footer.

**Data loading**: Show skeleton placeholders while loading, fade in content when ready. Handle empty state ("No data yet") and error state with retry button.

### Tier 1 Type: Interactive Tool

**Structure**: Single-page app with controls area + output area.

**Controls**: Grouped logically with fieldset/legend. Use appropriate input types (range sliders for numeric, selects for options, checkboxes for toggles).

**State**: URL query params for shareable state (`new URLSearchParams(window.location.search)`). Update URL on change without page reload.

**Output**: Reacts instantly on input change (no submit button unless it's a search/query tool).

---

## Tier 2 Live Console

Build this only when the user explicitly confirms they need local command execution.

### Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Browser (user-facing HTML)          │
│  Single HTML file on http://localhost:8077            │
│  Sends fetch() POST to http://127.0.0.1:9099         │
└─────────────────────────────────────────────────────┘
                        ↕ fetch /api/run
┌─────────────────────────────────────────────────────┐
│            Python Bridge (exec_bridge.py)            │
│  Flask server on 127.0.0.1:9099                       │
│  Executes commands via subprocess / paramiko SSH      │
│  No auth — dev/debug only                             │
└─────────────────────────────────────────────────────┘
```

### Python Bridge

Create `exec_bridge.py`:

```python
#!/usr/bin/env python3
"""exec_bridge.py — Minimal command execution bridge. Dev only, no auth."""
import subprocess, json, shlex
from flask import Flask, request, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route("/api/run", methods=["POST"])
def run_command():
    data = request.get_json(force=True)
    cmd_str = data.get("command", "")
    if not cmd_str.strip():
        return jsonify({"error": "empty command"}), 400
    # Block dangerous commands
    blocked = ["rm -rf /", "mkfs", "dd if=", ":(){ :|:& };:"]
    for b in blocked:
        if b in cmd_str:
            return jsonify({"error": "command blocked for safety"}), 403
    try:
        result = subprocess.run(
            cmd_str, shell=True, capture_output=True, text=True, timeout=30
        )
        return jsonify({
            "stdout": result.stdout,
            "stderr": result.stderr,
            "returncode": result.returncode
        })
    except subprocess.TimeoutExpired:
        return jsonify({"error": "command timed out after 30s"}), 408
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == "__main__":
    app.run(host="127.0.0.1", port=9099, debug=False)
```

### HTML — Terminal Emulator

Vanilla JS terminal in the browser:

```javascript
// Session store (in-memory — not localStorage)
const sessionStore = (() => {
  let state = '';
  return { get: () => state, set: (v) => { state = v; } };
})();

const term = document.getElementById('terminal');
const input = document.getElementById('input-line');

input.addEventListener('keydown', async (e) => {
  if (e.key !== 'Enter') return;
  const cmd = input.textContent.trim();
  if (!cmd) return;
  appendLine(`$ ${cmd}`);
  input.textContent = '';
  try {
    const res = await fetch('http://127.0.0.1:9099/api/run', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ command: cmd })
    });
    const data = await res.json();
    if (data.stdout) appendLine(data.stdout);
    if (data.stderr) appendLine(data.stderr, 'stderr');
    if (data.error) appendLine(`Error: ${data.error}`, 'error');
  } catch (err) {
    appendLine(`Connection failed: ${err.message}`, 'error');
  }
  term.scrollTop = term.scrollHeight;
});
```

### How to Start

```bash
python3 exec_bridge.py &             # Terminal 1: start bridge
python3 -m http.server 8077 --bind 127.0.0.1   # Terminal 2: serve page
```

**Security reminder**: The bridge has **no authentication**. Anyone who can reach `127.0.0.1:9099` can run arbitrary commands. Never expose it to a network.

---

## Failure Modes

| Failure | Symptom | Fix |
|---------|---------|-----|
| `python3` not found | `command not found: python3` | Try `python` or `python3.11` |
| Flask missing | `ModuleNotFoundError: No module named 'flask'` | `pip install flask flask-cors --break-system-packages` |
| paramiko/sshpass missing | SSH commands fail | `pip install paramiko --break-system-packages` or `apt install sshpass` |
| Port in use | `Address already in use` | Try 8078, 8079 for http; 9100 for bridge |
| Blank page on open | Page shows nothing | Check for `file://` CORS issues — serve via http.server instead |
| Missing source files | 404 on linked resources | All assets must be inline or embedded in the single HTML |
| localStorage unsupported | JS errors in private browsing | Use in-memory store fallback (see terminal example above) |
| CORS errors | fetch() blocked | Bridge must set CORS headers or use `flask-cors` |

---

## What NOT to do

1. **Don't assume localStorage works everywhere** — some environments block it (private browsing, some embedded webviews). Always provide an in-memory fallback.
2. **Don't rebuild from scratch for iterative changes** — read the existing file first, then surgically edit.
3. **Don't default to Tier 2** — static HTML is the default. Tier 2 (live console with bridge) requires explicit user confirmation.
4. **Don't embed credentials in HTML/JS** — no API keys, tokens, or passwords in client-side code.
5. **Don't pull external JS/CSS from CDNs** — everything must be self-contained. Exception: a single web font import via `@import url(...)` is acceptable if the user wants a specific font.
6. **Don't assume `python3`** — check availability and fall back to `python` or `python3.x`.
7. **Don't skip the self-check list** — all items must be verified before delivering.
8. **Don't put business logic in Python** — the bridge runs raw shell commands. If the user needs business logic, implement it in the client-side JS, not in `exec_bridge.py`.
