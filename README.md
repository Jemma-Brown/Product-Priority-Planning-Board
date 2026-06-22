# Product Priority Planning Board

A single-file, no-build sprint and roadmap planning board. It runs entirely as one `index.html`, hosted on **GitHub Pages**, with **Supabase** as the shared backend so the whole team sees the same board in real time (via polling, not websockets).

## What it does

- **Roadmap timeline** — themes (goals/epics) plotted across sprints for the current and next calendar year, grouped by owner.
- **Sprint tickets** — individual tickets with priority, story points, category, status labels (`Ticket Creation`, `Ready To Refine`, `Ready for Sprint`, `Ready to Test`), refinement session, and an optional 🎯 goal flag.
- **Progress board** — a simple To do / In progress / Done view of themes.
- **Planner** — a filterable, cross-sprint view of every ticket (by theme, owner, status label, refinement session, category, or deadline), with drag-to-reorder.
- **Capacity view** — per-owner, per-sprint point totals against a configurable cap.
- **Jira bookmarklet** — drag the "Send to Board" bookmarklet from the Jira settings panel into your bookmarks bar. Clicking it on an open Jira ticket scrapes the key fields (summary, points, labels, sprint, reporter) and opens the "Add ticket" form on the board pre-filled. If a board tab is already open, it's reused in place rather than reloaded — works for sending several tickets in a row.
- **Live collaboration** — the board polls Supabase every 5 seconds for changes, shows who else is currently viewing, and supports undo (Ctrl+Z) for the last 30 changes.
- **Dark mode**, archive view for past sprints, and a holiday/deadline overlay on the planner.

## Tech

Plain HTML/CSS/JS — no framework, no build step, no `node_modules`. Icons via [Tabler Icons](https://tabler.io/icons) (CDN). Everything lives in `index.html`.

## Setup

### 1. Create the Supabase tables

In your Supabase project's SQL editor, create three tables. Each one just stores an id and a JSON blob — the app does all the shaping client-side:

```sql
create table pb_themes (
  id   text primary key,
  data jsonb not null
);

create table pb_tickets (
  id   text primary key,
  data jsonb not null
);

create table pb_meta (
  key   text primary key,
  value text
);
```

`pb_meta` is used for shared settings (Jira base URL, capacity caps, goals, deadlines, holidays, last-updated timestamp) and for lightweight presence/heartbeat records, keyed like `presence_<user>` and `viewer_<name>`.

Enable Row Level Security and add policies allowing the anon/publishable key to `select`, `insert`, `update`, and `delete` on all three tables — anyone with the link and the publishable key can read and write the board, so don't put anything sensitive in it.

### 2. Point the app at your Supabase project

In `index.html`, set your project URL and **publishable** (anon) key:

```js
const SB_URL = 'https://YOUR_PROJECT.supabase.co';
const SB_KEY = 'YOUR_PUBLISHABLE_KEY';
```

Only use the publishable/anon key here — this file is public once deployed, so never put a service role key in it.

### 3. Deploy to GitHub Pages

1. Push this repo to GitHub.
2. In **Settings → Pages**, set the source to the branch/folder containing `index.html` (e.g. `main` / `/root`).
3. GitHub will publish it at `https://<your-username>.github.io/<repo>/`.

No build step is required — Pages serves the static file as-is.

### 4. Connect Jira (optional)

Open the **Jira** settings panel in the app header and set your Jira base URL (e.g. `https://yourcompany.atlassian.net`). This is only used to build links and the bookmarklet — no Jira credentials or API access are required, since the bookmarklet reads ticket fields directly from the page DOM while you're logged into Jira in your browser.

## Usage notes

- The first person to open the board picks a name from the user switcher; this is stored locally (`localStorage`) and used to attribute changes and presence, not as an authentication system.
- "Ready to Test", "Ready To Refine", "Ready for Sprint", and "Ticket Creation" are mutually exclusive status labels — selecting one clears the others.
- Undo only covers theme/ticket changes, not settings like capacity or holidays.
- Because sync is poll-based (every 5 seconds) rather than realtime, there can be a few seconds' delay before changes from teammates appear.

## Limitations

- No authentication — anyone with the URL and Supabase publishable key can read and write the board. Treat the link as you would an editable shared document.
- No conflict resolution beyond last-write-wins per row.
- Designed for a small team; it isn't built to scale to large ticket volumes.
