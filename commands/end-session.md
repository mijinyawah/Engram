---
description: Save all session progress to permanent project records at the end of a session
argument-hint: ""
---

# /end-session — Session End

Save all session progress to permanent project records. Run at the end of every work session.

Core principle: **compact, don't accumulate.** History goes to LOG.md; CLAUDE.md describes what's true now.

---

## Flow

### 1. Identify the active project

Determine which project was worked on from conversation context. Locate `projects/[slug]/CLAUDE.md`.

**If no project can be identified** but the session produced or discussed real work (files created, a recurring idea, something the user asked to save), don't let it evaporate untracked: add a one-line breadcrumb to `memory/projects.md` under "Unassigned ideas (no ID yet)" summarizing what it was, then continue with the rest of end-session as normal (there's nothing else to compact). This is the same net the Project Capture agent uses — if that agent already logged a breadcrumb for this conversation, don't duplicate it.

### 2. Gather session context

Read `projects/[slug]/.session-checkpoints.md` if it exists — this has any mid-session saves.

Compile from checkpoints + conversation:
- What was accomplished
- Decisions made
- Things learned or discovered
- Open issues or risks
- What's still in progress

### 3. Append the session narrative to LOG.md

Open `projects/[slug]/LOG.md` (create from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-LOG.md` if missing). Append a new entry at the TOP:

```
## [YYYY-MM-DD] — Session [N]

**Did:** [bullets]
**Decided:** [bullets or "Nothing significant"]
**Learned / discovered:** [bullets]
**Left open:** [bullets]
```

This is the ONLY place session history lives. Never trim or rewrite past entries.

### 4. Compact project CLAUDE.md

Open `projects/[slug]/CLAUDE.md` and REWRITE (not append):

- **State** block at top — status, phase, blockers, next 3 actions, as of right now
- **Decisions That Still Matter** — add only decisions that constrain future work; PRUNE rows that no longer do (their history is safe in LOG.md)
- **Known Issues & Gotchas** — add new, remove fixed
- **Open Questions** — add new, remove resolved
- **Next Session** — rewrite completely: what to pick up, what's mid-flight, context needed
- **Last updated** date in the header

### 5. Size check

If `projects/[slug]/CLAUDE.md` now exceeds ~150 lines, move narrative/history content into LOG.md until it fits. If it's structurally impossible to fit (huge stack tables etc.), tell the user rather than silently overflowing.

### 6. Update memory/projects.md

Find the project's line. Update the status emoji if it changed:

- 🔵 WIP — still actively working
- 🟢 Live — shipped / in production
- ✅ Complete — finished, no further work planned
- ⏸️ On Hold — paused
- 📦 Shelved — abandoned but kept

Enforce the one-sentence rule: if the line has grown past one sentence, trim it — detail belongs in the project files. Update the `Last updated` line at the bottom.

### 7. Clean up checkpoints

If `.session-checkpoints.md` exists in the project folder, delete it — its contents are now in LOG.md/CLAUDE.md.

### 8. Confirm

Tell the user what was saved and where. Be brief — 3–5 lines.

---

## Notes

- Always write to `projects/[slug]/` files — never to the root `CLAUDE.md`
- The root `CLAUDE.md` holds identity/preferences and is only updated via `/setup` or `/migrate`
- If the session touched multiple projects, run the save flow for each one
