---
name: session-management
description: >
  Use this skill when working with the start-session plugin's workspace structure,
  file conventions, and session rules. Triggers on: starting a session, ending a session,
  checkpointing progress, working with project files, reading CLAUDE.md or memory files,
  or when the user asks how memory or tracking works.
version: 2.2.0
---
# Session Management Skill

This skill gives you full context on the start-session workspace structure, file conventions, and hard rules. Load it when session-related topics arise — starting a session, ending a session, working with project files, or when a user asks about how memory or tracking works.

---

## Workspace structure

```
[working directory]/
├── CLAUDE.md                          ← primary context file (identity, tools, prefs, rules, agents)
├── AGENTS.md                          ← cross-tool pointer → CLAUDE.md (Codex CLI, Cursor, Windsurf, Copilot, etc.)
├── GEMINI.md                          ← Gemini CLI pointer → CLAUDE.md (Gemini looks for this name, not AGENTS.md)
├── .start-session-version             ← plugin version this workspace was last synced to (written by setup/migrate)
├── memory/
│   ├── projects.md                    ← single source of truth for all projects
│   └── glossary.md                    ← terms, acronyms, shorthand
├── templates/
│   ├── project-CLAUDE.md              ← template for new project state files
│   ├── project-LOG.md                 ← template for append-only session logs
│   └── project-CODEX.md              ← template for phased execution plans (optional)
├── resources/
│   └── example-agent.md              ← format reference for custom agents
└── projects/
    └── [project-slug]/
        ├── CLAUDE.md                  ← per-project STATE (what's true now; ~150-line budget)
        ├── LOG.md                     ← per-project HISTORY (append-only session journal)
        ├── AGENTS.md                  ← operational state (codebase projects only)
        └── .session-checkpoints.md   ← temporary mid-session saves (cleared at end-session)
```

---

## File roles

Mnemonic: **CLAUDE.md is the map, LOG.md is the travel journal, the handoff doc is the work order.**

**Root CLAUDE.md** — identity and preferences. Contains: My User, My User's Work, My User's Tools, References, Hard Rules, Custom Agents, Technical Experience, User Communication Preferences. Updated only by `/setup` or `/migrate`. Never touched by session commands.

**memory/glossary.md** — terms, shorthand, acronyms, people. Read at session start (once per conversation) so the assistant doesn't need shorthand explained mid-conversation. Updated only by the user or via `/setup`/`/migrate` — never by session commands.

**memory/projects.md** — the project index. One line per project, format: `- **CL-XX — Name** [emoji] Status — description`. Status key: 🔵 WIP · 🟢 Live · ✅ Complete · ⏸️ On Hold · 📦 Shelved · 💡 Idea. Updated by `/end-session` and `/new-project`.

**projects/[slug]/CLAUDE.md** — per-project STATE. Rewritten (compacted) by `/end-session`, never accumulated. Contains: State block (status/phase/blockers/next), overview, tech stack, conventions, decisions that still matter, known issues, open questions, next session. Size budget: ~150 lines — history overflow moves to LOG.md.

**projects/[slug]/LOG.md** — per-project HISTORY. Append-only session journal, newest entry first, written by `/end-session`. NOT read at session start — opened only for archaeology ("why did we do it that way?") or after long gaps. Never trimmed or rewritten.

**projects/[slug]/AGENTS.md** — operational state for codebase projects. Updated by `/end-session`. Contains: status (phase, in flight, next, blocker), architecture, last session, decisions.

**.session-checkpoints.md** — temporary. Accumulates during session via `/checkpoint`. Cleared by `/end-session` after contents are incorporated into LOG.md/CLAUDE.md.

**.start-session-version** — one line, the plugin version last synced. Written by `/setup`, updated by `/migrate` only after the user approves applying changes. Read (never written) by `/start-session` to detect a pending update. Missing file = untracked/pre-version-tracking workspace, treated the same as a stale version.

---

## Project index format

Machine-parseable. Do not change the format:
```
- **CL-XX — Project Name** [emoji] Status — one-line description.
```

Project ID conventions:
- CL-A## = App
- CL-W## = Website
- CL-B## = Bot / Telegram
- CL-F## = Finance
- CL-P## = Productivity / Tool
- CL-V## = Content / Video
- CL-D## = Design

---

## Custom agents pattern

Custom agents are stored in the user's root CLAUDE.md as a table. Format:

| Agent Name | Trigger | Behavior |
|------------|---------|----------|
| Name | When this arises | Do this |

When a trigger condition is met during a session, apply the agent's behavior automatically.

**Project Capture** is a built-in agent (not user-defined) documented in the root CLAUDE.md's Agents section, alongside Project Kickoff. It watches for untracked project-shaped work mid-conversation (files being produced over multiple turns, a recurring unnamed idea, a "save this" with no project home) and offers once — via native multi-choice — to run Project Kickoff. Rate-limited to one offer per conversation; never re-asks after a decline. A "not yet" answer still drops a one-line breadcrumb into `memory/projects.md` → Unassigned ideas, so the work isn't lost even if the user never revisits it.

---

## Hard rules (defaults — user may have customized)

- Never modify root CLAUDE.md during a session (only `/setup` and `/migrate` do this)
- Never delete any file without explicit user instruction
- `/migrate` is additive-only — it never removes or overwrites existing content
- Checkpoint files are temporary — always cleared at end-session
- The project index (`memory/projects.md`) is the single source of truth — there is only one file
- Project IDs and slugs are permanent once assigned
- Never update `.start-session-version` without the user approving the `/migrate` summary first — a stale marker is the signal that keeps the update visible

---

## Session flow summary

```
/setup        → one-time onboarding, generates all workspace files + .start-session-version
/start-session → check .start-session-version against the plugin version, mention drift once, then
                 read project context, orient, ask what to work on
[work happens]
/checkpoint   → mid-session save to .session-checkpoints.md
/end-session  → save all progress, clear checkpoints, update project index
/new-project  → create folder + context file, register in index
/migrate      → explain what changed in plain language, ask, then add missing sections/files
                and update .start-session-version
```
