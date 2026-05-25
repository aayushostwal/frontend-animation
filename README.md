# frontend-animation

A Claude Code skill that turns code snippets and workflow descriptions into animated, interactive HTML demos.

---

## Overview

Give it any code or system description — LLM agent loop, RAG pipeline, API flow, CI/CD chain, microservice trace — and it generates a single self-contained HTML file that animates the execution step-by-step, like watching the system run live.

No external JS dependencies. Pure HTML + CSS + vanilla JS.

---

## How It Works

1. **Maps the flow** — extracts every stage, branch, loop, and parallel fan from your code or description
2. **Assigns timings** — applies realistic mock durations per stage type (LLM call, DB query, tool execution, etc.)
3. **Builds a chat-style UI** — unified window with user bubble → live execution block → assistant reply
4. **Self-reviews** — runs layout, scroll, animation, and content checklists before saving

---

## What It Generates

- Single `.html` file, fully self-contained
- Animated execution block with per-stage loaders, status updates, and collapsible detail panels
- Realistic mock payloads (no lorem ipsum or `...` placeholders)
- Replay/Reset button after each run
- Responsive, mobile-friendly layout

---

## Supported Flow Types

| Flow | Accent |
|------|--------|
| LLM / AI agent loops | Indigo |
| RAG pipelines | Indigo |
| Data processing pipelines | Green |
| Agent / orchestration | Purple |
| API / microservice traces | Sky blue |

---

## Installation

Copy `SKILL.md` into your Claude Code skills directory (`.claude/skills/` or the path configured in your `settings.json`).

---

## Usage

Trigger phrases (Claude picks this skill automatically):

> "animate this", "show me how this works", "make a visual for this", "create a demo UI", "show the flow"

Or just paste code + say what you want visualized.

---

## Output

Saved as `<workflow-name>_animation.html` in your working directory (or `/mnt/user-data/outputs/` in sandboxed environments).
