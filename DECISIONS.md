<!--
DECISIONS.md · protocol (any agent editing this file follows these rules — vendor-neutral)
Spec: v0.1-draft · https://github.com/mc856/decision-records
1. Entries are append-only; the only mutable field is "status".
   Overturning a decision = add a new entry + set the old one to superseded-by D-xxx.
2. Status values: active / superseded-by D-xxx / expired.
   This file contains only entries the user has ratified.
3. Entry fields: status, decided-by, alternatives, reasoning@constraints, regret-when, review.
   regret-when must be checkable in one sentence at review time; omit it otherwise.
4. Reasons must come from real conversation or material — never invent or backfill them.
5. When citing this file for a new decision: surface it to the user and ask.
   Never silently reuse an old decision for a new choice.
6. Reviews are handled in a monthly batch; this file carries no reminders of its own.
7. Non-active entries move to the Archive section. Active stays readable in one screen.
-->

# DECISIONS.md: project-portfolio

## Active

### D-260708-single-watchlist — One watchlist only; domain trackers demote to extensions
- status: active
- decided-by: user-ratified (Claude recommended; executed same week)
- alternatives: each skill keeps its own global watchlist
- reasoning@constraints: two global tables made the same trigger fire twice in two places; the generic mechanism had already been folded into the github.md template, leaving the domain tracker only its domain-specific judgment knowledge
- regret-when: the portfolio↔tracker pointer chain breaks or grows complicated during real syncs
- review: 2026-08-10

## Archive
