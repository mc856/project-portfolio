# project-portfolio

**Long-term project memory for coding agents.**

Coding agents scope to one folder and one session. The things you actually pursue — a job hunt, several side projects, pull requests across repositories — span many folders and many months. Every new session starts with amnesia, and "what should I work on next?" gets answered by whatever folder happens to be open.

This skill gives your agent a persistent, cross-project state layer: plain markdown files you own, one per project, plus a global watchlist that makes syncing cheap and follow-ups automatic.

Ask your agent:

> *"What's the status of my projects? What should I do next?"*

and it reads the watchlist, runs each project's check recipe incrementally (only what changed since last sync), writes history back, and answers with prioritized next actions — across everything, not just the current folder.

## How it works

```
~/project-portfolio/
├── _watchlist.md      # last-sync date, date/event triggers, watched items
├── job-hunt.md        # one file per project: goal, items, next steps, history
├── acme-widgets.md
└── side-projects.md
```

- **Files are the database.** Human-readable, greppable, yours. No server, no lock-in — delete the skill and your data still makes sense.
- **The watchlist makes sync cheap.** Incremental checks driven by a last-sync date instead of polling every item; triggers fire once and are deleted.
- **Structure the agent can act on.** Each item carries a goal, a next step, a follow-up window, and an *impact one-liner* written at creation time — so a résumé/review summary is a compile, not an archaeology dig.

This is deliberately more structured than agent "memory" features: goals, triggers, and priorities are things memory won't maintain for you.

## Install

**Claude Code / Claude Cowork**

```bash
git clone https://github.com/mc856/project-portfolio ~/.claude/skills/project-portfolio
```

**Codex**

```bash
git clone https://github.com/mc856/project-portfolio ~/.agents/skills/project-portfolio
```

Then just talk to your agent: *"start tracking my job hunt"* or *"track my PRs on acme/widgets"*.

## Templates

| Template | For |
|---|---|
| [`project.md`](templates/project.md) | anything long-term — the generic shape |
| [`github.md`](templates/github.md) | your pull requests and issues across GitHub repos |
| [`job-hunt.md`](templates/job-hunt.md) | applications, interviews, follow-ups |
| [`side-projects.md`](templates/side-projects.md) | several personal projects competing for limited time |

Each template ships its own *check recipe* (how to fetch fresh state) and domain priority notes. Adding a new domain = writing one markdown file. PRs for new templates welcome.

## FAQ

**How is this different from CLAUDE.md / agent memory?** Those remember *how you like to work*, per repo. This tracks *where your endeavors stand*, across repos and outside repos entirely.

**How is this different from a dashboard like gh-dash?** A dashboard shows real-time state and forgets. This keeps history, goals, and triggers — and the agent acts on them.

**Where does my data live?** In `~/project-portfolio/` on your machine, plain markdown, outside any repo by default.

## License

MIT

---

*See also: [ContextSpec](https://github.com/mc856/ContextSpec) — portable, git-versioned role-context packs for coding agents (Claude Code / Codex).*
