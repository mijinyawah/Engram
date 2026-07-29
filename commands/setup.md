---
description: Run first-time onboarding and generate your workspace files
argument-hint: ""
---

# /setup — First-Time Onboarding

Run the full onboarding flow and generate the user's workspace files. This is a one-time setup — do not run again after the workspace exists unless the user explicitly asks.

---

## Before you begin

Check whether workspace files already exist in the user's working directory:
- If `CLAUDE.md` exists: ask the user if they want to re-run setup or just run `/migrate` to add missing sections. Default to `/migrate`.
- If no `CLAUDE.md`: proceed with the full flow below.

Scaffold templates live at `${CLAUDE_PLUGIN_ROOT}/scaffold/`. You will read them and write populated versions to the user's working directory.

---

## Step 0 — Orientation

Introduce yourself and the system briefly. Something like:

> "I'm going to set up your memory workspace — a set of files I'll read at the start of every session to stay oriented about who you are, what you're working on, and how you like to work. This takes about 5–10 minutes. Ready?"

Wait for confirmation before proceeding.

---

## Step 1 — Confirm known context

If you already have context about the user (from prior conversation or loaded files), surface it:

> "Based on what I know so far, here's what I'll pre-fill: [list what you know]. Does this look right, or should I start fresh?"

If no context exists, skip to Step 2.

---

## Step 2 — Capture identity

Ask for the following fields. Use the platform's native multi-choice UI where offered; always include a free-text option.

Fields to capture:
- **Name** — what to call them
- **Occupation / role** — one or two words (e.g. motion designer, researcher, solo founder)
- **Tools** — based on their occupation, offer a curated shortlist of likely tools + "Other / add your own". Let them select multiple.
- **Project naming convention** — how they want projects labeled. Default: `CL-[type][number] — Name` (e.g. CL-A01 — Sonogram). Show examples and offer: keep as-is / modify / skip.
- **Timezone** — for session timestamps
- **Communication style** — brief, detailed, technical, casual (multi-select)
- **Environment** — OS + version, default shell, path style, package manager(s), cloud-sync quirks. **Detect before asking:** if you have shell access, run a lightweight detection command (`uname -a` on macOS/Linux, `$PSVersionTable`/`echo %OS%` on Windows) and confirm the result with the user instead of interviewing them for it — many users don't reliably know their own shell or path style. Fall back to plain-language questions ("Mac, Windows, or Linux?") only if you have no shell access. If they mention syncing this workspace across multiple machines, capture each machine's path root separately for the References section.

---

## Step 3 — Reference memory

Ask where their work lives so you can surface the right context during sessions.

> "Where do you keep your work? Select all that apply."

Options (multi-select + free text):
- Notion
- Google Drive / Docs
- GitHub / GitLab
- Figma
- Airtable / spreadsheets
- Local files / desktop
- Other (free text)

Capture as a "References" section in CLAUDE.md — plain list of locations with optional notes.

---

## Step 4 — Hard Rules review

Read `${CLAUDE_PLUGIN_ROOT}/scaffold/CLAUDE.md` and surface the default Hard Rules section to the user.

> "Here are some default hard rules I'll follow. Review them and tell me what to keep, remove, or add."

Wait for their edits. Write their confirmed rules into the final CLAUDE.md.

---

## Step 5 — Technical calibration (conditional)

**Skip this step if:** the user's occupation and tool list suggest a non-technical background (e.g. writer, designer with no dev tools listed).

**Run this step if:** any coding/dev tools were listed, or if occupation suggests technical work.

Ask:
> "Do you work with code, scripts, or developer tools in your projects?"

If yes: ask which languages/tools, comfort level, and whether they want technical explanations or plain-language summaries. OS and shell are already captured in Environment (Step 2) — don't re-ask. Capture in a "Technical Experience" section.

If no: skip.

---

## Step 6 — Custom agents (conditional)

**Skip this step if:** the user seems unfamiliar with AI agents or did not reference custom workflows.

**Run this step if:** they've mentioned specific recurring tasks, workflows, or agent-like patterns.

Explain briefly:
> "You can give me standing instructions for specific recurring tasks — like always following a certain format for a script review, or acting as a particular kind of collaborator. Want to set any up now?"

If yes: capture up to 3 agents with: name, trigger, behavior. Write to CLAUDE.md Custom Agents table.
If no: skip, leave the table empty for later.

---

## Step 7 — Project intake (conditional)

**Skip this step if:** the user is brand new to projects and has nothing in flight.

**Run this step if:** they have existing projects they'd like to track.

> "Do you have any active projects you'd like me to know about? I can pull context from a document you paste, or I can ask you a few quick questions. Up to 3 projects for now."

**Path A — Doc import:** User pastes a brief, notes, or any document. Extract: project name, type, current phase, key decisions already made, next steps, where project files live. Confirm with user.

**Path B — Interview:** Ask 6 questions per project: name, what it is, current phase, biggest decision made so far, what's next, where files/notes live.

Cap at 3 projects. For each project confirmed:
1. Add an entry to `memory/projects.md`
2. Create a folder at `projects/[slug]/`
3. Generate a context file at `projects/[slug]/CLAUDE.md` from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-CLAUDE.md`

---

## Step 8 — Finalize

Write all files to the user's working directory:

1. `CLAUDE.md` — populated from `${CLAUDE_PLUGIN_ROOT}/scaffold/CLAUDE.md` with all collected data
2. `AGENTS.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/AGENTS.md`
2b. `GEMINI.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/GEMINI.md`. Both this and `AGENTS.md` are thin pointers to the same `CLAUDE.md` — AGENTS.md is read by Codex CLI, Cursor, Windsurf, Copilot, and most other coding agents (notably not Cline, which needs its own `.clinerules` file); GEMINI.md is what Gemini CLI looks for instead.
3. `memory/projects.md` — with any projects from Step 7, or empty template
4. `memory/glossary.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/memory/glossary.md`
5. `templates/project-CLAUDE.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-CLAUDE.md`
5b. `templates/project-LOG.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-LOG.md`
6. `templates/project-CODEX.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-CODEX.md`
7. `resources/example-agent.md` — copied from `${CLAUDE_PLUGIN_ROOT}/scaffold/resources/example-agent.md`
8. `projects/` directory — empty, ready for `/new-project`
9. `.start-session-version` — write a single line containing the `version` field from `${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json`. This is how `/start-session` and `/migrate` detect plugin updates later — see "Version tracking" below. Do not explain this file to the user; it's bookkeeping, not content they need to manage.

Close with a confirmation message. If projects were added: summarize them. If none: let the user know they can run `/new-project` anytime to kick one off.

> "You're set up. I'll read these files at the start of every session. Run `/start-session` when you're ready to begin work."

---

## Version tracking

`.start-session-version` records which plugin version generated this workspace. It lets `/start-session` notice when the plugin has updated since setup and offer `/migrate` — the user never has to track version numbers themselves or ask what changed. See `/start-session` and `/migrate` for how it's read.
