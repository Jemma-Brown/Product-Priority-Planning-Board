# Product Priority Planning Board

A collaborative, real-time sprint planning board built as a single HTML file, backed by Supabase.

## Features

### Tabs
- **CY 2026 / CY 2027** — Timeline and sprint detail views for each calendar year. Sprint 118 (24 Dec 2026–6 Jan 2027) appears in both as it spans the year boundary.
- **Progress** — Kanban-style board showing themes grouped by owner (To Do / In Progress / Done).
- **Planner** — Cross-sprint planning grid with rows per person and columns per sprint. Supports drag-and-drop reordering between sprints.

### Themes
- Create themes with owner, start/end sprint, priority, and status (To Do / WIP / Done)
- Colour-coded bars on the timeline grid, with automatic multi-lane layout to prevent overlaps
- Click any theme bar to edit

### Tickets
- Assign tickets to a person, sprint, category (Data / Webi / Dashboard), and theme
- Status labels: **Ticket Creation** (red), **Ready To Refine** (amber), **Ready for Sprint** (green)
- Refinement session labels (Session 1–3)
- Story points shown as a circle badge on each ticket
- Jira key field with one-click import via bookmarklet
- Duplicate Jira key prevention
- Drag tickets between categories and sprints

### Sprint Detail View
- Click any sprint header to drill into it
- Tickets grouped by owner → theme, with colour-coded status
- Capacity, remaining points, unpointed, Ready To Refine, and Ticket Creation counts per category
- Navigate between sprints with prev/next arrows or dropdown

### Planner
- Full cross-sprint grid: rows = person + theme, columns = all sprints
- Filter by theme, person, status label, refinement session, or category
- Drag tickets between sprint columns or reorder within a sprint
- Deadlines: add calendar deadlines assigned to a person and/or theme, auto-placed in the correct sprint column based on date
- "Deadlines only" toggle for a clean calendar view

### Collaboration
- Real-time sync via Supabase (polling every 10 seconds)
- Online presence indicator showing who is currently active
- Last updated timestamp
- Undo (Ctrl+Z) with up to 30 steps

### Sprint Goals
- Add freetext goals per sprint, draggable to reorder
- Goals shown in the timeline header for each sprint

## Setup

### Prerequisites
- A [Supabase](https://supabase.com) project with the following tables:

```sql
create table pb_themes (id text primary key, data jsonb);
create table pb_tickets (id text primary key, data jsonb);
create table pb_meta (key text primary key, value text);
```

- Enable Row Level Security and add appropriate policies, or disable RLS for internal use.

### Configuration

Open `index.html` and update the Supabase credentials near the top of the `<script>` tag:

```js
const SB_URL = 'https://your-project.supabase.co';
const SB_KEY = 'your-anon-key';
```

### Jira Integration

A bookmarklet is included to push Jira tickets directly into the board. Drag the **Send to Board** link from the Jira settings panel to your browser bookmarks bar, then click it on any Jira ticket page.

### Deployment

The entire app is a single `index.html` file — no build step required. Host it anywhere static files are served: GitHub Pages, Netlify, Vercel, or an S3 bucket.

For GitHub Pages, simply push `index.html` to the `main` branch of your repository and enable Pages in the repository settings.

## Tech Stack

- **Frontend** — Vanilla HTML/CSS/JS, no framework
- **Backend** — [Supabase](https://supabase.com) (Postgres + REST API)
- **Icons** — [Tabler Icons](https://tabler.io/icons)

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+Z` / `Cmd+Z` | Undo last change |
| `Escape` | Close any open modal |
| `Enter` (in sprint goal) | Save goal |

## Notes

- All data is stored in Supabase and shared in real time across all users
- The app uses polling rather than websockets for simplicity
- Undo history is per-session and not synced across users
