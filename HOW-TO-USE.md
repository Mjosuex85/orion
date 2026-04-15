# HOW-TO-USE.md — Orion OS Operational Guide

> This file answers one question: **how do I actually use Orion OS?**
> It covers the three main scenarios: create a new project, start a session on an existing project, and work within a project.

---

## PREREQUISITES

Before anything:
- Claude.ai Pro with a **Project** configured
- GitHub MCP connected to Claude
- The Orion OS project instructions loaded (system prompt referencing `ORION.md`, `DECISIONS.md`, `ORION-OS-ROADMAP.md`)

---

## SCENARIO 1 — CREATE A NEW PROJECT

**When:** You want to start a brand new product from scratch.

**Command:**
```
Orion iniciar proyecto
```

**What happens:**
1. Orion runs a wizard — asks you:
   - Project name
   - Topology: monorepo or polyrepo
   - Frontend framework (Angular, React, etc.)
   - Backend (NestJS, serverless, etc.)
   - Database
   - Auth
   - Deploy target
2. Orion shows you the plan — you confirm
3. Orion creates via GitHub MCP:
   - Repo(s) with `develop` + `main` branches
   - `CLAUDE.md` (agent instructions)
   - `projects/<name>.md` (project state)
   - `projects/<name>-decisions.md` (project decisions)
   - Issue #1: environment setup + agent bootstrap
4. You run locally:
   ```bash
   git clone <repo>
   npm install
   ```
5. Done — project is live in Orion OS

**You do NOT need to touch any files manually. Orion handles all of it.**

---

## SCENARIO 2 — START A SESSION ON AN EXISTING PROJECT

**When:** You open Claude and want to work on a project that already exists.

**Command:**
```
despierta Orion, vamos con <project-name>
```

Examples:
```
despierta Orion, vamos con GameOn
despierta Orion, vamos con NutriApp
```

Or if you just want the primary project (GameOn by default):
```
Orion despierta
```

**What Orion does automatically:**
1. Reads `ORION.md` + `DECISIONS.md` from the repo
2. Reads `projects/<name>.md` → gets current status, stack, open issues
3. Loads the right skills (Angular? → angular-patterns. React? → react-patterns)
4. Reads project-specific decisions
5. Runs a health check on the project repos
6. Gives you a status summary + asks where to start

**You arrive ready to work in under 2 minutes.**

---

## SCENARIO 3 — WORK WITHIN A PROJECT

**Once a session is active**, this is the normal workflow:

### Creating work (issues)
```
"Orion, necesito implementar <feature>"
"Orion, hay un bug en <X>"
"Orion, idea: ¿podríamos hacer <Y>?"
```
- Orion analyzes the request technically
- Drafts the issue (English, with context + agent prompt + test plan)
- Quality check ≥ 8/10 before assignment
- You confirm → Orion pushes to GitHub

### Assigning work to agents
```
"Asígnale el issue #XX a Nestor"
"Olga puede tomar el #XX"
```
- Orion assigns the issue via GitHub MCP
- Agent reads `CLAUDE.md` from the repo → bootstraps
- Agent reads the issue body → implements → says "Ready to test"
- You test → give green light → Orion closes the issue

### Switching between projects mid-session
```
"cambiemos a NutriApp"
"vamos con GameOn"
```
- Orion saves current project state first
- Loads the new project context + skills
- Confirms switch is complete

### Closing a session
```
"Orion cierra sesión"
```
Or Orion does it automatically at conversation end.

Orion will:
1. Update `projects/<name>.md` with current status
2. Log the session in `logs/sessions.jsonl`
3. Push any pending decisions
4. Confirm: `"Sesión cerrada. Próxima prioridad: [X]."`

---

## QUICK REFERENCE

| I want to... | I say... |
|---|---|
| Create a new project | `Orion iniciar proyecto` |
| Start working on GameOn | `despierta Orion, vamos con GameOn` |
| Start working on NutriApp | `despierta Orion, vamos con NutriApp` |
| Load default project | `Orion despierta` |
| Switch projects | `cambiemos a <project>` |
| Create a new feature | `Orion, necesito implementar X` |
| Report a bug | `Orion, hay un bug en X` |
| Propose an idea | `Orion, idea: ¿podríamos hacer X?` |
| Close the session | `Orion cierra sesión` |

---

## WHAT YOU NEVER NEED TO DO MANUALLY

- Create repos or branches manually
- Write issue bodies
- Edit `projects/*.md` files
- Push docs or decisions
- Bootstrap agents (they read `CLAUDE.md` themselves)

**Orion handles all of that. Your job is to decide, test, and validate.**

---

## ACTIVE PROJECTS (v1.5.0)

| Project | Stack | Topology | Primary contact |
|---|---|---|---|
| GameOn | Angular 21 + NestJS | Polyrepo | Nestor (BE) + Olga (FE) |
| NutriApp | React + Supabase + Vite | Monorepo | Mario + Orion (Phase 1) |

---

*Orion OS v1.5.0 — Created: April 15, 2026 — Session 24*
*Maintained by Orion. Source of truth: `Mjosuex85/orion` (main)*
