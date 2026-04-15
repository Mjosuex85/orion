# HOW-TO-USE.md — Orion OS Operational Guide

> This file answers one question: **how do I actually use Orion OS?**
> It covers all scenarios: CLI setup, project creation, session start, and working within a project.
>
> **This file is a living document.** It is updated by Orion at every version bump.
> See the [Changelog](#changelog) section at the bottom.

---

## PREREQUISITES

- Claude.ai Pro **or** Claude CLI
- GitHub MCP connected
- For CLI: a local `CLAUDE.md` in your working directory (see Scenario 0)

---

## SCENARIO 0 — SET UP ORION IN CLAUDE CLI (one-time)

**When:** First time using Orion from the terminal.

1. Create a directory on your machine — your permanent Orion workspace:
   ```
   mkdir orion-cli
   cd orion-cli
   ```

2. Create a `CLAUDE.md` file inside with this exact content:
   ```
   You are Orion. Read CLAUDE.md from GitHub repo Mjosuex85/orion (main branch)
   via GitHub MCP and follow its boot sequence.
   ```

3. From that directory, always start Claude CLI:
   ```
   claude
   ```

4. Claude CLI reads your local `CLAUDE.md` → connects to `Mjosuex85/orion` → loads `ORION.md` + `DECISIONS.md` → Orion is live.

**This is a one-time setup. After that, just `cd orion-cli && claude` is enough.**

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
   - `projects/<n>.md` (project state)
   - `projects/<n>-decisions.md` (project decisions)
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

**When:** You want to work on a project that already exists.

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
1. Reads `projects/<n>.md` → current status, stack, open issues
2. Loads the right skills (Angular → angular-patterns. React → react-patterns)
3. Reads project-specific decisions
4. Runs a health check on the project repos
5. Gives you a status summary + asks where to start

**Works from both Claude.ai and Claude CLI.**

---

## SCENARIO 3 — WORK WITHIN A PROJECT

**Once a session is active**, this is the normal workflow:

### Creating work (issues)
```
"Orion, necesito implementar <feature>"
"Orion, hay un bug en <X>"
"Orion, idea: ¿podríamos hacer <Y>?"
```
- Orion analyzes technically, drafts the issue, quality check ≥ 8/10
- You confirm → Orion pushes to GitHub

### Assigning work to agents
```
"Asígnale el issue #XX a Nestor"
"Olga puede tomar el #XX"
```
- Orion assigns via GitHub MCP
- Agent bootstraps from `CLAUDE.md` → implements → "Ready to test"
- You test → green light → Orion closes the issue

### Switching projects
```
"cambiemos a NutriApp"
"vamos con GameOn"
```

### Closing a session
```
"Orion cierra sesión"
```
Orion updates `projects/<n>.md`, logs session, confirms next priority.

---

## QUICK REFERENCE

| I want to... | I say... |
|---|---|
| Set up CLI (one-time) | Create local `CLAUDE.md` — see Scenario 0 |
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

- Create repos or branches
- Write issue bodies
- Edit `projects/*.md` files
- Push docs or decisions
- Bootstrap agents

**Orion handles all of that. Your job is to decide, test, and validate.**

---

## ACTIVE PROJECTS

| Project | Stack | Topology | Primary contact | Since |
|---|---|---|---|---|
| GameOn | Angular 21 + NestJS | Polyrepo | Nestor (BE) + Olga (FE) | v1.0.0 |
| NutriApp | React + Supabase + Vite | Monorepo | Mario + Orion (Phase 1) | v1.5.0 |

---

## UPDATE PROTOCOL (D91)

This file is updated by Orion whenever:
- A new Orion OS version is released
- A new project is added or removed
- A command is created, changed, or deprecated
- A workflow changes how Mario interacts with the system

Mario never edits this file manually — Orion owns it.

---

## CHANGELOG

### v1.5.0 — April 15, 2026 — Session 24
- File created
- Scenarios 1–3: project creation, session start, in-session workflow
- Quick reference table, active projects table
- D91: versioning protocol defined
- Scenario 0 added: Claude CLI one-time setup
- `CLAUDE.md` created in `Mjosuex85/orion` for CLI boot with lazy loading

---

*Orion OS v1.5.0 — Last updated: April 15, 2026 — Session 24*
*Maintained by Orion. Source of truth: `Mjosuex85/orion` (main)*
