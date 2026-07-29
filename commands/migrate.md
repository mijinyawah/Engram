---
description: Add missing sections from newer versions without overwriting existing content
argument-hint: ""
---

# /migrate — Add Missing Sections

Additive migration for users updating from an older version of start-session. Adds missing sections and files — never overwrites or removes existing content.

**What this is not:** `/migrate` does not create a new workspace folder, and it does not touch your `projects/` subfolders except for the one optional, ask-first, per-project history restructure in Step 5 below. It updates the small set of top-level workspace files (`CLAUDE.md`, `memory/`, `templates/`, `resources/`, `AGENTS.md`, `GEMINI.md`, `.start-session-version`) in place, in the same folder you already work in. If you want to bring existing projects into the system for the first time, that's `/setup`'s Step 7 (project intake) or `/new-project` — not this.

---

## When to run

Run `/migrate` when:
- The user updated the plugin and wants to pick up any new sections or files
- The user's workspace is missing sections that exist in the current scaffold templates
- The user installed the plugin but already has a CLAUDE.md from a previous version

Do NOT run after `/setup` — setup already creates the full current structure.

---

## Flow

### 0. Explain what's changed before touching anything

Read the `version` field from `${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json` (current plugin version) and `.start-session-version` from the working directory (the version this workspace was last synced to — if the file doesn't exist, treat the workspace as untracked/older).

Read `${CLAUDE_PLUGIN_ROOT}/CHANGELOG.md`. Collect every entry newer than the workspace's recorded version (or, if untracked, summarize the last 2-3 entries — going further back rarely matters to the user).

Translate the collected "Added"/"Changed" bullets into a short, plain-language summary — no dev shorthand, no file-path-as-explanation. Say what changed for the user, not what changed in the repo. For example, translate "new `## State` block in project-CLAUDE.md" into "project files now lead with a short status summary so I can get oriented faster."

Present the summary and ask before proceeding:

> "Since your workspace was last updated, here's what's new: [plain-language bullets]. I'll add the missing pieces below — nothing existing gets removed or overwritten. Want me to go ahead?"

If the user says no or not now: stop here. Don't touch any files, and don't update `.start-session-version` (so this same summary surfaces again next time `/migrate` or `/start-session` runs).

If yes: continue to Step 1.

### 1. Read current scaffold

Read `${CLAUDE_PLUGIN_ROOT}/scaffold/CLAUDE.md` — this is the canonical current template.

### 2. Compare against user's CLAUDE.md

Read the user's `CLAUDE.md`.

Identify any top-level sections (marked by `## Section Name`) that exist in the scaffold but are missing from the user's file.

### 3. Append missing sections

For each missing section:
- Append it to the end of the user's `CLAUDE.md`
- Pre-fill with placeholder values from the template
- Add a comment above the section: `<!-- Added by /migrate [YYYY-MM-DD] — fill in as needed -->`

Do NOT:
- Remove any existing sections
- Overwrite any existing content
- Reorder sections

### 4. Check for missing files

Verify these files exist in the user's working directory. If missing, create from scaffold:
- `memory/glossary.md`
- `templates/project-CLAUDE.md`
- `templates/project-CODEX.md`
- `resources/example-agent.md`
- `AGENTS.md`

### 5. Offer the v2.1 state/log restructure (per project)

New in v2.1: project files split into `CLAUDE.md` (state, ~150-line budget) + `LOG.md` (append-only history).

For each folder in `projects/`:
- If `LOG.md` is missing, or `CLAUDE.md` exceeds ~150 lines, flag it.
- ASK the user before restructuring (this moves content, unlike the additive steps above). If approved:
  - Create `LOG.md` from scaffold.
  - Move session narratives / historical prose from `CLAUDE.md` into `LOG.md` as dated entries (preserve all content — relocate, never delete).
  - Rebuild `CLAUDE.md` around the current template: State block, Overview, Tech Stack, Conventions, Decisions That Still Matter, Known Issues, Open Questions, Next Session.

### 6. Update the version record

Write the plugin's current version (from `${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json`) to `.start-session-version` in the working directory, creating the file if it didn't already exist. This is what lets `/start-session` stop mentioning the pending update.

### 7. Confirm

Tell the user what was added. List the sections and files created. If nothing was missing, say so.

---

## Notes

- `/migrate` is always safe to run — it only adds, never removes or changes existing content
- If the user has heavily customized their CLAUDE.md, their customizations are preserved
- After migrating, the user should review the newly added sections and fill in any placeholders
- If the user declines at Step 0, `.start-session-version` is left untouched on purpose — the update stays visible until they're ready
