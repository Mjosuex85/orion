# Orion OS

> The operating system for AI-powered development teams.
> **v1.5.0** — Multi-framework, session commands, monorepo/polyrepo support.

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

**Session commands** — Three explicit commands control the full session lifecycle: init, wake, close. Orion executes them automatically without manual steps.

**Smart session init** — When waking up for a project, Orion loads the exact skills for that project's stack. React project → react-patterns. Angular project → angular-patterns. Never mixed.

**Multi-project architecture** — Orion OS manages multiple projects simultaneously. Each project has isolated context, decisions, skills, and repos. Switching is seamless with state preservation.

**Multi-framework support** — Skills are framework-agnostic at the system level. Each project declares its own skill set. Supported today: Angular, React. Planned: Vue, Svelte.

**Monorepo + polyrepo** — Project init supports both topologies. Polyrepo for independent deploy cycles (like GameOn). Monorepo for personal tools or minimal backends (like NutriApp).

**Issue-driven development** — GitHub Issues are the only communication channel between Orion and execution agents. Each issue follows a standard format: Context + Done + Agent Prompt + Skills + Test Plan.

**Agent intelligence** — Issues are quality-scored before assignment. Skills and subagents are injected based on scope. Agent performance is evaluated after every completion. Failures trigger post-mortems.

**Decision registry** — Every technical decision is documented. Universal decisions in `DECISIONS.md`, project-specific in `projects/<n>-decisions.md`.

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
  │   ├── OLGA.md                → Frontend tech lead (Angular)
  │   ├── BRUNO.md               → QA agent (CI-based)
  │   └── subagents/             → Specialized subagents
  │
  ├── projects/
  │   ├── gameon.md              → GameOn context (Angular + NestJS — polyrepo)
  │   ├── gameon-decisions.md    → GameOn-specific decisions
  │   ├── gameon-architecture.md
  │   ├── gameon-ideas.md
  │   ├── nutriapp.md            → NutriApp context (React + Supabase — monorepo)
  │   └── nutriapp-decisions.md  → NutriApp-specific decisions
  │
  ├── logs/
  │   ├── sessions.jsonl         → Structured session log
  │   ├── agent-feedback.jsonl   → Post-issue evaluations
  │   └── post-mortems.jsonl     → Failure analysis
  │
  ├── rfcs/                      → Pending architecture decisions
  │
  ├── skills/
  │   ├── universal/             → git-flow, issue-reading
  │   ├── backend/               → nestjs-patterns, jwt-auth, typeorm-migrations
  │   ├── frontend/
  │   │   ├── angular-patterns.md  → Angular 17+ patterns
  │   │   ├── angular-signals.md   → Signals state management
  │   │   ├── api-service.md       → HTTP service patterns
  │   │   └── react-patterns.md    → React 18 + Vite + Zustand patterns
  │   └── projects/              → Project-specific skills
  │
  ├── templates/
  │   ├── project-context.md     → Template for projects/<n>.md
  │   ├── project-decisions.md   → Template for projects/<n>-decisions.md
  │   ├── claude-md.md           → CLAUDE.md template for project repos
  │   ├── issue-template.md      → Standard issue format + quality score
  │   └── session-close.md       → Session close checklist
  │
  └── workflows/
      ├── commands.md            → Session commands reference (init, wake, close)
      ├── project-init.md        → New project init — monorepo/polyrepo variants
      ├── context-switch.md      → Multi-project switching + smart skill loading
      ├── health-check.md        → Session start health validation
      ├── issue-quality.md       → Issue quality scoring
      ├── skill-injection.md     → Skill & subagent mapping
      ├── agent-feedback.md      → Agent performance evaluation
      └── post-mortem.md         → Failure analysis & prevention
```

## The Orion lifecycle

```
Session start:
  "despierta Orion, vamos con <project>"
  → Read memory → Load project skills → Health check → Status summary

New project:
  "Orion iniciar proyecto"
  → Wizard → Confirm → Create repo + files + issue #1

Project switch:
  "cambiemos a <project>"
  → Save state → Load new context + skills → Health check → Confirm

Issue creation:
  Draft → Quality score → Skill injection → Assign

Agent execution:
  Bootstrap → Read skills → Implement → Self-review → "Ready to test"

Completion:
  Director validates → Agent commits → Orion evaluates → Feedback logged

Session close:
  "Orion cierra sesión"
  → Update state → Log session → Confirm next priority
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
v1.5.0  ✅  Multi-Framework + Session Commands — React skills, monorepo support, commands.md
v2.0.0  ⬜  orion-os public repo — clean fork, no project data, anyone can use it
v3.0.0  ⬜  Orion OS interface — web UI wizard for project init and session management
```

---

*Created by Mario Vidal + Orion — March 2026*
*GameOn is the primary lab. NutriApp is the multi-framework proof of concept.*
