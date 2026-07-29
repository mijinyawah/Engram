---
description: Orient yourself at the top of every session — load project context and pick up from where you left off
argument-hint: "[project-name]"
---

# /start-session — Session Start

Orient yourself before beginning work. Run at the top of every session.

---

## Flow

### 0. Check for a plugin update

Read the `version` field from `${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json` — this is the plugin's current version.

Read `.start-session-version` from the working directory (this file is written by `/setup` and updated by `/migrate`).

- **File missing:** this workspace predates version tracking, or was built from the file template rather than the plugin. Treat as "unknown version."
- **File present and matches the plugin version:** no update pending — skip straight to Step 1.
- **File present and differs, or unknown:** an update is pending. Surface it as a single plain-language line, not a blocking question:

  > "Heads up — the start-session plugin has updated since your workspace was last synced. Run `/migrate` any time to pick up what's new, or say the word and I'll walk you through it now. Otherwise, let's get started."

  Then continue straight into Step 1 regardless of their answer — never block session start on this. If they ask to migrate now, run `/migrate` first, then continue.

Only surface this once per session (at the top). Don't re-mention it again later in the same conversation.

### 1. Identify the project

Before identifying the project, read `memory/glossary.md` if it hasn't already loaded this conversation — decode shorthand and acronyms up front rather than asking the user to explain them later.

**If the user named a project** (e.g. `/start-session CL-A03` or "start session on Slate"):
- Locate the project folder at `projects/[slug]/`
- Read `projects/[slug]/CLAUDE.md` — the `## State` block at the top is the orientation summary (status, phase, blockers, next actions)
- If the header's `Last updated` date is more than 30 days old, mention it and offer to skim `LOG.md` before diving in — don't wait for the user to think to ask
- If it has an `AGENTS.md` (codebase project), note the current status and next steps from there
- Do NOT read `LOG.md` yourself by default — it's append-only history, opened only if the user asks "why did we…" or accepts the long-gap offer above
- Skip to step 3

**If no project was named:**
- Read `memory/projects.md`
- Surface all 🔵 WIP and 🟢 Live projects
- Ask: "Which project are we working on today?" — use multi-choice if the platform supports it
- Wait for selection, then read that project's context file

### 2. Check for checkpoints

Look for `projects/[slug]/.session-checkpoints.md`. If it exists:
- Surface the most recent checkpoint
- Ask if the user wants to pick up from there or start fresh

### 3. Orient and confirm

Briefly summarize what you know:
- Project name and current phase
- What was in progress or next (from CLAUDE.md or AGENTS.md)
- Any open blockers

Then ask: "What would you like to work on today?"

### 4. Begin

Start the session. You're oriented.

---

## Notes

- Do not modify any files during `/start-session` — this is read-only orientation
- If `CLAUDE.md` doesn't exist in the working directory, suggest running `/setup` first
- If the project folder doesn't exist, suggest `/new-project`
