# Orion OS

> The operating system for AI-powered development teams.
> **v1.2.0** — See [CHANGELOG.md](CHANGELOG.md) for version history.

---

## What is Orion OS?

Orion OS is a framework for building and orchestrating AI agent teams that develop real software. It's not a chatbot — it's a working methodology with roles, rules, memory, metrics, and automation.

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

**Separation of concerns** — Orion OS (the framework) is completely decoupled from any specific project. Universal decisions, agent definitions, and templates live at the root. Project-specific state, decisions, and context live in `projects/`.

**Issue-driven development** — GitHub Issues are the only communication channel between Orion and execution agents. Each issue follows a standard format: Context + Agent Prompt + Test Plan.

**Agent bootstrap chain** — Every agent starts the same way: `CLAUDE.md` (in the project repo) → `agents/<AGENT>.md` (from orion) → `AGENT_RULES.md` (from orion) → issue body.

**Decision registry** — Every technical decision is documented with its reasoning. Universal decisions in `DECISIONS.md`, project-specific in `projects/<n>-decisions.md`.

**Metrics & observability** — System performance is tracked with structured session logs, a metrics dashboard, and health checks at the start of every session.

## Repository structure

```
orion/
  ├── README.md                  → This file
  ├── ORION.md                   → Orion's identity, universal rules, session protocol
  ├── DECISIONS.md               → Universal decisions (all projects)
  ├── CHANGELOG.md               → Version history and roadmap
  │
  ├── agents/
  │   ├── AGENT_RULES.md         → Universal rules for all execution agents
  │   ├── DIRECTOR.md            → Founder profile
  │   ├── NESTOR.md              → Backend tech lead system prompt
  │   ├── OLGA.md                → Frontend tech lead system prompt
  │   ├── BRUNO.md               → QA agent (CI-based)
  │   └── subagents/             → 7 specialized subagents (reusable)
  │
  ├── projects/
  │   ├── gameon.md              → GameOn project context (active)
  │   ├── gameon-decisions.md    → GameOn-specific decisions
  │   ├── gameon-architecture.md → GameOn architecture docs
  │   └── gameon-ideas.md        → GameOn product backlog
  │
  ├── metrics/
  │   └── METRICS.md             → System dashboard (updated every session)
  │
  ├── logs/
  │   ├── sessions.jsonl         → Structured session log (machine-parseable)
  │   └── usage.jsonl            → Agent commit activity (auto-generated)
  │
  ├── rfcs/                      → Request for Comments (pending decisions)
  │
  ├── skills/
  │   ├── universal/             → Apply to all agents and projects
  │   ├── backend/               → Reusable backend skills
  │   ├── frontend/              → Reusable frontend skills
  │   └── projects/              → Project-specific skills
  │
  ├── templates/
  │   ├── new-project.md         → Checklist to start a new project
  │   ├── claude-md.md           → Standard CLAUDE.md template for project repos
  │   ├── issue-template.md      → Standard issue format
  │   ├── session-close.md       → Session close checklist
  │   └── architecture.md        → Architecture documentation template
  │
  └── workflows/
      ├── commit-log.yml         → GitHub Action: agent activity tracking
      └── health-check.md        → Session start health check protocol
```

## How to start a new project

1. Copy `templates/new-project.md` — follow the checklist
2. Create project repos with `CLAUDE.md` (from `templates/claude-md.md`)
3. Create `projects/<n>.md` and `projects/<n>-decisions.md` in this repo
4. Set up CI/CD, SonarCloud, and Bruno QA
5. Copy `workflows/commit-log.yml` to project repos for activity tracking
6. Orion coordinates — agents execute — Director validates

## Observability

Orion OS tracks system performance through three layers:

```
metrics/METRICS.md       → Human-readable dashboard (updated per session)
logs/sessions.jsonl      → Structured session data (machine-parseable)
logs/usage.jsonl         → Agent commit activity (auto-generated via GitHub Action)
workflows/health-check   → Pre-session health check (CI, PRs, issues, branches)
```

## Roadmap

```
v1.0.0  ✅  Foundation — manual framework, single project (GameOn)
v1.1.0  ✅  Separation of Concerns — framework decoupled from project
v1.2.0  ✅  Metrics & Observability — dashboard, structured logs, health checks
v1.3.0  ⬜  Agent Intelligence — skill injection, quality scoring, post-mortems
v1.4.0  ⬜  Multi-Project Ready — CLI scaffolding, context switching
v2.0.0  ⬜  Semi-Autonomous Orchestration — Orion as API
```

See [CHANGELOG.md](CHANGELOG.md) for full details on each version.

---

*Created by Mario Vidal + Orion — March 2026*
*GameOn is the lab project where this framework is tested and refined.*
