---
name: frontend-animation
description: >
  Transform code snippets, workflow descriptions, or architecture diagrams into animated,
  interactive HTML UI demonstrations. Trigger when the user wants to visualize code execution,
  animate a technical workflow, create a live demo for an API flow, agent loop, RAG pipeline,
  multi-step system, or any sequential/parallel process — even vague requests like "animate this",
  "show me how this works", "make a visual for this", "create a demo UI", or "show the flow".
  Also trigger for: LLM agent loops, tool-calling chains, database query flows, microservice
  request traces, CI/CD pipelines, and data processing pipelines.
  Output is always a single self-contained HTML file with no external JS dependencies.
---

# Frontend Animation Skill

Turn code or workflow descriptions into polished, animated HTML demos that feel like watching
the system run live — everything in one unified chat-style window.

---

## Process Overview

1. **Map the flow** — identify every execution stage, branches, and loops
2. **Choose layout** — single vs multi-snippet, complexity tier
3. **Assign mock timings** — believable durations per stage type
4. **Build the HTML** — single self-contained file, unified chat window
5. **Self-review** — run every checklist item before saving

---

## Step 1 — Map the Flow

Read the code or description and produce a named stage list before writing any code:

- **Entry point**: what user action triggers everything? (send button, form submit, CLI run)
- **Stages**: every logical step in order, named concisely
- **Parallel fans**: concurrent operations (show as indented, same timestamp)
- **Branches/loops**: conditional paths, retry loops
- **Terminal state**: what "done" looks like

**Example — LLM agent with tool calls:**
```
[Click: Send Message]
  → LLM Call ①               (3 000 ms)
  → Parse Response            (300 ms)   — tool call found?
  → Tool Dispatch: get_order  (900 ms)
  → Append Result             (200 ms)
  → LLM Call ②  [Loop 2]     (2 500 ms)
  → Parse Final Response      (300 ms)
  → [Assistant reply rendered]
```

**Example — RAG pipeline:**
```
[Click: Ask Question]
  → Embed Query               (400 ms)
  → Vector Search             (600 ms)
  → Rerank Results            (500 ms)
  → Context Assembly          (200 ms)
  → LLM Generation            (3 500 ms)
  → [Answer rendered]
```

Write this list out before writing HTML.

---

## Step 2 — Choose Layout & Complexity Tier

### Tier A — Single Flow (default)
One prompt → one execution → one answer. Use unified chat window (no sidebar).

### Tier B — Multi-Snippet
Multiple independent flows selectable via sidebar. Each has its own chat window.
- Left sidebar (220 px, fixed): snippet name cards only. No code.
- Main area: unified chat window per snippet. Selecting resets the chat.

### Tier C — Parallel / Concurrent Stages
Fan-out stages that run simultaneously. Show as a nested indented group within the exec block,
with a "Fan-out" header row. All start at the same time, each has its own loader, they complete
in order of their individual durations. A "Fan-in" row appears after all complete.

---

## Step 3 — Assign Mock Timings

| Stage type                    | Mock duration     |
|-------------------------------|-------------------|
| LLM / AI inference            | 2 500 – 4 000 ms  |
| Vector search / DB query      | 500 – 1 000 ms    |
| External API call             | 800 – 1 500 ms    |
| Tool execution (local)        | 400 – 900 ms      |
| Embedding / reranking         | 300 – 700 ms      |
| Message formatting / parsing  | 150 – 400 ms      |
| Instantaneous state change    | 80 – 150 ms       |

- Loops repeat at the same durations — do not speed them up.
- Cap total animation wall-clock time at ~20 s.
- Add ±15% random jitter per run so replays feel live.

---

## Step 4 — Build the Animated HTML

One self-contained HTML file. All CSS and JS inline. Vanilla JS only — no React, Vue, Svelte,
or any framework. Google Fonts allowed via `<link>` (Inter + JetBrains Mono).

---

### Layout — Critical CSS (copy exactly)

```
┌───────────────────────────────────┐
│  Header: agent name · status      │  position: fixed top  (or sticky)
├───────────────────────────────────┤
│                                   │
│  Feed (flex-col, overflow-y:auto) │  flex:1, scrollable
│    [welcome state]                │
│    → user bubble                  │
│    → exec block (stages)          │
│    → assistant bubble             │
│    → replay button                │
│                                   │
├───────────────────────────────────┤
│  Input bar: textarea + send btn   │  flex-shrink: 0
└───────────────────────────────────┘
```

```css
html, body { height: 100%; margin: 0; }
.app {
  display: flex; flex-direction: column;
  height: 100vh;
  max-width: 760px; margin: 0 auto;
}
.feed {
  flex: 1;
  overflow-y: auto;
  min-height: 0;        /* REQUIRED — prevents flex overflow */
  padding: 20px 20px 12px;
  display: flex; flex-direction: column; gap: 4px;
}
.input-bar { flex-shrink: 0; }
```

`min-height: 0` on `.feed` is mandatory. Without it flexbox will not constrain feed height
and the input bar gets pushed off-screen.

---

### Execution Block

```
┌─────────────────────────────────────────────┐
│  ● Routing query…                [hdr live] │
├─────────────────────────────────────────────┤
│ 🤖  LLM Call           Sending…      ···   │  active (loading)
│ 🔍  Parse Response     Done        ✓  ⌄   │  done, collapsed
│ 🔧  Tool Dispatch      Done        ✓  ⌄   │
│ 📎  Append Result      Done        ✓  ⌄   │
│ 🤖  LLM Call  Loop 2   Sending…      ···   │  active
└─────────────────────────────────────────────┘
```

**Stage lifecycle:**
1. Row fade-in (0.25 s)
2. Pulsing 3-dot loader + status label on the right
3. After `duration` ms: loader hides → green ✓ → status "Done" → chevron appears
4. Row collapses to single line; click chevron to expand/collapse
5. Expanded view: `<pre>` block with realistic mock payload
6. Next stage begins

**Exec block header** (`#xlabel`): updates live with each stage's label.
After all stages done: `"N steps · X LLM calls · Y tool calls"`, dot turns green.

**Loop badge**: amber pill `Loop 2`, `Loop 3` on repeated stages.

**Parallel group (Tier C only):**
```
│ ⇶  Fan-out (3 parallel)                    │  header row
│    🔍  Vector Search   Searching…   ···    │
│    📊  Analytics       Querying…    ···    │
│    🗄️  Cache Check     Done       ✓  ⌄   │
│ ⇉  Fan-in              Done       ✓       │  appears when last parallel finishes
```

---

### Scroll Rules — Must Not Be Broken

**Rule 1 — One scroll owner.**
Only `.feed` scrolls. No other element gets `overflow`, `scroll`, or `scrollIntoView`.

**Rule 2 — Instant scroll on append.**
```js
function scrollNow() {
  feed.scrollTop = feed.scrollHeight;
}
```
Call `scrollNow()` immediately after every `feed.appendChild(el)`.
Never use `scrollIntoView` — it fights other scroll calls and freezes the viewport.

**Rule 3 — Double-rAF for the final bubble.**
```js
function scrollSmooth() {
  requestAnimationFrame(() =>
    requestAnimationFrame(() =>
      feed.scrollTo({ top: feed.scrollHeight, behavior: 'smooth' })
    )
  );
}
// scrollSmooth() → only the final assistant bubble and replay button
// scrollNow()    → everything else
```

**Rule 4 — No stacked smooth scrolls.**
`behavior: 'smooth'` only once per sequence. Multiple smooth scrolls cancel each other,
leaving the viewport stuck mid-animation (e.g. "Loop 2 stuck in view").

**Rule 5 — No feedEnd sentinel div.**
Do not use a scroll-anchor div. Use `feed.scrollHeight` directly.

---

### Loading Dots Component

```html
<div class="ld"><span></span><span></span><span></span></div>
<style>
.ld { display:inline-flex; gap:3px; align-items:center; }
.ld span {
  width:5px; height:5px; border-radius:50%; background:var(--accent);
  animation:ldp 1s ease-in-out infinite;
}
.ld span:nth-child(2){animation-delay:.15s}
.ld span:nth-child(3){animation-delay:.30s}
.ld.off { display:none; }
@keyframes ldp{0%,80%,100%{transform:scale(.5);opacity:.3}40%{transform:scale(1);opacity:1}}
</style>
```

---

### Design System

| Token         | Value                    | Use                        |
|---------------|--------------------------|----------------------------|
| `--accent`    | `#6366f1` (indigo)       | AI / LLM flows (default)   |
| `--accent`    | `#16a34a` (green)        | data pipelines             |
| `--accent`    | `#7c3aed` (purple)       | agent / orchestration      |
| `--accent`    | `#0ea5e9` (sky)          | API / microservice flows   |
| `--bg`        | `#f9fafb`                | shell background           |
| `--surface`   | `#ffffff`                | cards, bubbles             |
| `--muted`     | `#6b7280`                | secondary text             |
| `--border`    | `#e5e7eb`                | dividers                   |

- **One accent color per demo.** Pick based on flow type above.
- **Fonts**: Inter (UI text) · JetBrains Mono (code blocks, mock payloads)
- No nav bars, footers, or decorative chrome.
- Chat bubbles use full available width — never truncate.
- Mobile-friendly: single column at 380 px viewport.
- Input bar: textarea + send button. Both disabled while `busy = true`.
- Replay/Reset button appears in feed after animation ends, never before.
- No auto-play. Animation starts only on explicit user click.

---

### Mock Data Quality

Payloads shown in expanded stage rows must be realistic:

- **IDs**: `ORD-7821`, `call_Ax9k2`, `SUP-1042`, `req_8fn2x`, `vec_3f9a1`
- **LLM responses**: include `model`, `usage.prompt_tokens`, `usage.completion_tokens`, truncated `content`
- **Tool results**: valid JSON matching the code's actual field names — not generic placeholders
- **Vector search**: include `score`, `chunk_id`, `metadata.source` fields
- **Timestamps**: ISO-8601 with realistic offsets, not `2024-01-01T00:00:00Z`
- Never use `...`, `lorem ipsum`, `TODO`, or `[data]` as mock content
- Add ±5% variation in token counts between runs for realism

---

## Step 5 — Self-Review Checklist

Re-read the generated HTML and verify every item. Do not skip this step.

### Layout
- [ ] `html, body { height: 100% }` present
- [ ] `.app` has `height: 100vh` and `display: flex; flex-direction: column`
- [ ] `.feed` has `flex: 1`, `overflow-y: auto`, and **`min-height: 0`**
- [ ] `.input-bar` has `flex-shrink: 0`
- [ ] No other element has `overflow: auto` or `overflow: scroll`

### Scroll
- [ ] `scrollNow()` uses `feed.scrollTop = feed.scrollHeight` — no `scrollIntoView` anywhere
- [ ] `scrollSmooth()` uses double-`requestAnimationFrame` + `feed.scrollTo({behavior:'smooth'})`
- [ ] `scrollNow()` called after every `feed.appendChild()`
- [ ] `scrollSmooth()` called only once — for the final assistant bubble
- [ ] No `behavior:'smooth'` inside the stage animation loop
- [ ] No `feedEnd` sentinel div

### Animation
- [ ] Every stage from Step 1 is present in the exec block
- [ ] Each active stage shows loading dots + status text
- [ ] Completed stages: loader off, ✓ shown, "Done" text, chevron present, click expands
- [ ] Only one stage active at a time (Tier A/B); or correct parallel group logic (Tier C)
- [ ] Loop stages have amber `Loop N` badge
- [ ] Exec block header updates live; shows step summary when done

### Interaction
- [ ] Animation starts only on send button click
- [ ] Input + send button disabled while `busy = true`
- [ ] Re-enabled after animation ends
- [ ] Replay/Reset button appears after final bubble
- [ ] Completed stage rows are click-to-expand/collapse

### Content
- [ ] No `...` or placeholder mock data
- [ ] All bubbles readable at full width
- [ ] No split panels for single-snippet demos
- [ ] File is fully self-contained (no external JS)
- [ ] Accent color matches flow type

Fix any failing items before saving.

---

## Output

Save to the user's working directory as `<workflow-name>_animation.html`.
If running in a sandboxed environment, save to `/mnt/user-data/outputs/<workflow-name>_animation.html`.
Present the file path clearly. Do not paste raw HTML in chat.
