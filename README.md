# Orion OS

> The operating system for AI-powered development teams.
> **v2.0.0** — Public system layer, multi-framework agents, kanban per project, decision prefix convention.

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
| `"Orion iniciar proyecto"` | Wizard → choose stack → create repo + files + issue #1 |
| `"despierta Orion, vamos con <project>"` | Smart bootstrap → load context + skills for that project |
| `"Orion cierra sesión"` | Update docs + log session + confirm next priority |

Full reference: `workflows/commands.md`

## Core concepts

**Stack-scoped agent identities** — Olga has two identities: `OLGA.md` for Angular projects and `OLGA-REACT.md` for React projects. The `CLAUDE.md` of each repo tells her which to load. One repo, one agent, one session — never mixed.

**Smart session init** — When waking up for a project, Orion loads the exact skills for that project's stack. React → react-patterns. Angular → angular-patterns. Never mixed.

**Kanban per project (D96)** — Each active project has its own GitHub Project board at account level. Standard columns: Backlog → Ready → In progress → In review → Done. Orion references issues by repo, not by board URL (D97).

**Decision prefix convention (D99)** — Project-specific decisions use a prefix: `GN-D` (GameOn), `N-D` (NutriApp), `PM-D` (PortfolioMV). Universal decisions use bare `D`.

**Reversible decisions (D89)** — Every technical decision must be reversible or explicitly documented as irreversible.

**Scaffold via GitHub MCP (D90)** — Orion creates project scaffolds by pushing files to the repo via GitHub MCP. Mario clones and runs `npm install` locally.

**Multi-project architecture** — Each project has isolated context, decisions, skills, repos, and kanban. Max 2 issues In Progress per project simultaneously (D7).

**Issue-driven development** — GitHub Issues are the only communication channel between Orion and execution agents. Format: Context + Done + Agent Prompt + Skills + Test Plan.

**Decision registry** — Universal decisions in `DECISIONS.md`. Project-specific in `projects/<n>-decisions.md`.

## Repository structure

```
orion/
  ├── README.md                  → This file
  ├── ORION.md                   → Orion's identity, rules, session protocol
  ├── DECISIONS.md               → Universal decisions (all projects)
  │
  ├── agents/
  │   ├── AGENT_RULES.md         → Universal rules for all agents
  │   ├── DIRECTOR.md            → Founder profile
  │   ├── NESTOR.md              → Backend tech lead (NestJS)
  │   ├── OLGA.md                → Frontend tech lead (Angular — GameOn)
  │   ├── OLGA-REACT.md          → Frontend tech lead (React — PortfolioMV)
  │   ├── BRUNO.md               → QA agent (CI-based)
  │   └── subagents/
  │       ├── angular-component-architecture.md
  │       ├── angular-performance.md
  │       ├── angular-accessibility.md
  │       ├── react-component-architecture.md
  │       ├── ui-design-reviewer.md
  │       ├── nestjs-architecture.md
  │       ├── typeorm-migrations.md
  │       └── backend-security.md
  │
  ├── projects/
  │   ├── gameon.md              → GameOn (Angular + NestJS — polyrepo)
  │   ├── gameon-decisions.md
  │   ├── gameon-ideas.md
  │   ├── nutriapp.md            → NutriApp (React + Supabase — monorepo)
  │   ├── nutriapp-decisions.md
  │   ├── portfoliomv.md         → PortfolioMV (React CRA — single repo)
  │   └── portfoliomv-decisions.md
  │
  ├── logs/
  │   ├── sessions.jsonl
  │   ├── agent-feedback.jsonl
  │   └── post-mortems.jsonl
  │
  ├── rfcs/                      → Pending architecture decisions
  │
  ├── skills/
  │   ├── universal/
  │   ├── backend/
  │   └── frontend/
  │       ├── angular-patterns.md
  │       ├── angular-signals.md
  │       ├── api-service.md
  │       └── react-patterns.md
  │
  ├── templates/
  └── workflows/
      ├── commands.md            → Session + agent bootstrap protocol
      ├── project-init.md
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
v1.1.0  ✅  Separation of Concerns — framework decoupled from project
v1.2.0  ✅  Metrics & Observability — dashboard, logs, health checks
v1.3.0  ✅  Agent Intelligence — quality scoring, skill injection, feedback
v1.4.0  ✅  Multi-Project Ready — templates, init workflow, context switching
v1.5.0  ✅  Multi-Framework + Session Commands + Reversible Architecture
v2.0.0  ✅  Public system layer (orion-os), OLGA-REACT, kanban per project,
             agent bootstrap protocol, decision prefix convention (D96-D99)
v3.0.0  ⬜  Orion OS web interface — wizard, GitHub OAuth, zero config
```

---

*Created by Mario Vidal + Orion — March 2026*
*Last updated: May 4, 2026 — Session 34*
