# Orion OS — Session Commands

> These are the core commands Mario uses to interact with Orion OS.
> Orion recognizes these triggers and executes the corresponding workflow automatically.

---

## COMMAND 1 — "Orion iniciar proyecto"

**Triggers:**
- `"Orion iniciar proyecto"`
- `"Orion nuevo proyecto"`
- `"Orion init project"`

**What Orion does — step by step:**

### Step 1 — Ask questions (one at a time, wait for answer)

```
1. ¿Nombre del proyecto?
2. ¿Topología?
   → A) Monorepo (frontend + backend en el mismo repo)
   → B) Polyrepo (repos separados)
3. ¿Framework frontend?
   → React / Angular / Vue / Svelte
4. ¿Backend?
   → A) NestJS
   → B) Express
   → C) Ninguno (SDK directo desde frontend)
5. ¿Base de datos?
   → Supabase (PostgreSQL) / Firebase Firestore / PostgreSQL propio / SQLite / Ninguna
6. ¿Autenticación?
   → Supabase Auth / Firebase Auth / JWT propio / Ninguna (Phase 1)
7. ¿Deploy?
   → Vercel / Railway / Firebase Hosting / Otro
8. ¿Creo el repo en GitHub ahora?
   → Sí / No (ya lo creé)
```

### Step 2 — Confirm before executing

Orion shows a summary:
```
Proyecto:   <n>
Topología:  monorepo / polyrepo
Frontend:   <framework>
Backend:    <backend>
DB:         <db>
Auth:       <auth>
Deploy:     <deploy>
Repo:       crear / ya existe
```
Then asks: **"¿Todo correcto? Dame luz verde."**

### Step 3 — Execute (only after Mario confirms)

```
1. Create repo via GitHub MCP (if requested)
2. Create develop branch
3. Create projects/<n>/<n>.md (from template, filled with chosen stack)
4. Create projects/<n>/<n>-decisions.md (initial decisions based on choices)
5. Create projects/<n>/<n>-architecture.md (placeholder)
6. Create projects/<n>/<n>-ideas.md (placeholder)
7. Push CLAUDE.md + .npmrc + .gitattributes + .env.example to develop
8. Update ORION.md — add project to REPOS + active projects list
9. Create issue #1 — project scaffold based on chosen stack
10. Mario creates GitHub Project (Kanban) + links repo — see workflows/config.md (D96)
```

### Output

```
"Proyecto <n> inicializado bajo Orion OS.
Repo: Mjosuex85/<n>
Issue #1 abierto: <title>
Kanban: crear manualmente en GitHub Projects > <n> → vincular repo → workflows/config.md
Próximo paso: ejecuta el issue #1."
```

---

## COMMAND 2 — Bootstrap de sesión

**MODO CTO**

Triggers:
- `"despierta Orion"`
- `"necesito hablar contigo"`
- Strategic or architectural discussion (no project work)

```
1. Read ORION.md          → Mjosuex85/orion (main)
2. Read DECISIONS.md      → Mjosuex85/orion (main)
→ No project. No skills. No health check.
→ Ready to discuss architecture, strategy, system.
```

---

**MODO PROYECTO**

Triggers:
- `"despierta Orion, vamos con <project>"`
- `"hola Orion, trabajamos en <project>"`
- `"Orion vamos con <project>"`

```
1. Read ORION.md                              → Mjosuex85/orion (main)
2. Read DECISIONS.md                          → Mjosuex85/orion (main)
3. Read projects/<project>/<project>.md       → stack + skills + issues repo
4. Read projects/<project>/<project>-decisions.md
5. Load skills declared in project            → only if coding session
6. Health check (workflows/health-check.md)
7. Status summary: current state + open issues + active priorities
8. "Listo. ¿Por dónde arrancamos?"
```

**Rules:**
- If Mario does not specify project → Orion asks which one. No default.
- Skills are project-scoped — never load Angular skills for a React project
- If switching mid-session → follow workflows/context-switch.md

---

## COMMAND 3 — "Orion cierra sesión"

**Triggers:**
- `"Orion cierra sesión"`
- `"Orion close session"`
- `"cerramos Orion"`

**What Orion does — session close protocol:**

```
1. Show diff for projects/<project>/<project>.md
   → What changed: issues closed, decisions made, priorities updated
   → Ask Mario: "¿Hago push?"

2. On Mario's green light, push in this exact order:
   a. projects/<project>/<project>.md          — status, features, pending (D87)
   b. projects/<project>/<project>-decisions.md — new technical decisions (ALWAYS)
   c. logs/sessions.jsonl                       — append structured entry
   d. ORION.md SESSION LOG                      — one-liner only (see format below)

3. Confirm:
   "Sesión cerrada. Próxima prioridad: [X]."
```

### SESSION LOG format — ORION.md (CRITICAL)

> The SESSION LOG in ORION.md is a timeline of Orion OS — not a project log.
> **One line per session. No project detail. No feature lists.**

```
### Session XX — Month DD, YYYY
<Project>: <one sentence summary>. <key decisions if any>.
```

Examples of CORRECT entries:
```
### Session 24 — April 16, 2026
NutriApp Phase 1 complete. Plan, cook flow, stock deduction, UI redesign. N-D16 to N-D21.
```

Examples of WRONG entries (never do this):
```
### Session 24 — April 16, 2026
- Monthly Plan page: Hoy/Semana/Mes views, week navigation, month grid
- Auto-generate: Fisher-Yates shuffle
...
```

### projects/<project>/<project>-decisions.md — ALWAYS updated at close

All new technical decisions made during the session go here — no exceptions.
Format: `<PREFIX>-Dxx. <Decision title>` with context and reason.
This is not optional. If decisions were made, they must be documented.

### logs/sessions.jsonl — entry format

```json
{"session": N, "date": "YYYY-MM-DD", "project": "<project>", "issues_closed": ["#N"], "issues_created": [], "decisions": ["short description"], "summary": "one sentence", "orion_os_version": "x.x.x"}
```

**Rules:**
- Never close without updating projects/<project>/<project>.md (D87)
- Always show diff before pushing — Mario decides what goes in
- If session was short and nothing changed → say so, no unnecessary push
- ORION.md SESSION LOG = one-liner only. The detail lives in project files.

---

## COMMAND 4 — Starting an agent (Olga / Nestor)

> This is not a command to Orion — it's the protocol Mario follows to start any agent
> cleanly, without context pollution.

### The rule

```
Orion    → trigger phrase in Claude.ai
Agents   → CLAUDE.md of the repo does the bootstrap
           Mario only gives the issue number
```

### How to start Olga — React (PortfolioMV, Antigravity)

1. Open Antigravity with `portfolioMV` repo loaded
2. Olga reads `CLAUDE.md` automatically on startup
3. She bootstraps: reads `OLGA-REACT.md` + `AGENT_RULES.md` from `Mjosuex85/orion`
4. She posts her **Read Log** confirming what she read
5. Mario gives the issue number — nothing else
6. Olga reads the issue body via GitHub MCP and begins

### How to start Olga — Angular (GameOn, Antigravity)

1. Open Antigravity with `gameon` repo loaded
2. Olga reads `CLAUDE.md` automatically on startup
3. She bootstraps: reads `OLGA.md` + `AGENT_RULES.md` from `Mjosuex85/orion`
4. She posts her **Read Log**
5. Mario gives the issue number

### How to start Nestor (GameOn backend, VSCode + Copilot)

1. Open VSCode with `gameon-api` repo loaded
2. Nestor reads `CLAUDE.md` automatically on startup
3. He bootstraps: reads `NESTOR.md` + `AGENT_RULES.md` from `Mjosuex85/orion`
4. He posts his **Read Log**
5. Mario gives the issue number

### Agent–repo mapping

| Agent | Repo | Identity file |
|-------|------|---------------|
| Olga-React | portfolioMV | `OLGA-REACT.md` |
| Olga-Angular | gameon | `OLGA.md` |
| Nestor | gameon-api | `NESTOR.md` |

### What pollutes context — never do this

```
❌ Give the issue before the agent confirms the Read Log
❌ Open agent in the wrong repo (gameon loaded, portfolioMV issue)
❌ Add extra instructions at startup — CLAUDE.md is enough
❌ Long sessions without closing — context degrades after ~3h
❌ Same agent session for two different projects
```

### One repo, one agent, one session

Each agent works in exactly one repo per session.
If you need to work on two projects → two separate agent sessions.

---

## COMMAND REFERENCE

| Mario says | Who | What happens |
|---|---|---|
| `"Orion iniciar proyecto"` | Orion | Wizard → create repo + 4 project files + issue #1 |
| `"despierta Orion, vamos con <X>"` | Orion | MODO PROYECTO → bootstrap context + skills for X |
| `"despierta Orion"` / `"necesito hablar contigo"` | Orion | MODO CTO → identity + decisions only, no project |
| `"cambiemos a <X>"` | Orion | Context switch → see context-switch.md |
| `"Orion cierra sesión"` | Orion | Session close → update docs + log |
| Open Antigravity (portfolioMV) | Olga-React | Auto-bootstrap via CLAUDE.md → OLGA-REACT.md |
| Open Antigravity (gameon) | Olga-Angular | Auto-bootstrap via CLAUDE.md → OLGA.md |
| Open VSCode (gameon-api) | Nestor | Auto-bootstrap via CLAUDE.md → NESTOR.md |

---

*Part of Orion OS v2.1.0 — session commands*
*Updated: May 7, 2026 — Session 36*
*Changes: bootstrap modes (MODO CTO / MODO PROYECTO), modular project paths, no default project*
