# Orion OS

> The operating system for AI-powered development teams.
> **v2.1.0** — Bootstrap modes (MODO CTO / MODO PROYECTO), modular project folders, system evolution tracking.

---

## What is Orion OS?

Orion OS is a framework for building and orchestrating AI agent teams that develop real software. It's not a chatbot — it's a working methodology with roles, rules, memory, intelligent agent management, and multi-project support.

The team operates like a development agency: each agent has a specialized role, follows clear rules, and communicates through GitHub Issues as the single state layer.

## The hierarchy

```
Director / Founder (vision & business decisions)
  └── Orion (CTO / permanent orchestrator)
        └── Per project:
              ├── Backend Tech Lead (Nestor)
              │     └── Subagents: architecture, migrations, security
              ├── Frontend Tech Lead (Olga)
              │     ├── OLGA.md       → Angular stack
              │     ├── OLGA-REACT.md → React stack
              │     └── Subagents: components, performance, accessibility
              └── QA Agent (Bruno)
                    └── Automated CI — runs on every PR
```

## Session commands

| Command | What happens |
|---|---|
| `"despierta Orion"` / `"necesito hablar contigo"` | MODO CTO → identity + decisions + evolution |
| `"despierta Orion, vamos con <project>"` | MODO PROYECTO → load context + skills for that project |
| `"Orion iniciar proyecto"` | Wizard → choose stack → create repo + files + issue #1 |
| `"Orion cierra sesión"` | Update docs + log session + confirm next priority |

Full reference: `workflows/commands.md`

## Core concepts

**Bootstrap modes** — Two distinct modes: MODO CTO (identity + decisions + evolution, no project) and MODO PROYECTO (full project context + skills). No default project — Orion always asks.

**Stack-scoped agent identities** — Olga has two identities: `OLGA.md` for Angular and `OLGA-REACT.md` for React. The `CLAUDE.md` of each repo tells her which to load. One repo, one agent, one session — never mixed.

**Modular project structure** — Each project lives in `projects/<name>/` with 4 standard files: `<name>.md`, `<name>-decisions.md`, `<name>-architecture.md`, `<name>-ideas.md`.

**Kanban per project (D96)** — Each project has its own GitHub Project board. Repo must be linked manually — see `workflows/config.md`.

**Decision prefix convention (D99)** — `GN-D` (GameOn), `N-D` (NutriApp), `PM-D` (PortfolioMV). Universal decisions use bare `D`.

**System evolution tracking** — `ORION-EVOLUTION.md` is the source of truth for how Orion grows. Read in MODO CTO. Contains pending items, learned patterns, open system decisions.

**Issue-driven development** — GitHub Issues are the only communication channel between Orion and execution agents.

## Repository structure

```
orion/
  ├── README.md                  → This file
  ├── ORION.md                   → Orion's identity, rules, session protocol
  ├── DECISIONS.md               → Universal decisions (all projects)
  ├── ORION-EVOLUTION.md         → System growth tracking (read in MODO CTO)
  ├── BOOT.md                    → Boot sequence (provisional until v3.0.0)
  ├── CHANGELOG.md               → Version history
  ├── ORION-OS-ROADMAP.md        → Roadmap toward v3.0.0
  │
  ├── agents/
  │   ├── AGENT_RULES.md
  │   ├── DIRECTOR.md
  │   ├── NESTOR.md
  │   ├── OLGA.md
  │   ├── OLGA-REACT.md
  │   ├── BRUNO.md
  │   └── subagents/
  │
  ├── projects/
  │   ├── gameon/
  │   │   ├── gameon.md
  │   │   ├── gameon-decisions.md
  │   │   ├── gameon-architecture.md
  │   │   └── gameon-ideas.md
  │   ├── nutriapp/
  │   │   ├── nutriapp.md
  │   │   ├── nutriapp-decisions.md
  │   │   ├── nutriapp-architecture.md
  │   │   └── nutriapp-ideas.md
  │   └── portfoliomv/
  │       ├── portfoliomv.md
  │       ├── portfoliomv-decisions.md
  │       ├── portfoliomv-architecture.md
  │       └── portfoliomv-ideas.md
  │
  ├── logs/
  │   ├── sessions.jsonl
  │   ├── agent-feedback.jsonl
  │   └── post-mortems.jsonl
  │
  ├── rfcs/
  ├── skills/
  │   ├── universal/
  │   ├── backend/
  │   └── frontend/
  │
  ├── templates/
  └── workflows/
      ├── commands.md
      ├── config.md              → Universal config (Kanban setup, conventions)
      ├── context-switch.md
      ├── health-check.md
      ├── issue-quality.md
      ├── skill-injection.md
      ├── agent-feedback.md
      ├── post-mortem.md
      └── security-incident.md
```

## Active projects

| Project | Stack | Topology | Issues repo | Status |
|---|---|---|---|---|
| GameOn | Angular 21 + NestJS + PostgreSQL | Polyrepo | gameon-api | v1.5.2 production |
| NutriApp | React 19 + Vite + Supabase | Monorepo | nutriapp | Phase 1 complete |
| PortfolioMV | React 18 + CRA | Single repo | portfolioMV | Active — features phase |

## Roadmap

```
v1.0.0  ✅  Foundation — manual framework, single project
v1.1.0  ✅  Separation of Concerns
v1.2.0  ✅  Metrics & Observability
v1.3.0  ✅  Agent Intelligence
v1.4.0  ✅  Multi-Project Ready
v1.5.0  ✅  Multi-Framework + Session Commands
v2.0.0  ✅  Public system layer (orion-os), OLGA-REACT, kanban per project
v2.1.0  ✅  Bootstrap modes, modular project folders, ORION-EVOLUTION.md
v3.0.0  ⬜  Orion OS web interface — wizard, GitHub OAuth, zero config
```

---

*Created by Mario Vidal + Orion — March 2026*
*Last updated: May 7, 2026 — Session 36*
