---
name: frontend-animation
description: >
  Transform ANY concept, process, workflow, or system into an animated, interactive HTML
  demonstration. Trigger whenever the user wants to visualize something that has steps,
  flow, sequence, or cause-and-effect — regardless of domain.

  Trigger on: "animate this", "show me how this works", "make a visual", "create a demo",
  "explain this visually", "show the flow", "illustrate this concept", "walk me through",
  "make an interactive explainer", "build a simulation", "visualize this".

  Works for ANY domain — not limited to software or AI:
  - Finance: compound interest, loan amortization, options pricing, order book, portfolio rebalancing
  - Teaching / Education: sorting algorithms, Dijkstra's, how HTTP works, photosynthesis, supply & demand
  - Software Engineering: CI/CD pipeline, git branching, Docker build, database query plan, OAuth flow
  - AI / LLM: agent loops, RAG pipelines, tool-calling chains, fine-tuning steps
  - Science: cell division, DNA replication, Newton's laws simulation, chemical reactions
  - Business / Product: sales funnel, user onboarding, approval workflow, SLA escalation
  - Networking: TCP handshake, DNS resolution, CDN cache flow, load balancer routing
  - Everyday processes: how a coffee machine works, how a bill becomes a law, how bread rises

  If it can be broken into steps — it can be animated. Output is always a single
  self-contained HTML file with no external JS dependencies.
---

# Frontend Animation Skill

Turn any concept, process, or workflow into a polished, animated HTML demo. Domain-agnostic.
Everything that has steps, sequence, or cause-and-effect can become a live visual.

---

## Process Overview

1. **Identify the domain & narrative** — what story is being told?
2. **Map the flow** — stages, branches, loops, parallel tracks
3. **Choose layout tier** — single flow, multi-snippet, or simulation
4. **Assign timings** — paced for the viewer to follow, not for realism
5. **Choose visual metaphor** — chat window, pipeline, board, canvas, or ticker
6. **Build the HTML** — self-contained, vanilla JS only
7. **Self-review** — run every checklist item before saving

---

## Step 1 — Identify Domain & Narrative

Before mapping stages, answer:

- **What domain is this?** (finance, CS, biology, product, networking, …)
- **Who is the audience?** (student, developer, investor, general user)
- **What is the one thing they should understand after watching?**
- **What metaphor fits?** Does it feel like a chat, a conveyor belt, a ledger, a circuit, a classroom?

This shapes every design decision downstream.

**Domain → Visual metaphor quick-reference:**

| Domain | Natural metaphor | Accent color |
|---|---|---|
| AI / LLM / agent | Chat + execution trace | Indigo `#6366f1` |
| Software / DevOps | Pipeline / terminal | Slate `#475569` |
| Finance / trading | Ticker + ledger | Emerald `#059669` |
| Education / CS concepts | Whiteboard / step-by-step | Amber `#d97706` |
| Networking / protocols | Packet flow / handshake | Sky `#0ea5e9` |
| Science / biology | Diagram + labels | Teal `#0d9488` |
| Business / product | Kanban / funnel | Violet `#7c3aed` |
| Everyday / general | Story cards | Rose `#e11d48` |

---

## Step 2 — Map the Flow

Produce a named stage list before writing any code:

- **Trigger**: what starts the process? (user action, event, time, condition)
- **Stages**: every logical step in order, named concisely
- **Branches**: conditional paths with labels (`yes/no`, `hit/miss`, `approved/rejected`)
- **Loops**: repeat cycles with loop count
- **Parallel tracks**: concurrent operations (same timestamp, fan-out / fan-in)
- **Terminal state**: what does "done" look like? What changed?

**Example — compound interest (Finance):**
```
[Click: Calculate]
  → Validate Inputs             (instant)
  → Year 1: Apply 7% interest   (800 ms) — balance updates
  → Year 2: Apply 7% interest   (800 ms)
  → ...repeat for N years...
  → Plot Final Balance           (600 ms)
  → Show Growth Breakdown       (400 ms)
  → [Result: $X after N years]
```

**Example — bubble sort (Education):**
```
[Click: Sort]
  → Pass 1: Compare [5,3] → swap    (500 ms each pair)
  → Pass 1: Compare [5,8] → no swap
  → Pass 2: Compare [3,5] → no swap
  → ...until sorted...
  → Highlight sorted array          (300 ms)
  → [Result: sorted in N passes]
```

**Example — TCP handshake (Networking):**
```
[Click: Connect]
  → Client → SYN            (400 ms)
  → Server → SYN-ACK        (600 ms)
  → Client → ACK            (300 ms)
  → Connection Established  (200 ms)
  → [Data transfer begins]
```

**Example — OAuth 2.0 (Software):**
```
[Click: Login with Google]
  → Redirect to Auth Server     (300 ms)
  → User Grants Permission      (1 500 ms)
  → Auth Code Returned          (400 ms)
  → Token Exchange (backend)    (900 ms)
  → Access Token Stored         (200 ms)
  → [User logged in]
```

Write this list out before writing HTML.

---

## Step 3 — Choose Layout Tier

### Tier A — Single Flow (default)
One trigger → one execution → one result. Use unified chat/story window. No sidebar.
Best for: most explainers, single algorithm demos, API flows.

### Tier B — Multi-Snippet / Comparison
Multiple independent flows selectable via sidebar.
Best for: "sorting algorithm comparison", "HTTP vs HTTPS", "bull vs bear market".
- Left sidebar (220 px, fixed): snippet name cards. No code shown.
- Main area: one chat window per snippet. Selecting resets.

### Tier C — Parallel / Concurrent Tracks
Fan-out stages that run simultaneously.
Best for: distributed systems, multi-agent, parallel data processing.
- Show fan-out header row → nested parallel stages → fan-in row.
- All parallel stages start at the same time, complete independently.

### Tier D — Simulation / Canvas
Continuous, stateful visualization with controls (play/pause/step/reset).
Best for: sorting visualizers, physics simulations, market price movement, cellular automata.
- Replace the chat window with a `<canvas>` or DOM-based grid as the main area.
- Keep play/pause/step/reset controls at the bottom instead of an input bar.
- Show a stats panel (step count, comparisons, elapsed time, current value, etc.).

---

## Step 4 — Assign Timings

Timings are for the viewer, not for realism. Fast enough to hold attention, slow enough to follow.

| Operation type | Duration |
|---|---|
| Instantaneous (validation, flag set) | 80 – 200 ms |
| Fast computation (hash, parse, format) | 200 – 500 ms |
| Local I/O (read file, cache lookup) | 300 – 700 ms |
| Network / API call (fast) | 500 – 1 000 ms |
| Network / API call (slow, LLM) | 1 500 – 4 000 ms |
| Human-paced step (user reads, decides) | 1 000 – 2 000 ms |
| Visual reveal (chart render, highlight) | 400 – 800 ms |
| Simulation frame (sort swap, graph step) | 80 – 300 ms per frame |

- **Loops**: same duration per iteration — do not speed up.
- **Simulations**: use `requestAnimationFrame` or `setInterval` with consistent frame delay.
- **Cap total wall-clock time** at ~25 s for linear flows. Simulations may run until user pauses.
- Add ±15% random jitter per run so replays feel live.

---

## Step 5 — Build the Animated HTML

One self-contained HTML file. All CSS and JS inline. **Vanilla JS only** — no React, Vue,
Svelte, D3, Chart.js, or any external library. Google Fonts allowed via `<link>`.

---

### Layout A/B/C — Chat/Story Window (Critical CSS)

```
┌───────────────────────────────────┐
│  Header: title · domain · status  │  sticky top
├───────────────────────────────────┤
│                                   │
│  Feed (scrollable)                │  flex:1, overflow-y:auto
│    [welcome / context card]       │
│    → trigger bubble               │
│    → execution block              │
│    → result bubble                │
│    → replay button                │
│                                   │
├───────────────────────────────────┤
│  Input / trigger bar              │  flex-shrink: 0
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

`min-height: 0` is mandatory. Without it the input bar gets pushed off-screen.

---

### Layout D — Simulation / Canvas

```
┌───────────────────────────────────┐
│  Header: title · stats panel      │  sticky top
├───────────────────────────────────┤
│                                   │
│  Canvas / visualization area      │  flex:1
│    (bars, nodes, grid, chart…)    │
│                                   │
├───────────────────────────────────┤
│  Controls: Play · Pause · Step    │  flex-shrink: 0
│            Speed slider · Reset   │
└───────────────────────────────────┘
```

- Stats panel (top-right): domain-specific counters (comparisons, swaps, balance, step #, etc.)
- Speed slider: `50ms` → `500ms` per frame delay, default `150ms`
- Step button: advances exactly one frame while paused
- Reset: restores initial state without reloading the page

---

### Execution Block (Tier A/B/C)

```
┌─────────────────────────────────────────────┐
│  ● Processing…                   [hdr live] │
├─────────────────────────────────────────────┤
│ 📊  Validate Inputs     Done     ✓  ⌄      │  done, collapsible
│ 💰  Apply Year 1 Rate   Running… ···        │  active
│ 📈  Plot Growth         Waiting  –          │  pending
└─────────────────────────────────────────────┘
```

**Stage lifecycle:**
1. Row fade-in (0.25 s)
2. Pulsing 3-dot loader + status label
3. After duration: loader off → ✓ → "Done" → chevron → click to expand
4. Expanded: `<pre>` or `<table>` with realistic mock data for the domain
5. Next stage begins

**Exec header** (`#xlabel`): live stage label → summary (`"N steps · Ys · key result"`) when done.
**Loop badge**: amber pill `Loop 2`, `Loop 3` on repeated stages.
**Branch label**: teal pill `→ Cache Hit` or `→ Miss` on conditional paths.
**Parallel group**: fan-out header → nested rows → fan-in row.

---

### Scroll Rules (Tier A/B/C) — Must Not Break

**Rule 1** — Only `.feed` scrolls. No `scrollIntoView` anywhere.

**Rule 2** — Instant scroll on every append:
```js
function scrollNow() { feed.scrollTop = feed.scrollHeight; }
// call immediately after feed.appendChild(el)
```

**Rule 3** — Double-rAF for the final result bubble:
```js
function scrollSmooth() {
  requestAnimationFrame(() =>
    requestAnimationFrame(() =>
      feed.scrollTo({ top: feed.scrollHeight, behavior: 'smooth' })
    )
  );
}
// scrollSmooth() → only for the final result bubble (once)
// scrollNow()    → everything else
```

**Rule 4** — `behavior:'smooth'` only once per sequence. Stacked smooth scrolls cancel each other.

**Rule 5** — No `feedEnd` sentinel div. Use `feed.scrollHeight` directly.

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

| Token | Value | Default use |
|---|---|---|
| `--accent` | `#6366f1` indigo | AI / LLM |
| `--accent` | `#059669` emerald | Finance |
| `--accent` | `#d97706` amber | Education |
| `--accent` | `#0ea5e9` sky | Networking |
| `--accent` | `#475569` slate | DevOps / Systems |
| `--accent` | `#0d9488` teal | Science |
| `--accent` | `#7c3aed` violet | Business |
| `--accent` | `#e11d48` rose | General / Story |
| `--bg` | `#f9fafb` | shell background |
| `--surface` | `#ffffff` | cards, bubbles |
| `--muted` | `#6b7280` | secondary text |
| `--border` | `#e5e7eb` | dividers |

- **One accent color per demo.**
- **Fonts**: Inter (UI) · JetBrains Mono (code, numbers, payloads)
- No nav bars, footers, or chrome. The feed/canvas is the product.
- Bubbles / cards use full available width — never truncate.
- Mobile-friendly: single column at 380 px.
- Controls disabled while animation/simulation runs (except pause/step).
- Replay/Reset appears after animation ends — never before.
- No auto-play. Always starts on explicit user interaction.

---

### Mock & Simulation Data Quality

Make data feel real for the domain:

| Domain | Good mock data |
|---|---|
| Finance | `$12,847.23`, `AAPL 182.40 ▲1.2%`, `7.00% APY`, realistic P&L |
| Networking | `192.168.1.42`, `seq=1024 ack=1025`, `TTL=64`, `200 OK` |
| AI / LLM | model name, `prompt_tokens: 312`, `completion_tokens: 87`, truncated output |
| Sorting | actual array values mutating step by step, swap count, comparisons |
| Auth / OAuth | `code=4/P7q...`, `access_token=ya29...`, realistic expiry timestamps |
| Science | proper unit labels (`6.022×10²³`, `pH 7.4`, `37°C`), element symbols |
| Business | named stages (`"Sent to Legal"`, `"VP Approved"`), SLA countdowns |

- Never use `...`, `lorem ipsum`, `[value]`, or `TODO` as mock content.
- Simulation data should mutate visibly each frame — no static placeholders.

---

## Step 6 — Self-Review Checklist

Re-read the HTML and check every item before saving.

### Layout
- [ ] `html, body { height: 100% }` present
- [ ] `.app` has `height: 100vh; display: flex; flex-direction: column`
- [ ] `.feed` has `flex: 1`, `overflow-y: auto`, **`min-height: 0`**
- [ ] `.input-bar` / controls row has `flex-shrink: 0`
- [ ] No other element has `overflow: auto/scroll`

### Scroll (Tier A/B/C only)
- [ ] `scrollNow()` = `feed.scrollTop = feed.scrollHeight` — no `scrollIntoView`
- [ ] `scrollSmooth()` uses double-rAF + `feed.scrollTo({behavior:'smooth'})`
- [ ] `scrollNow()` after every `feed.appendChild()`
- [ ] `scrollSmooth()` called only once — for the final result bubble
- [ ] No `behavior:'smooth'` inside stage loop
- [ ] No feedEnd div

### Animation / Simulation
- [ ] Every stage from Step 2 is present
- [ ] Active stage shows loader + status text
- [ ] Completed stages: ✓, "Done", chevron, expandable detail
- [ ] One active stage at a time (or correct parallel group logic)
- [ ] Loop stages have amber badge; branch stages have teal badge
- [ ] Exec header updates live; shows summary when done
- [ ] Simulation (Tier D): play/pause/step/reset/speed controls all work
- [ ] Simulation stats panel updates every frame

### Interaction
- [ ] Animation/simulation starts only on explicit user click
- [ ] Controls disabled while `busy = true` (except pause/step in Tier D)
- [ ] Re-enabled after completion
- [ ] Replay/Reset appears after final result
- [ ] Expandable stage rows work

### Content & Polish
- [ ] No placeholder mock data
- [ ] Accent color matches domain
- [ ] Domain-appropriate emoji/icons on stage rows
- [ ] No split panels for single-flow demos
- [ ] Fully self-contained (no external JS)
- [ ] Mobile layout works at 380 px

Fix any failing items before saving.

---

## Output

Save as `<topic-name>_animation.html` in the user's working directory.
In sandboxed environments: `/mnt/user-data/outputs/<topic-name>_animation.html`.
State the file path clearly. Do not paste raw HTML in chat.
