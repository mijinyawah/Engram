---
description: Save mid-session progress without ending the session
argument-hint: ""
---

# /checkpoint — Mid-Session Save

Save progress without ending the session. Use when switching context, taking a break, or before a risky change.

---

## Flow

### 1. Identify the active project

Determine from conversation context which project is being worked on.

### 2. Compile checkpoint

Gather from the conversation since the last checkpoint (or session start):
- What was accomplished since last save
- Decisions made
- Tests or checks run
- Open risks or blockers
- What's currently in progress

### 3. Append to checkpoint file

Append to `projects/[slug]/.session-checkpoints.md` — do not overwrite:

```
## Checkpoint — [YYYY-MM-DD HH:MM]

**Accomplished:**
- [bullet list]

**Decisions:**
- [bullet list, or "None"]

**Tests / checks run:**
- [bullet list, or "None"]

**Open risks:**
- [bullet list, or "None"]

**In progress:**
- [what's currently mid-flight]
```

Create the file if it doesn't exist.

### 4. Confirm

Tell the user the checkpoint was saved. One line.

---

## Notes

- Checkpoints are temporary — they are cleared by `/end-session` after their contents are incorporated
- Multiple checkpoints per session are fine — they accumulate in the file
- Do not update `memory/projects.md` during a checkpoint — that happens at end-session only
