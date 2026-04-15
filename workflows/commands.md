# Orion OS — Session Commands

> These are the formal commands Mario uses to interact with Orion.
> Each command has a trigger, a step-by-step protocol, and an expected output.
> Orion recognizes these triggers in any chat where GitHub MCP is active.

---

## COMMAND 1 — Start new project

**Trigger:**
> "Orion iniciar proyecto" / "Orion new project" / "Orion quiero crear un proyecto nuevo"

**Protocol:**

Orion runs an interactive wizard — one question at a time, waits for Mario's answer before continuing:

```
Q1: ¿Nombre del proyecto?
Q2: ¿Topología?
      A) Monorepo — frontend + backend en un solo repo
      B) Polyrepo — repos separados (como GameOn)
Q3: ¿Framework frontend?
      A) React + Vite
      B) Angular
      C) Vue
      D) Svelte
      E) Ninguno (solo backend)
Q4: ¿Backend?
      A) NestJS
      B) Express
      C) Ninguno — SDK directo desde frontend
Q5: ¿Base de datos?
      A) Supabase (PostgreSQL)
      B) Firebase Firestore
      C) PostgreSQL propio
      D) SQLite
      E) Ninguna
Q6: ¿Auth?
      A) Supabase Auth
      B) Firebase Auth
      C) JWT propio
      D) Ninguna (Phase 1)
Q7: ¿Deploy?
      A) Vercel
      B) Railway
      C) Firebase Hosting
      D) Otro
Q8: ¿Crear repo(s) en GitHub ahora?
      A) Sí
      B) No — ya los crearé manualmente
```

Once all answers collected, Orion shows a summary and asks for confirmation:
> "Resumen: [name] — [topology] — [stack]. ¿Confirmas?"

On confirmation, Orion executes **in this exact order**:

```
1. Create repo(s) via GitHub MCP (if Q8 = yes)
2. Create develop branch
3. Create projects/<name>.md in orion repo
4. Create projects/<name>-decisions.md in orion repo
5. Push CLAUDE.md + .npmrc + .gitattributes + .env.example to develop
6. Update ORION.md — add project to REPOS + active projects
7. Create issue #1 — project scaffold
8. Confirm: "Proyecto [name] inicializado. Issue #1 abierto. ¿Arrancamos?"
```

**Rules:**
- Never skip a question — every answer goes into decisions
- Never assume a technology not explicitly chosen
- If Mario is unsure about a question → Orion gives a recommendation with reasoning, Mario decides
- Issue #1 is always the scaffold — never business logic

---

## COMMAND 2 — Work on a project

**Trigger:**
> "Orion vamos con [project]" / "despierta Orion, vamos con [project]" / "hola Orion, trabajamos en [project]"

**Protocol:**

```
1. Read ORION.md           → Mjosuex85/orion (main)
2. Read DECISIONS.md       → Mjosuex85/orion (main)
3. Read projects/<project>.md → identify stack + skills array
4. Load each skill in the skills array
5. Read projects/<project>-decisions.md
6. Run health check (workflows/health-check.md)
7. Status summary:
   - Current version / last deploy
   - Open issues (In Progress + Ready)
   - Next priority
8. "Listo. ¿Por dónde arrancamos?"
```

**If no project is specified:**
Load the primary project (GameOn) by default.

**If switching mid-session:**
Run mini-close on current project first → then load new project.
Full protocol: `workflows/context-switch.md`

**Rules:**
- Skills are project-scoped — never carry Angular skills into a React project
- Never mix decisions from different projects
- Health check is mandatory — never skip it

---

## COMMAND 3 — Close session

**Trigger:**
> "Orion cierra sesión" / "Orion cerramos" / "Orion wrap up" / "hasta luego Orion"

**Protocol:**

```
1. Show session summary:
   - Issues worked on
   - Decisions made
   - Problems found / solved
   - What was NOT finished

2. Update projects/<project>.md
   - STATUS section → reflect current state
   - Active priorities → update
   - Show diff to Mario before pushing

3. Append entry to logs/sessions.jsonl:
   {
     "date": "YYYY-MM-DD",
     "project": "<name>",
     "issues_closed": [],
     "issues_opened": [],
     "decisions": [],
     "next_priority": ""
   }

4. Update ORION.md session log

5. If new decisions made → add to correct DECISIONS file

6. Confirm:
   "Sesión cerrada. Próxima prioridad: [X]. Hasta luego."
```

**Rules:**
- Always show diff before pushing project.md (D87)
- Never close without updating next priority
- If session was short with no significant work → still log it, just mark as "brief session"

---

## QUICK REFERENCE

| Mario says | Orion does |
|------------|------------|
| "Orion iniciar proyecto" | Wizard → create repo + files + issue #1 |
| "Orion vamos con [project]" | Bootstrap → load context + skills + status |
| "Orion cierra sesión" | Summary → update docs → log → confirm next priority |
| "Orion switch a [project]" | Mini-close current → bootstrap new |
| "Orion qué tenemos pendiente" | List open issues + priorities for active project |

---

*Part of Orion OS v1.5.0 — session commands*
*Last updated: April 15, 2026*
