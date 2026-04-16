# Orion OS — Session Commands

> These are the three core commands Mario uses to interact with Orion OS.
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
3. Create projects/<n>.md (from template, filled with chosen stack)
4. Create projects/<n>-decisions.md (initial decisions based on choices)
5. Push CLAUDE.md + .npmrc + .gitattributes + .env.example to develop
6. Update ORION.md — add project to REPOS + active projects list
7. Create issue #1 — project scaffold based on chosen stack
```

### Output

```
"Proyecto <n> inicializado bajo Orion OS.
Repo: Mjosuex85/<n>
Issue #1 abierto: <title>
Próximo paso: ejecuta el issue #1."
```

---

## COMMAND 2 — "Orion vamos con X" / "Orion despierta"

**Triggers:**
- `"despierta Orion, vamos con <project>"`
- `"hola Orion, trabajamos en <project>"`
- `"Orion vamos con <project>"`
- `"Orion despierta"` (loads primary project)

**What Orion does — smart session init:**

```
1. Read ORION.md          → Mjosuex85/orion (main)
2. Read DECISIONS.md      → Mjosuex85/orion (main)
3. Read projects/<project>.md → identify stack + skills array
4. Load each skill in the skills array
5. Read projects/<project>-decisions.md
6. Run health check (workflows/health-check.md)
7. Status summary: current state + open issues + active priorities
8. "Listo. ¿Por dónde arrancamos?"
```

**Rules:**
- Skills are project-scoped — never load Angular skills for a React project
- If Mario doesn't specify a project → load primary project (GameOn)
- If switching mid-session → follow workflows/context-switch.md

---

## COMMAND 3 — "Orion cierra sesión"

**Triggers:**
- `"Orion cierra sesión"`
- `"Orion close session"`
- `"cerramos Orion"`

**What Orion does — session close protocol:**

```
1. Show diff for projects/<project>.md
   → What changed: issues closed, decisions made, priorities updated
   → Ask Mario: "¿Hago push?"

2. On Mario's green light, push in this exact order:
   a. projects/<project>.md          — status, features, pending (D87)
   b. projects/<project>-decisions.md — new technical decisions (ALWAYS, not optional)
   c. logs/sessions.jsonl            — append structured entry
   d. ORION.md SESSION LOG           — one-liner only (see format below)

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
NutriApp Phase 1 complete. Plan, cook flow, stock deduction, UI redesign. ND16-ND21.
```

Examples of WRONG entries (never do this):
```
### Session 24 — April 16, 2026
- Monthly Plan page: Hoy/Semana/Mes views, week navigation, month grid
- Auto-generate: Fisher-Yates shuffle
- Cook flow: MealActionSheet → ConfirmModal → applyRecipe
...
```

### projects/<project>-decisions.md — ALWAYS updated at close

All new technical decisions made during the session go here — no exceptions.
Format: `NDxx. <Decision title>` with context and reason.
This is not optional. If decisions were made, they must be documented.

### logs/sessions.jsonl — entry format

```json
{"session": N, "date": "YYYY-MM-DD", "project": "<project>", "issues_closed": ["#N"], "issues_created": [], "decisions": ["short description"], "summary": "one sentence", "orion_os_version": "x.x.x"}
```

**Rules:**
- Never close without updating projects/<project>.md (D87)
- Always show diff before pushing — Mario decides what goes in
- If session was short and nothing changed → say so, no unnecessary push
- ORION.md SESSION LOG = one-liner only. The detail lives in project files.

---

## COMMAND REFERENCE

| Mario says | Orion does |
|---|---|
| `"Orion iniciar proyecto"` | Wizard → create repo + files + issue #1 |
| `"despierta Orion, vamos con <X>"` | Smart bootstrap → load context + skills for X |
| `"Orion despierta"` | Smart bootstrap → load primary project |
| `"cambiemos a <X>"` | Context switch → see context-switch.md |
| `"Orion cierra sesión"` | Session close → update docs + log |

---

*Part of Orion OS v1.5.0 — session commands*
*Updated: April 16, 2026 — Session 25 — fix: session close protocol (bugs from session 24)*
