# Orion OS

> The operating system for AI-powered development teams.
> **v1.5.0** — Multi-framework, session commands, monorepo/polyrepo, reversible decisions.

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
              ├── Frontend Tech Lead (Olga / React agent / etc.)
              │     └── Subagents: components, performance, accessibility
              └── QA Agent (Bruno)
                    └── Automated CI — runs on every PR
```

## Session commands

Three commands drive every interaction with Orion:

| Command | What happens |
|---|---|
| `"Orion iniciar proyecto"` | Wizard → choose stack → create repo + files + issue #1 |
| `"despierta Orion, vamos con <project>"` | Smart bootstrap → load context + skills for that project |
| `"Orion cierra sesión"` | Update docs + log session + confirm next priority |

Full reference: `workflows/commands.md`

## Core concepts

**Session commands** — Three explicit commands control the full session lifecycle: init, wake, close.

**Smart session init** — When waking up for a project, Orion loads the exact skills for that project's stack. React project → react-patterns. Angular project → angular-patterns. Never mixed.

**Reversible decisions (D89)** — Every technical decision must be reversible or explicitly documented as irreversible. Decisions that block the path to the v3.0.0 web product are flagged and justified. When two options exist, always prefer the one that doesn't close doors.

**Scaffold via GitHub MCP (D90)** — Orion creates project scaffolds by pushing files to the repo via GitHub MCP. Mario clones and runs `npm install` locally. This maps 1:1 to a future "Setup" button in the web UI — zero migration cost. Claude Code is optional for Mario, never required for a workflow to function.

**Multi-project architecture** — Orion OS manages multiple projects simultaneously. Each project has isolated context, decisions, skills, and repos.

**Multi-framework support** — Skills are framework-agnostic at the system level. Each project declares its own skill set. Supported: Angular, React. Planned: Vue, Svelte.

**Monorepo + polyrepo** — Project init supports both topologies.

**Issue-driven development** — GitHub Issues are the only communication channel between Orion and execution agents. Format: Context + Done + Agent Prompt + Skills + Test Plan.

**Decision registry** — Universal decisions in `DECISIONS.md`. Project-specific in `projects/<n>-decisions.md`.

## Repository structure

```
orion/
  ├── README.md                  → This file
  ├── ORION.md                   → Orion's identity, rules, session protocol
  ├── DECISIONS.md               → Universal decisions (all projects)
  ├── BOOT.md                    → Provisional session bootstrap for Claude Desktop
  │
  ├── agents/
  │   ├── AGENT_RULES.md         → Universal rules for all agents
  │   ├── DIRECTOR.md            → Founder profile
  │   ├── NESTOR.md              → Backend tech lead (NestJS)
  │   ├── OLGA.md                → Frontend tech lead (Angular)
  │   ├── BRUNO.md               → QA agent (CI-based)
  │   └── subagents/             → Specialized subagents
  │
  ├── projects/
  │   ├── gameon.md              → GameOn context (Angular + NestJS — polyrepo)
  │   ├── gameon-decisions.md
  │   ├── gameon-architecture.md
  │   ├── gameon-ideas.md
  │   ├── nutriapp.md            → NutriApp context (React + Supabase — monorepo)
  │   └── nutriapp-decisions.md
  │
  ├── logs/
  │   ├── sessions.jsonl
  │   ├── agent-feedback.jsonl
  │   └── post-mortems.jsonl
  │
  ├── rfcs/                      → Pending architecture decisions
  │
  ├── skills/
  │   ├── universal/             → git-flow, issue-reading
  │   ├── backend/               → nestjs-patterns, jwt-auth, typeorm-migrations
  │   ├── frontend/
  │   │   ├── angular-patterns.md
  │   │   ├── angular-signals.md
  │   │   ├── api-service.md
  │   │   └── react-patterns.md  → provider-agnostic service layer
  │   └── projects/
  │
  ├── templates/
  │   ├── project-context.md
  │   ├── project-decisions.md
  │   ├── claude-md.md
  │   ├── issue-template.md
  │   └── session-close.md
  │
  └── workflows/
      ├── commands.md            → Session commands (init, wake, close)
      ├── project-init.md        → Monorepo/polyrepo + stack selection
      ├── context-switch.md      → Smart skill loading on project switch
      ├── health-check.md
      ├── issue-quality.md
      ├── skill-injection.md
      ├── agent-feedback.md
      └── post-mortem.md
```

## The Orion lifecycle

```
Session start:
  "lee BOOT.md... vamos con <project>"
  → Read memory → Load project skills → Status summary

New project:
  "Orion iniciar proyecto"
  → Wizard → Confirm → Create repo + files + issue #1 (via GitHub MCP)

Scaffold execution:
  Orion pushes source files to repo via GitHub MCP
  Mario: git clone + npm install
  (maps to future web UI "Setup" button — D90)

Session close:
  "Orion cierra sesión"
  → Update project.md + log + confirm next priority
```

## Active projects

| Project | Stack | Topology | Status |
|---|---|---|---|
| GameOn | Angular 21 + NestJS + PostgreSQL | Polyrepo | v1.3.0 production |
| NutriApp | React 18 + Vite + Supabase | Monorepo | Initializing |

## Roadmap

```
v1.0.0  ✅  Foundation — manual framework, single project
v1.1.0  ✅  Separation of Concerns — framework decoupled from project
v1.2.0  ✅  Metrics & Observability — dashboard, logs, health checks
v1.3.0  ✅  Agent Intelligence — quality scoring, skill injection, feedback
v1.4.0  ✅  Multi-Project Ready — templates, init workflow, context switching
v1.5.0  ✅  Multi-Framework + Session Commands + Reversible Architecture
             React skills, monorepo support, commands.md, D89, D90
v2.0.0  ⬜  orion-os public repo — clean fork, no project data
v3.0.0  ⬜  Orion OS web interface — wizard, GitHub OAuth, zero config
```

---

*Created by Mario Vidal + Orion — March 2026*
*GameOn is the primary lab. NutriApp is the multi-framework proof of concept.*
*Every decision made today is a brick in the v3.0.0 product.*
