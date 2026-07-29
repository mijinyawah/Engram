# [Project Name]

> [One-sentence description of what this project is and what it produces.] Last updated: [YYYY-MM-DD].

<!-- SIZE BUDGET: this file stays under ~150 lines. It holds STATE — what is true right now.
     Session history and narratives go in LOG.md (append-only, same folder).
     At session end the assistant compacts this file; it never accumulates history here. -->

---

## State

<!-- The first thing read at session start. Keep it scannable — no prose paragraphs. -->

- **Status:** [🔵 WIP / 🟢 Live / ✅ Complete / ⏸️ On Hold / 📦 Shelved]
- **Phase:** [Phase name or number — one line]
- **Blockers:** [Blocker, or "None"]
- **Next up:**
  1. [Most important next action]
  2. [Second]
  3. [Third]

---

## Overview

- **Project ID:** [e.g. CL-A01, or any identifier you use]
- **Type:** [Tool / Website / Video / Design / Experiment / Other]
- **Owner:** [Your name]
- **Started:** [YYYY-MM-DD]

[2–4 sentences describing the project: what problem it solves, who it's for, and what success looks like for v1. This rarely changes.]

---

## Tech Stack

<!-- The assistant removes this section (and Key Files / How to Run) automatically at kickoff
     if the project isn't technical — see /new-project. Don't leave it for the user to delete. -->

| Layer | Tool | Notes |
|-------|------|-------|
| [e.g. Framework] | [e.g. SvelteKit] | [e.g. Client-only, no SSR needed] |
| [e.g. Hosting] | [e.g. Vercel] | |

### Key Files

- `[path/to/file]` — [what it does]

### How to Run

<!-- Give commands in the user's actual shell (see root CLAUDE.md → Environment). Don't default to bash syntax. -->

```
[install / dev commands, in the user's shell]
```

---

## Conventions

<!-- Rules the assistant should follow when working on this project. -->

- [e.g. TypeScript-first with strict interfaces]
- [e.g. No external dependencies without explicit approval]

---

## Decisions That Still Matter

<!-- Only decisions that actively constrain current work — not a history of every choice.
     Full decision narratives live in LOG.md. Prune entries when they stop mattering. -->

| Date | Decision | Why it still matters |
|------|----------|----------------------|
| [YYYY-MM-DD] | [What was decided] | [The constraint it imposes today] |

---

## Known Issues & Gotchas

<!-- Things that are tricky, fragile, or non-obvious. Prune when fixed. -->

- [Issue or gotcha]

---

## Open Questions

<!-- Unresolved decisions. When answered: move to Decisions (if still constraining) or LOG.md (if just history). -->

- [ ] [Question]

---

## Next Session

<!-- Rewritten completely at session end. What to pick up, what's mid-flight, context needed. -->

[To be filled at session end.]

---

## Links & Resources

- [Label](URL or path)
