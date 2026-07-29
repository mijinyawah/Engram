# Changelog

## v2.2.0 — 2026-07-29

Theme: environment-aware and self-updating. Closes the biggest gaps from the v1.6.0 audit — the plugin now adapts to the user's actual machine, gates developer-only concepts behind demonstrated technical use, catches untracked project work before it's lost, and tells the user when it's updated instead of leaving them to ask.

### Added
- `.start-session-version` — workspace file recording which plugin version last synced it. Written by `/setup`, updated by `/migrate` only after the user approves.
- `/start-session` — new Step 0: compares the workspace's recorded version against the plugin's current version and surfaces a one-line, non-blocking mention if they've drifted, offering `/migrate`.
- `/migrate` — new Step 0: reads `CHANGELOG.md`, translates what changed into plain language, and asks before touching anything (previously only asked before the state/log restructure specifically — the whole migration now explains itself upfront).
- `CLAUDE.md` — new "Environment" section (OS + version, default shell, path style, package manager(s), cloud-sync quirks, optional multi-machine path map). Captured by detection first; interview only as fallback.
- `CLAUDE.md` / `SKILL.md` — new Hard Rules: always match the user's actual shell/OS syntax; if a referenced path stops resolving, ask whether the machine changed rather than guessing.
- `CLAUDE.md` — new "Project Capture" agent: notices untracked project-shaped work mid-conversation and offers once (native multi-choice) to run Project Kickoff. Rate-limited to one offer per conversation; a "not yet" leaves a breadcrumb in `memory/projects.md` → Unassigned ideas so the work isn't lost.
- `/end-session` — if no project can be identified but the session produced real work, drops the same Unassigned-ideas breadcrumb rather than letting it evaporate.
- `CLAUDE.md` / `SKILL.md` — Checkpoint protocol documented for file-template-only users (previously plugin-only, undocumented in the template).
- `scaffold/GEMINI.md` — pointer file for Gemini CLI, which auto-discovers `GEMINI.md` rather than `AGENTS.md`.
- `README.md` — platform table expanded: `AGENTS.md` documented as a genuine cross-tool convention (Codex CLI, Cursor, Windsurf, Copilot, Amp, Devin, Aider, Zed, Jules, VS Code, JetBrains Junie), with Cline called out as a verified exception (needs its own `.clinerules` — does not read `AGENTS.md` as of this writing), and Gemini CLI documented separately.

### Changed
- Project Kickoff flow (`CLAUDE.md`, `/new-project`) — the "handoff doc / CODEX.md needed?" question is now gated on the user's Technical Experience; skipped entirely for non-technical users instead of asked by default.
- `templates/project-CLAUDE.md` — Tech Stack / Key Files / How to Run sections are now pruned automatically by the assistant at kickoff for non-technical projects, instead of left with a "delete if not applicable" comment for the user to act on.
- `templates/project-CLAUDE.md` — How to Run fence de-bashed; commands are given in the user's actual shell per the new Environment section.
- `memory/glossary.md` — now actually read at session start (fixes a claim/behavior mismatch — the file said it was read at session start, but no flow instructed it).

### Fixed
- `memory/glossary.md` — leading-lowercase sentence corrected.

---

## v2.1.0 — 2026-07-28

Theme: decay management. v2.0.0 (plugin conversion) solved distribution; this release makes the system forget well — project files stop growing without bound in long-running projects, and duplicated facts (status in both glossary and index) stop drifting out of sync.

### Added
- `templates/project-LOG.md` — new append-only per-project session journal. One entry per session (Did / Decided / Learned / Left open), newest first, never trimmed, never read at session start.
- `templates/project-CLAUDE.md` — new `## State` block at the top (status, phase, blockers, next 3 actions) so session start can orient from the first ~15 lines.
- `CLAUDE.md` — new "Memory Ownership" table: one owner file per fact type (status → projects.md, state → project CLAUDE.md, history → LOG.md, definitions → glossary, identity → root CLAUDE.md, behavioral feedback → platform memory). Assistants check the table before saving a fact.
- `scaffold/` now ships inside the plugin zip. **Fixes a v2.0.0 bug:** `setup`, `migrate`, and `new-project` all referenced `${CLAUDE_PLUGIN_ROOT}/scaffold/…`, but the shipped zip contained no scaffold — fresh plugin-only installs would fail at setup.

### Changed
- `templates/project-CLAUDE.md` — reframed as a *state* file with an explicit ~150-line size budget. "Decisions Log" replaced by "Decisions That Still Matter" (only decisions that actively constrain current work; prunable). Session narratives no longer live here.
- Session End flow (`CLAUDE.md` + plugin `end-session.md`) — rewritten around "compact, don't accumulate": append session narrative to LOG.md, rewrite (not append) the project CLAUDE.md, enforce the size budget, enforce the one-sentence rule in `memory/projects.md`.
- Session Start flow (`CLAUDE.md` + plugin `start-session.md`) — orients from the `## State` block; explicitly does NOT read LOG.md.
- Project Kickoff flow (`CLAUDE.md` + plugin `new-project.md`/`setup.md`) — creates LOG.md alongside CLAUDE.md.
- `memory/glossary.md` — Status column removed from Project Shorthand. Status lives only in `memory/projects.md`; anything stored twice eventually disagrees.
- `memory/projects.md` — one-sentence-per-project rule made explicit and enforced at session end.
- Plugin `migrate.md` — new step offering (asks first, never silent) a per-project state/log restructure for workspaces upgrading from earlier versions.

---

## v2.0.0 — 2026-05-11

### Added
- Plugin format: `start-session` is now distributable as a Claude CoWork / Claude Code plugin.
- `.claude-plugin/plugin.json` — plugin manifest.
- `.claude-plugin/marketplace.json` — marketplace catalog for one-command install (`/plugin marketplace add mijinyawah/start-session`).
- `commands/setup.md` — `/setup` slash command. Replaces the self-removing Setup block in CLAUDE.md. Runs the full 9-step onboarding flow and writes the workspace scaffold to the user's directory.
- `commands/start-session.md` — `/start-session` slash command. Session start protocol.
- `commands/end-session.md` — `/end-session` slash command. Session end protocol.
- `commands/checkpoint.md` — `/checkpoint` slash command. Mid-session save.
- `commands/new-project.md` — `/new-project` slash command. Project kickoff agent.
- `commands/migrate.md` — `/migrate` slash command. Additive migration for users updating from older versions. Never destructive.
- `skills/session-management/SKILL.md` — session management skill. Gives the assistant full context on workspace structure, file conventions, project index format, and hard rules. Loaded automatically when session-related topics arise.
- `scaffold/` directory — workspace template files generated by `/setup`. Previously distributed at repo root.

### Changed
- Repo restructured: template files moved from root into `scaffold/`. Root is now the plugin.
- `scaffold/CLAUDE.md` — simplified to data-only. Session protocols and agent flow instructions removed (now live in plugin commands). Custom Agents section retained as a data table.
- `scaffold/AGENTS.md` — updated to reference plugin commands.
- `README.md` — rewritten for v2.0. Documents plugin install path and file template fallback.

### Architecture
- **Logic / data split:** session protocols, setup flow, and command behavior live in the plugin and update automatically with each push. User identity, tools, preferences, and project state live in the user's workspace files and are never touched by plugin updates.
- **Update model:** commit-SHA versioning. Every push to `main` is treated as a new version. Users receive logic updates automatically at next Claude startup.

---

## v1.4.0 — 2026-05-11

### Added
- `CLAUDE.md` — branching directive: branching questions use the platform's native multi-choice UI with a free-text fallback.
- `CLAUDE.md` — Step 3 (Reference memory): captures where the user's project work lives using multi-select. Populates a new "References" section.
- `CLAUDE.md` — Step 7 (Project intake, conditional): onboards in-flight projects via doc-import or 6-question interview. Caps at 3 projects per first session. Triggers Project Kickoff per project.
- `CLAUDE.md` — "References" section in the body alongside My User, Tools, etc.

### Changed
- `CLAUDE.md` — My User's Tools: two-step capture (occupation → curated list + free text).
- `CLAUDE.md` — Our Projects naming convention: reframed with examples and multi-choice keep/modify/skip.
- `CLAUDE.md` — Step renumbering: Hard Rules (3→4), Technical calibration (4→5), Agents (5→6), Finalize (6→8).
- `CLAUDE.md` — Step 8 closing message adapts to whether projects were brought in.
- `README.md` — "How setup adapts to you" updated for three conditional steps.

---

## v1.3.0 — 2026-05-08

### Changed
- `CLAUDE.md` — setup flow renamed from Onboarding to Setup.
- Lite/Pro mode selection removed. Session commands available to all users; Steps 4 and 5 conditional on answers.
- Step 2 expanded with "Why:" notes for each field.
- Step 4 technical question reframed to catch creative-technical users.
- Step 5 (Agents) now conditional.

---

## v1.2.2 — 2026-05-08

### Added
- `CLAUDE.md` — Step 0.5 (Lite/Pro mode selection).

---

## v1.2.1 — 2026-05-06

### Added
- `LICENSE` — MIT.
- `AGENTS.md` — Codex pointer.
- `projects/.gitkeep`.

### Changed
- Voice pass: "the assistant" replaces "Claude" throughout.
- `memory/projects.md` — single canonical file with status-emoji format.

---

## v1.2 — 2026-04-30

### Improved
- Added `START_HERE.md` with explicit fallback commands.
- Reworked README for clearer platform setup.
- Cleaned CLAUDE.md of personal/org-specific assumptions.
