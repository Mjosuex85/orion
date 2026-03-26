# Orion OS

> The operating system for AI-powered development teams.

---

## What is Orion OS?

Orion OS is a framework for building and orchestrating AI agent teams that develop real software. It's not a chatbot — it's a working methodology with roles, rules, memory, and automation.

The team operates like a development agency: each agent has a specialized role, follows clear rules, and communicates through GitHub Issues as the state layer.

## The hierarchy

```
Director / Founder (vision & business decisions)
  └── Orion (CTO / permanent orchestrator)
        └── Per project:
              ├── Nestor Senior (backend tech lead)
              │     ├── Dev backend
              │     ├── Tester
              │     └── Security
              └── Olga Senior (frontend tech lead)
                    ├── Dev frontend
                    └── QA
```

## Repository structure

```
orion/
  ├── README.md              → This file
  ├── ORION.md               → Orion's identity and global memory
  ├── DECISIONS.md           → Global rules (all projects)
  ├── agents/
  │   ├── NESTOR.md          → Nestor's system prompt (backend tech lead)
  │   └── OLGA.md            → Olga's system prompt (frontend tech lead)
  ├── projects/
  │   └── gameon.md          → GameOn project context (lab project)
  ├── skills/
  │   ├── universal/         → Apply to all agents and projects
  │   ├── backend/           → Reusable backend skills
  │   ├── frontend/          → Reusable frontend skills
  │   └── projects/          → Project-specific skills
  ├── templates/
  │   ├── new-project.md     → How to start a new project
  │   └── issue-template.md  → Standard issue format
  └── workflows/
      └── commit-log.yml     → GitHub Action for activity metrics
```

## How to use Orion OS on a new project

1. Copy `templates/new-project.md` and fill in the project context
2. Create project repos with `AGENTS.md` and `DECISIONS.md`
3. Set up GitHub Project (Kanban): Todo / In Progress / Done
4. Give each agent their system prompt from `agents/` + project context
5. Orion coordinates — agents execute — Director validates

## Scalability roadmap

```
Phase 1 → Manual framework + guided onboarding (now)
Phase 2 → Claude API as semi-automatic orchestration engine
Phase 3 → Fully automated system with human supervision
```

---

*Created by Mario Vidal + Orion — March 26, 2026*
*GameOn is the lab project where this framework is tested and refined.*
