# Projects

Single source of truth for all your projects. This file is read at session start (when no project is named) and is the canonical place to track project status.

Status key: 🔵 WIP · 🟢 Live · ✅ Complete · ⏸️ On Hold · 📦 Shelved · 💡 Idea

Format (one line per project — machine-parseable, do not change):
`- **[ID] — [Project Name]** [emoji] Status — one-line description`

Hard rule: **one sentence max per project.** Anything longer belongs in that project's
`CLAUDE.md` (state) or `LOG.md` (history). At session end the assistant enforces this —
if a line has grown into a paragraph, it trims the line and moves the detail into the project files.

---

## [Category 1]

<!-- Replace with your own category. Examples: Websites, Apps, Bots, Design, Content, Research. Add as many sections as you need. -->

- **[ID-01] — [Project Name]** 🔵 WIP — [one-line description]

---

## [Category 2]

- **[ID-02] — [Project Name]** ✅ Complete — [one-line description]

---

## 💡 Ideas / Queue

<!-- Reserved IDs and rough ideas you might pick up later. Promote into a category section above when work begins. -->

- **[ID-03] — [Idea Name]** 💡 Idea — [one-line description]

### Unassigned ideas (no ID yet)

- **[Idea title]** — [one-line description]

---

*Last updated [YYYY-MM-DD].*
