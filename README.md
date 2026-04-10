# Orion OS

> The operating system for AI-powered development teams.
> **v1.4.0** — See [CHANGELOG.md](CHANGELOG.md) for version history.

---

## What is Orion OS?

Orion OS is a framework for building and orchestrating AI agent teams that develop real software. It's not a chatbot — it's a working methodology with roles, rules, memory, metrics, intelligent agent management, and multi-project support.

The team operates like a development agency: each agent has a specialized role, follows clear rules, and communicates through GitHub Issues as the single state layer.

## The hierarchy

```
Director / Founder (vision & business decisions)
  └── Orion (CTO / permanent orchestrator)
        └── Per project:
              ├── Backend Tech Lead (Nestor)
              │     └── Subagents: architecture, migrations, security
              ├── Frontend Tech Lead (Olga)
              │     └── Subagents: components, performance, accessibility, design review
              └── QA Agent (Bruno)
                    └── Automated CI — runs on every PR
```

## Core concepts

**Multi-project architecture** — Orion OS manages multiple projects simultaneously. Each project has isolated context, decisions, and repos. Switching between projects is seamless with state preservation.

**Issue-driven development** — GitHub Issues are the only communication channel between Orion and execution agents. Each issue follows a standard format: Context + Agent Prompt + Skills + Test Plan.

**Agent intelligence** — Issues are quality-scored before assignment. Skills and subagents are injected based on scope. Agent performance is evaluated after every completion. Failures trigger post-mortems.

**Decision registry** — Every technical decision is documented. Universal decisions in `DECISIONS.md`, project-specific in `projects/<n>-decisions.md`.

**Metrics & observability** — Structured session logs, a metrics dashboard, health checks at session start, agent feedback tracking, and post-mortem analysis.

## Repository structure

```
orion/
  ├── README.md                  → This file
  ├── ORION.md                   → Orion's identity, rules, session protocol
  ├── DECISIONS.md               → Universal decisions (all projects)
  ├── CHANGELOG.md               → Version history and roadmap
  │
  ├── agents/
  │   ├── AGENT_RULES.md         → Universal rules (skills + subagent protocol)
  │   ├── DIRECTOR.md            → Founder profile
  │   ├── NESTOR.md              → Backend tech lead
  │   ├── OLGA.md                → Frontend tech lead
  │   ├── BRUNO.md               → QA agent (CI-based)
  │   └── subagents/             → 7 specialized subagents
  │
  ├── projects/
  │   ├── gameon.md              → GameOn project context (active)
  │   ├── gameon-decisions.md    → GameOn-specific decisions
  │   ├── gameon-architecture.md
  │   └── gameon-ideas.md
  │
  ├── metrics/
  │   └── METRICS.md             → System dashboard
  │
  ├── logs/
  │   ├── sessions.jsonl         → Structured session log
  │   ├── usage.jsonl            → Agent commit activity
  │   ├── agent-feedback.jsonl   → Post-issue evaluations
  │   └── post-mortems.jsonl     → Failure analysis
  │
  ├── rfcs/                      → Pending decisions
  │
  ├── skills/
  │   ├── universal/             → git-flow, issue-reading
  │   ├── backend/               → nestjs-patterns, jwt-auth, typeorm-migrations
  │   ├── frontend/              → angular-signals, api-service
  │   └── projects/              → Project-specific skills
  │
  ├── templates/
  │   ├── new-project.md         → Full project setup checklist
  │   ├── project-context.md     → Template for projects/<n>.md
  │   ├── project-decisions.md   → Template for projects/<n>-decisions.md
  │   ├── claude-md.md           → CLAUDE.md for project repos
  │   ├── issue-template.md      → Standard issue format + quality score
  │   ├── session-close.md       → Session close checklist
  │   ├── architecture.md        → Architecture documentation template
  │   ├── ci-backend.yml         → CI template for NestJS
  │   ├── ci-frontend.yml        → CI template for Angular
  │   └── bruno.yml              → Bruno QA template
  │
  └── workflows/
      ├── project-init.md        → New project initialization (5 phases)
      ├── context-switch.md      → Multi-project context switching
      ├── commit-log.yml         → Agent activity tracking
      ├── health-check.md        → Session start health validation
      ├── issue-quality.md       → Issue quality scoring
      ├── skill-injection.md     → Skill & subagent mapping
      ├── agent-feedback.md      → Agent performance evaluation
      └── post-mortem.md         → Failure analysis & prevention
```

## The Orion lifecycle

```
Session start:
  Read memory → Health check → Status summary

New project:
  Define → Init repos → Configure → First issues → Validate

Project switch:
  Save state → Load new context → Health check → Confirm

Issue creation:
  Draft → Quality score → Skill injection → Assign

Agent execution:
  Bootstrap → Read skills → Implement → Self-review → "Ready to test"

Completion:
  Director validates → Agent commits → Orion evaluates → Feedback logged

Failure:
  Trigger → Post-mortem → Prevention applied → System improved

Session close:
  Update state → Log session → Update metrics → Confirm
```

## How to start a new project

1. Mario creates backend + frontend repos
2. Orion follows `workflows/project-init.md` (15-30 min)
3. Uses templates for all config: CLAUDE.md, CI, Bruno, SonarCloud
4. Creates `projects/<n>.md` + `projects/<n>-decisions.md`
5. Creates first issues for agents
6. Validates: CI, Bruno, SonarCloud all operational
7. Project is live under Orion OS

## Roadmap

```
v1.0.0  ✅  Foundation — manual framework, single project
v1.1.0  ✅  Separation of Concerns — framework decoupled from project
v1.2.0  ✅  Metrics & Observability — dashboard, logs, health checks
v1.3.0  ✅  Agent Intelligence — quality scoring, skill injection, feedback
v1.4.0  ✅  Multi-Project Ready — templates, init workflow, context switching
v2.0.0  ⬜  Semi-Autonomous Orchestration — Orion as API
```

See [CHANGELOG.md](CHANGELOG.md) for full details.

---

*Created by Mario Vidal + Orion — March 2026*
*GameOn is the lab project where this framework was born.*
