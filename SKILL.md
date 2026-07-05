---
name: project-portfolio
description: Track and manage the user's long-term projects across folders and sessions — a local, file-based state layer holding each project's goal, status, next actions, triggers, and history. Use this skill whenever the user asks what is happening across their projects ("status of my projects", "what should I work on next", "weekly review", "sync my projects"), wants to start tracking a new long-term effort (a job hunt, side projects, GitHub pull requests, writing a book, a home renovation), reports progress or a new event on something they are pursuing over weeks or months, or asks for a summary of what they have accomplished. Also use it at the end of a work session on any tracked project to record what changed — even if the user only says "log this" or "remember where we left off".
---

# Project Portfolio

A file-based memory layer for long-term projects. Coding agents naturally scope to one folder and one session; the things people actually pursue — a job search, several side projects, contributions across repositories — span many folders and many months. This skill closes that gap with plain markdown files the user owns: one file per project, plus a global watchlist that makes syncing cheap and follow-ups automatic.

## Data home

All state lives in `~/project-portfolio/` (resolve the user's home directory for the current environment; in sandboxes it may be mounted elsewhere).

- One file per project, named with a short slug: `job-hunt.md`, `blog-rewrite.md`, `acme-widgets.md`.
- `_watchlist.md` holds global sync state and triggers (see below).
- Create the directory and files on first use. Keep this directory outside any git repository by default — the data belongs to the user alone. If the user wants it versioned or synced across machines, let them opt in.

When the user starts tracking a new project, create its file by **routing to a template and bootstrapping from evidence** — see "Creating a project file" below.

## Creating a project file

**Route to a template.** Infer the domain from what the user says and — when the project has a folder — its actual contents (README, recent commits, open TODOs). Match against the table at the end of this file; no clear match means `templates/project.md`, the generic shape. Templates are starting shapes, not schemas: add or drop fields freely per project. The engine only relies on a few conventions being present — `Status`, `last_checked`, `Next step`, the impact one-liner, a dated `History` list, and a `Check recipe` — everything else is domain flavor.

**Bootstrap from evidence, not from a blank form.** Making the user hand-fill a template is the main reason tracking systems die on day one. Draft the file from what already exists: the current conversation, the project folder (README, `git log`, TODO comments), and any agent memory available in the environment (don't assume a specific memory location or format — capabilities differ across agents). Two hard rules: **show the draft and get the user's confirmation before writing it to disk**, and **never invent a goal** — if the goal isn't evident from the material, ask; an aspirational-sounding but wrong goal poisons every future priority ranking.

## Sync entry point: `_watchlist.md`

For any status or "what's next" request, read `_watchlist.md` first. It exists so the user never has to supply dates or parameters from memory:

- **Last full sync** — a date field. Use it as the `SINCE` value for incremental checks, so you only look at what changed since then. Always compare inclusively (`>=`), not strictly (`>`): a strict comparison at day granularity silently drops events that happened later on the sync day itself. Duplicates are harmless — they deduplicate against the project files.
- **Date triggers** — "on or after YYYY-MM-DD, do X" (e.g. "2026-07-20: follow up with recruiter if no reply"). Check each one against today.
- **Event triggers** — "when X happens, do Y" (e.g. "when release 2.0 ships, rebase the branch"). Check each against fresh data.
- **Watched items** — things owned by other people that incremental "involves me" style queries won't surface. Check these explicitly.

After every sync: update **Last full sync** to today, delete triggers that fired or expired, and append one history line to each affected project file. A trigger that fires twice nags; a sync date that isn't updated makes the next sync expensive. This bookkeeping is the whole mechanism — never skip it.

If `_watchlist.md` doesn't exist (fresh environment), create it from `templates/_watchlist.md`, then fall back to reading every project file individually.

## Sync workflow

1. Read `_watchlist.md`, then the project files in scope (all of them, unless the user named one).
2. For each project, run its **check recipe** — the per-project section that says how to fetch fresh external state (a `gh` query, a calendar check, an inbox search, or simply "ask the user"). Domain templates ship good recipes; prefer incremental queries driven by the last-sync date over polling every item — it is routinely a 10x reduction in calls.
3. Diff fresh state against each file's snapshot (status fields, counts, `last_checked`).
4. Write changes back: update status fields, append dated history lines (`YYYY-MM-DD event`), refresh `last_checked`.
5. Report: changes first (say explicitly if there were none), then next actions ordered by priority.

## Prioritizing next actions

When the user asks "what should I do next", rank across all projects and explain the reasoning:

1. **A person is waiting on the user** — a reply owed, a review requested, a question asked. Responsiveness compounds; silence decays trust. Same-day if possible.
2. **Something is broken or blocking** — a failing check, an expired document, a stalled dependency the user can unstick.
3. **A trigger fired** — the user set it for a reason; honor it or consciously retire it.
4. **A follow-up is due** — an item quiet past its stated follow-up window gets exactly one polite nudge, then patience. Repeated chasing backfires in almost every domain.
5. **All quiet** — recommend investing in whichever project's stated goal has the largest gap to close, and say why.

## Discipline

- One-shot actions are one-shot: a follow-up already sent is recorded in history and never re-suggested.
- On a mismatch between a file and reality (an item closed that the file says is open, a state someone else changed), stop and report before writing anything — don't guess and continue.
- Write the **impact one-liner** the moment a record is created ("cut cold-start time 40% for ~2k users", "landed interview at X via Y"). It is what makes the evidence report below instant instead of an archaeology dig.

## Evidence report (on request only)

When the user wants a résumé/portfolio/annual-review summary: compile every completed record's impact one-liner with its link, ordered by significance. Refresh any live numbers (stars, metrics) at compile time rather than trusting stale snapshots in the files.

## Templates

| Template | Use when the user is tracking… |
|---|---|
| `templates/project.md` | anything long-term that doesn't fit below — the generic shape |
| `templates/github.md` | their pull requests and issues across GitHub repositories |
| `templates/job-hunt.md` | applications, interviews, offers, follow-ups |
| `templates/side-projects.md` | several personal projects competing for limited time |

Read the chosen template in full before creating a project file — each one carries its own check recipe and field conventions.
