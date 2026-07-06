# Product Priority Planning Board

A single-file, browser-based planning board for tracking themes, tickets, and sprint work across a team. Everything lives in one `index.html` — no build step, no server required.

## Views / Tabs

| Tab | Purpose |
|---|---|
| **CY 2026 / CY 2027** | Timeline grid — one row per owner, one column per sprint. Drag themes onto sprints to plan work. |
| **Progress** | Kanban-style board (To do / Work in progress / Done) showing themes as cards. |
| **Planner** | Filterable list of all tickets, with search by name or Jira key, and filters for theme, person, status label, refinement session, category, and ticket type. |
| **Ad-Hoc Work** | A lightweight log for work items that fall outside normal sprint planning. |
| **Released** | Archive view of everything marked as released. |

## Core Concepts

- **Themes** — a goal or initiative (e.g. "Migrate reporting pipeline"), owned by a person, spanning one or more sprints, with an optional description, priority, and status (To do / WIP / Done).
- **Tickets** — individual pieces of work under a theme, with a title, Jira key, story points, type (Defect/Enhancement/Spike), status labels, comments, and owner.
- **Sprints** — fixed time blocks that themes and tickets are planned against; sprint dates are configurable via **Sprint dates** in the header.

## Key Features

- **Drag and drop** planning — drag tickets between sprints/owners on the timeline.
- **Jira integration** — a draggable bookmarklet ("Send to Board") scrapes an open Jira ticket and pushes it straight into the board. A debug bookmarklet is also available for troubleshooting field scraping.
- **Undo** — `Ctrl+Z` or the Undo button reverts the last change.
- **Live sync & presence** — changes are shared with the team in real time; the header shows who else is currently online and when the board was last saved.
- **Dark mode** toggle, following or overriding system preference.
- **Hover tooltips** — hovering reveals full detail that's otherwise truncated on cards:
  - Hovering a **ticket card** shows its full title, status, priority, and points (useful when the title is clipped to 3 lines).
  - Hovering the **speech-bubble icon** next to a ticket's points shows its full comment text (only appears on tickets that have a comment).
  - Hovering a **theme card** (Progress tab) or a **theme bar** (timeline grid) shows the full theme title and description.
- **Archive** — move past sprints out of the active view without deleting their data.

## Data Model (high level)

```
theme:  { id, title, desc, owner, priority, color (todo/wip/done), start/end sprint, groupId }
ticket: { id, title, jira, points, type, labels, category, owner, sprint, themeId,
          comments, goal, done, released, releasedDate }
```

## Files

- `index.html` — the entire application (markup, styles, and logic in one file).

## Running Locally

Just open `index.html` in a browser — no dependencies to install. Data is persisted via the app's live-sync layer, so opening the file directly will show whatever board state was last saved.
