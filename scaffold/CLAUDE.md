# Memory

## Setup

<!--
If this section exists, setup is not complete.
Run the flow below once, then delete this entire Setup section.
-->

### Setup flow

Note for the assistant: session commands (start session, end session, new project) are available to all users — no mode selection needed. Steps 5, 6, and 7 are conditional; ask before running each one. Agents can always be added later even if skipped here.

Branching directive: whenever a step branches based on the user's answer, present options using the platform's native multi-choice UI (e.g., AskUserQuestion in Claude CoWork, equivalent in Codex if available) with multi-select where appropriate and a free-text "other / type your own" option for nuance. Fall back to a numbered list with a final "other" option if the platform has no native multi-choice UI.

Step 0 - Orientation
- Introduce the Memory Engine: this file stores context about who the user is, how they work, where their work lives, and where their projects stand — so they don't have to re-explain it each time.
- Tell the user setup covers identity, references, projects, and a few behavior preferences. It takes about 10–15 minutes.

Step 1 - Confirm known context
- Summarize anything already known from this session.
- Ask the user to confirm or correct it.

Step 2 - Fill sections one at a time
Before asking for each section, briefly tell the user what the information enables — so questions feel purposeful, not interrogative.

- My User: name, role, location.
  Why: role calibrates explanation depth and vocabulary; location helps if the user references time zones or region-specific tools.

- My User's Work: employer or primary work context, one-line description.
  Why: helps orient to the nature and constraints of their projects (freelance vs. internal, audience, scope).

- My User's Tools: a two-step capture of tools they use regularly.
  Step 1: ask their primary work/occupation (e.g., "motion designer," "product manager," "systems engineer").
  Step 2: show a curated list of tools commonly used in that occupation, + a free-text option to add custom tools. User selects from list + adds custom entries.
  Why: this is the most directly useful field — knowing their stack shapes every troubleshooting response, suggestion, and shortcut. Pre-populated lists reduce friction while leaving room for specialization.

- Our Projects: tell the user there's a default project naming convention (e.g. initials + category letter + number + slug, like `HS-V01-reel`) that makes project kickoff and tracking cleaner. Use the platform's multi-choice UI to ask: keep the default, modify it, or skip having one entirely. If "modify," ask what format they want and document it. If "skip," remove the convention guidance from the Our Projects section below.

- My User's Workflow: primary machine, storage/backup setup, typical project types.
  Why: shapes context about how and where the user builds things.

- Environment: OS + version, default shell, path style, package manager(s) if technical, cloud-sync quirks.
  Why: every command, path, and compatibility claim depends on this — getting it wrong wastes the user's time re-explaining what didn't work.
  How to capture: **detect before asking.** If you have shell access, run a lightweight detection command (e.g. `uname -a` on macOS/Linux, `$PSVersionTable`/`echo %OS%` on Windows) and confirm what you found with the user rather than asking them to self-report — many non-developers don't reliably know their own shell or path style. If you have no shell access, ask directly, but phrase it in plain terms ("Are you on a Mac, Windows PC, or Linux machine?") rather than technical jargon they may not recognize.
  If the user mentions syncing this workspace across more than one machine (e.g. a Mac and a Windows PC), capture each machine's path root separately — see the References section below.

- User Communication Preferences: directness, formatting, tone.
  Why: shapes how every future response is structured and delivered.

Step 3 - Reference memory
- Tell the user: "Quick capture — where does most of your project work live? Knowing this lets me point you back to your own systems instead of guessing."
- Use the platform's multi-choice UI with multi-select. Options: Notion, Google Drive, Local folder(s), Slack, Specific dashboards/tools, Other (free text).
- For each selected option, ask a brief follow-up to capture specifics:
  - Notion → workspace URL or main page
  - Google Drive → folder name/link
  - Local folder(s) → root path(s)
  - Slack → channel names
  - Specific dashboards/tools → URLs and what each is for
  - Other → free text
- Output: populate the "References" section in this file with what was captured. Keep entries short — name + location + one-line purpose.

Step 4 - Hard Rules review
- Before reviewing, explain the purpose of this section: these rules exist to reduce hallucination, drift, and inconsistency across sessions and projects. They tell the assistant how to behave when uncertain, what to prioritize, and when to push back.
- Explain each rule briefly.
- Ask what to keep, change, remove, or add.

Step 5 - Technical calibration (conditional)
- Ask: "Do you ever work in a terminal, write code or scripts, or troubleshoot software at a technical level?"
- Use the platform's multi-choice UI. Options: Yes, Sometimes / it depends, No, Other (free text).
- If yes: ask what kind of technical work and how comfortable they are (OS and shell are already captured in Environment — don't re-ask). Write a calibrated summary in Technical Experience.
- If sometimes / it depends: ask what that looks like for them (expressions in creative tools, self-hosted setups, hardware troubleshooting, etc.). Write a light summary in Technical Experience noting the context.
- If no: note "No technical work planned" in Technical Experience and move on.

Step 6 - Agents (conditional)
- Ask: "Do you want to set up any custom behavior modes? For example — a research mode that cites every source, or a debug mode that goes step by step. You can always add these later just by asking."
- Use the platform's multi-choice UI. Options: Yes — let's set one up now, Not now / I'll add later, Tell me more first, Other (free text).
- If yes: ask for a name, trigger phrase(s), and what the agent should do. Create the instruction file at `resources/[agent-name].md` (follow the format in `resources/example-agent.md`). Add a row to the Custom Agents table in this file.
- If no: skip. No further explanation needed.
- The user never edits `resources/` files directly. The assistant maintains them — "add a research agent" or "tweak the debug mode" is enough.

Step 7 - Project intake (conditional)
- Ask: "Are there any projects you're already working on that you want to bring into the system? I'll set them up so you can pick up right where you left off."
- Use the platform's multi-choice UI. Options: Yes — one project, Yes — multiple, Not right now / I'll add later, Other (free text).
- If yes (one or multiple):
  - If multiple: cap at 3 projects in this first session. Tell the user they can add more anytime via `new project`.
  - For each project, ask via multi-choice UI: "Do you have a brief, doc, or notes for this project, or should we walk through it together?" Options: I have a doc or link to share, Walk me through it, Skip this one, Other.
  - Doc-import path: have the user paste or share the doc/link. Read it and extract these fields: project name, type (Tool/Web/Bot/Video/Design/etc.), current phase or stage, key decisions already made, what's next, and where the project lives. Confirm extraction with the user before writing files. Then run the Project Kickoff Agent flow (described below in the Agents section) with the prefilled fields.
  - Interview path: ask 6 short questions covering the same fields (name, type, phase, decisions, next, where it lives). Then run the Project Kickoff Agent flow (described below) with the answers.
- If no: skip. Mention `new project` is available anytime to bring one in later.

Step 8 - Finalize
- Summarize what was captured across all steps, including any projects brought in via Step 7.
- Confirm with the user.
- Populate all sections below with the information gathered.
- Delete this entire Setup section.
- Close with this message to the user (adapt the project line based on whether any projects were brought in):

  "Your context is saved in this folder and will be available every time we work together. You can ask me to update or change anything at any time.

  [If projects were brought in:] I've also set up [N] project[s] so you can pick up right where you left off — just say their name or `start session` and we'll resume.

  [If no projects yet:] When you're ready to bring a project into the system, just say new project and I'll set it up.

  A few commands worth knowing:
  - start session — I'll orient to your active projects and ask what you want to work on
  - end session — I'll save your progress and set up clear next steps for next time
  - new project — I'll create a tracked project folder with all the context I need"

Fallback command if this does not trigger automatically: `run onboarding now`

<!-- END SETUP -->

---

## My User

**[Your name]** - [Role / title]
Based in [City, State/Country]
Contact: [email or preferred handle]

---

## My User's Work

**[Employer or main context]** - [One-line description]

---

## My User's Tools

| Tool | What I use it for |
|------|-------------------|
| **[Tool name]** | [Use case] |

---

## Our Projects

**Project naming convention** — optional, but recommended if you're tracking multiple projects over time. A consistent naming scheme makes it easy to find projects, organize them visually, and scale as your project list grows.

Suggested format (adapt or remove if you don't want one):
- **[PREFIX]** = your initials or a team abbreviation
- **[category letter]** = A/App, W/Web, B/Bot, V/Video, D/Design, etc.
- **[number]** = two-digit sequential ID inside category
- **[slug]** = short kebab-case name

Example:
- `HS-V01-reel` (project HS, category Video, ID 01, name "reel")
- `BM-W02-site-redesign` (project BM, category Web, ID 02, name "site-redesign")

Project index file: `memory/projects.md` — single source of truth for all projects across all states (WIP, Live, Complete, On Hold, Shelved, Idea). Update the status emoji on a project's line when its state changes; don't move it between files. One sentence max per project line.

**State vs. history:** each project folder holds two files. `CLAUDE.md` is *state* — what's true right now, capped at ~150 lines, compacted at every session end. `LOG.md` is *history* — an append-only session journal, never read at session start, never trimmed. Status lives only in `memory/projects.md`; definitions live only in `memory/glossary.md`. Nothing is stored in two places.

If you don't want a naming convention, remove this entire section.

---

## References

Where the user's project work lives. Captured during Setup Step 3 and updated as systems change. Keep entries short — name + location + one-line purpose.

- **[System name]** — [URL or path] — [what it's used for]

If the user has no external systems to reference, remove this section.

**Multi-machine path map (optional):** if this workspace is synced across machines with different path roots (e.g. Mac + Windows PC), list each machine's root path here instead of hardcoding one path everywhere else in this file:

- **[Machine name]** — [root path on that machine]

---

## My User's Workflow

- **Primary machine:** [device]
- **Storage / backup:** [NAS / cloud / external]
- **Project types:** [what they usually build]

---

## Environment

Captured during setup, detected where possible rather than asked. Keeps commands, paths, and compatibility advice accurate to the user's actual machine.

- **OS + version:** [e.g. macOS 15 Sequoia / Windows 11 / Ubuntu 24.04]
- **Default shell:** [e.g. zsh / bash / PowerShell / cmd / fish]
- **Path style:** [e.g. `~/Users/name/...` / `C:\Users\name\...` or `%USERPROFILE%\...`]
- **Package manager(s):** [e.g. Homebrew, npm, pip — leave blank if not applicable]
- **Cloud-sync quirks:** [e.g. "Dropbox intercepts local paths and adds `(1)` on conflict" or "OneDrive renames folders on sign-out" — leave blank if none known]
- **Multiple machines?** [If this workspace syncs across machines with different path roots, list each: machine name → root path.]

**Directive:** always give commands in the user's actual shell — never hand bash syntax to a PowerShell user or vice versa. If unsure which shell is active, ask rather than guess.

---

## User Communication Preferences

- [Directness/detail preferences]
- [Formatting preferences]
- [Tone preferences]

---

## Technical Experience

[Fill during setup. If no technical work is planned, state that explicitly.]

---

## Hard Rules

These apply to every session:

- If source material is inaccessible, say so clearly.
- Distinguish recalled knowledge from verified knowledge.
- Do not promise compatibility without version/environment context.
- Name at least one failure mode when proposing a solution.
- Confidence must track evidence.
- Cite sources for claims and recommendations whenever possible.
- For technical/software guidance, prioritize current documentation.
- Always give commands and paths in the user's actual shell/OS (see Environment) — never emit bash syntax to a PowerShell user or vice versa.
- If a path from References or the Environment section stops resolving, ask whether the machine or OS changed rather than guessing where the file went or silently trying alternate paths.

---

## Memory Ownership

Where each kind of fact lives — so nothing gets written twice or drifts:

| Fact type | Owner | Notes |
|-----------|-------|-------|
| Project status | `memory/projects.md` | Only place status appears. Glossary and other files reference by ID, never restate status. |
| Project state (phase, blockers, next) | `projects/[slug]/CLAUDE.md` | Compacted every session end; ~150-line budget. |
| Session history | `projects/[slug]/LOG.md` | Append-only; not read at session start. |
| Terms, shorthand, people | `memory/glossary.md` | Definitions only — no status, no state. |
| Identity, preferences, rules | root `CLAUDE.md` | Updated only via setup/migrate or explicit user request. |
| Behavioral feedback for the assistant (platform-side memory, e.g. CoWork auto-memory) | The platform's own memory | Do not duplicate workspace facts there; do not duplicate behavioral feedback here. |

When about to save a fact, check this table first. If it already has an owner, update the owner file — don't create a copy.

---

## Session Behavior

These are protocol instructions for session handling.

Mode note: These protocols are always available. The assistant runs them when invoked — it does not proactively prompt for session start/end unless the user has established that habit. Run them when asked; otherwise stay passive.

Reliability note:
- Trigger phrases are protocol instructions, not hard automations. If a flow doesn't trigger, type the command directly in chat: `run onboarding now` / `start session` / `end session` / `new project` / `checkpoint`.

### Session Start

Triggers:
- `start session`
- any equivalent phrase requesting session startup

Flow:
0. Read `memory/glossary.md` if it hasn't already loaded this conversation — decode any shorthand, acronyms, or names before they come up rather than asking the user to explain them mid-conversation.
1. If user names a project: read `projects/[project-name]/CLAUDE.md` — the `## State` block at the top is the orientation summary (status, phase, blockers, next actions). Read the rest of the file as needed, but do NOT read `LOG.md` — it's history, only opened when the user asks "why did we…" or after a long gap.
   - **Long-gap check:** if the header's `Last updated` date is more than 30 days old, mention it and offer to skim `LOG.md` before diving in — a returning user after a long gap may not think to ask for history themselves.
2. If user wants something new: run Project Kickoff flow.
3. If user does not specify: read `memory/projects.md`, list projects whose status is 🔵 WIP or 🟢 Live, and ask which one to resume.

Always confirm orientation before execution.

### Checkpoint

Triggers:
- `checkpoint`
- `save checkpoint`
- any equivalent phrase requesting a mid-session save

Flow (mid-session save, does not end the session):
1. Identify the active project from conversation context.
2. Compile what's happened since the last checkpoint (or session start): accomplished, decisions, tests/checks run, open risks, what's currently in progress.
3. Append (never overwrite) to `projects/[slug]/.session-checkpoints.md`, creating it if missing.
4. Confirm in one line that the checkpoint was saved.

Checkpoints are temporary — `Session End` clears this file once its contents are folded into `LOG.md`/`CLAUDE.md`. Multiple checkpoints per session are fine; they accumulate.

### Session End

Triggers:
- `end session`
- `session end`
- `save session`
- `save progress`
- `wrap up`

Flow (compact, don't accumulate):
1. **Append the session narrative to `projects/[slug]/LOG.md`** (newest at top): what was done, decided, learned, left open. This is the only place session history lives. Create LOG.md from `templates/project-LOG.md` if missing.
2. **Rewrite — don't append to — the project `CLAUDE.md`:**
   - `## State` block: current status, phase, blockers, next 3 actions.
   - `## Decisions That Still Matter`: add only decisions that constrain future work; prune entries that no longer do.
   - `## Known Issues & Gotchas` / `## Open Questions`: add new, remove resolved.
   - `## Next Session`: rewrite completely.
   - `Last updated` date in the header.
3. **Size check:** if the project `CLAUDE.md` exceeds ~150 lines, move narrative/history content into `LOG.md` until it fits. The file describes what's true *now*, not how it got there.
4. Update the project's line in `memory/projects.md` if status changed (emoji only — there's only one file). **Enforce the one-sentence rule:** if the line has grown past one sentence, trim it and move the detail into the project files.
5. Update the `Last updated` line at the bottom of `memory/projects.md`.
6. Confirm what was saved — brief, 3–5 lines.

---

## Agents

Agents are optional behavior modes activated by trigger phrases.

Mode note: Agents are always available on demand. If none were set up during setup, the user can request one at any time — "add a research agent" or "add a debug mode" is enough. The assistant creates the instruction file and adds a row to the Custom Agents table.

### Project Kickoff Agent

Triggers:
- `new project`
- `start a new project`
- `kick off [X]`
- `I want to build [X]`

Flow:
1. Ask for project name, type, and one-line description.
   - **Handoff doc (conditional):** only ask whether this project needs a phased execution plan / handoff doc for a coding agent (Codex, Claude Code, or similar) if Technical Experience indicates the user works with coding agents or does technical/dev work. If Technical Experience says no technical work, or wasn't captured, skip this question entirely — don't offer a concept ("Codex file," "handoff doc") that won't mean anything to them.
   - **Tech Stack / Key Files / How to Run:** if the project type is non-technical (e.g. Video, Design, Content) or Technical Experience indicates no dev work, remove these sections from the generated `CLAUDE.md` automatically — don't leave a "delete if not applicable" comment for the user to act on themselves.
2. Read `memory/projects.md` to determine the next project ID. Find the highest valid ID number (ignoring template placeholders like `[ID-01]`) across all category sections including Ideas / Queue, and increment by 1. If no valid IDs exist yet, start at `01`.
3. Create new folder under `projects/` using the naming convention.
4. Create `CLAUDE.md` from `templates/project-CLAUDE.md` and prefill known fields.
5. Create `LOG.md` from `templates/project-LOG.md` (empty header, no entries yet). If requested, also create `CODEX.md` from `templates/project-CODEX.md`.
6. Add a line to `memory/projects.md` under the appropriate category section: `- **[ID] — [Name]** 🔵 WIP — [one-line description].`
7. Confirm setup and ask for first task.

### Project Capture (Kickoff, self-triggered)

Notices untracked project-shaped work happening mid-conversation and offers to spin it up — for the sessions where the user never said "new project" but is clearly building one.

Trigger conditions (any of these, only outside an active project session):
- The chat has produced or modified files over multiple turns
- The same unnamed effort keeps recurring in conversation (e.g. "the reel," "that bot idea")
- The user asks to save or continue something that has no home in `projects/`

Behavior:
1. Ask once, lightweight, via the platform's native multi-choice UI: "This looks like it might be its own project — want me to set it up? [Yes / Not yet / Never for this one]"
2. **Yes** → run the Project Kickoff Agent flow above, prefilled from the conversation (same extraction fields as doc-import: name, type, phase, decisions, next action).
3. **Not yet** → don't create anything now, but drop a one-line breadcrumb into `memory/projects.md` under an "Unassigned ideas" note so it isn't lost. If the user runs `end session` later in this same conversation without ever assigning it to a project, leave that breadcrumb in place rather than letting the work evaporate untracked.
4. **Never for this one** → drop it, don't ask again about this specific effort.

Rate limit: **one offer per conversation.** Never re-ask after a no, even if the trigger conditions keep firing.

### Custom Agents

| Agent | Triggers | What the assistant should do |
|-------|----------|-----------------------|
| | | |
