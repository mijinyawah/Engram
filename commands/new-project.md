---
description: Kick off a new tracked project with a context file and project index entry
argument-hint: "<project-name>"
---

# /new-project — New Project Kickoff

Kick off a new tracked project. Creates the folder, generates a context file, and registers it in the project index.

---

## Flow

### 1. Gather project basics

Ask (or confirm if already stated):
- **Project name** — what to call it
- **Project type** — App, Website, Bot, Tool, Content, Design, Finance, Other
- **One-line description** — what it is
- **Current phase** — Idea, Planning, In Progress, etc.

**Handoff doc (conditional):** check the user's root `CLAUDE.md` for a Technical Experience section. Only ask whether this project needs a phased execution plan / handoff doc for a coding agent (Codex, Claude Code, or similar) if that section indicates the user works with coding agents or does technical/dev work. If it says no technical work, or doesn't exist, skip this question — don't introduce a concept that won't mean anything to a non-technical user. If yes, create `CODEX.md` per Step 3 below.

Suggest a project ID based on type and the next available number in `memory/projects.md`:
- App → CL-A##
- Website → CL-W##
- Bot → CL-B##
- Finance → CL-F##
- Productivity/Tool → CL-P##
- Content/Video → CL-V##
- Design → CL-D##

Show the suggested ID and ask: keep / modify.

Generate a slug from the name (lowercase, hyphens, no special chars). Example: "My Week Clawde" → `my-week-clawde`.

### 2. Create project folder

Create `projects/[slug]/` directory.

### 3. Generate project context file

Read `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-CLAUDE.md`. Also create `projects/[slug]/LOG.md` from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-LOG.md` (header only, no entries yet). If the handoff doc was requested above, also create `projects/[slug]/CODEX.md` from `${CLAUDE_PLUGIN_ROOT}/scaffold/templates/project-CODEX.md`.

**Prune non-technical sections automatically:** if the project type is non-technical (Content, Design, Video, etc.) or the user's Technical Experience indicates no dev work, remove the Tech Stack, Key Files, and How to Run sections from the generated file entirely — don't leave a "delete if not applicable" comment for the user to act on. If the project is technical, keep them and prefill what you can.

Populate with the collected info and write to `projects/[slug]/CLAUDE.md`.

### 4. Register in project index

Add a line to `memory/projects.md` in the appropriate section:

```
- **CL-XX — Project Name** 🔵 WIP — one-line description.
```

Update the `Last updated` line at the bottom.

### 5. Confirm

Tell the user what was created. Offer to run `/start-session` on the new project.

---

## Notes

- Do not create a `.session-checkpoints.md` file — that's created by `/checkpoint` only when needed
- If the user wants to immediately add context (a brief, doc, notes), proceed into a brief intake conversation and update `projects/[slug]/CLAUDE.md` with what's captured
- The project ID and slug are permanent — do not rename without user's explicit instruction
