# Context Switch Protocol — Orion OS

> How Orion switches between projects — at session start or mid-session.
> Includes smart skill loading based on project stack.

---

## SESSION START WITH PROJECT

When Mario says:
- `"despierta Orion, vamos con [project]"`
- `"hola Orion, trabajamos en [project]"`
- `"switch to [project]"`

Orion runs the **smart session init**:

```
1. Read ORION.md         → Mjosuex85/orion (main)
2. Read DECISIONS.md     → Mjosuex85/orion (main)
3. Read projects/<project>.md → load stack + skills array
4. Load each skill declared in the project's skills array
5. Read projects/<project>-decisions.md
6. Run health check (workflows/health-check.md)
7. Status summary: project state + active priorities
8. "Listo. ¿Por dónde arrancamos?"
```

### Skills array in project.md

Each project declares which skills to load:
```yaml
skills:
  - skills/universal/git-flow.md
  - skills/frontend/react-patterns.md      # React project
  - skills/backend/nestjs-patterns.md      # if has backend
```

Orion loads exactly those skills — no more, no less.
Angular project → loads angular-patterns. React project → loads react-patterns. No confusion.

---

## MID-SESSION SWITCH

When Mario says:
- `"cambiemos a [project]"`
- `"ahora trabajemos en [project]"`

### Step 1 — Save current project state
```
1. Update projects/<current-project>.md (STATUS + priorities)
2. Append partial entry to logs/sessions.jsonl
3. Note open threads
```

### Step 2 — Load new project
```
1. Read projects/<new-project>.md
2. Load skills declared in new project's skills array
3. Read projects/<new-project>-decisions.md
4. Health check for new project repos
5. "Switched to [project]. [Status]. ¿Por dónde arrancamos?"
```

---

## RULES

1. **Skills are project-scoped.** Never carry skills from one project to another.
2. **Never mix contexts.** Decisions from Project A never apply to Project B unless they're in universal DECISIONS.md.
3. **Issues stay in their repo.** Never create an issue for Project A in Project B's repo.
4. **Decisions go to the right file.** Universal → `DECISIONS.md`. Project-specific → `projects/<project>-decisions.md`.
5. **Max 2 In Progress applies per project**, not globally.
6. **Agents re-bootstrap when switching projects.** Mario gives them the new CLAUDE.md explicitly.

---

## SWITCHING BACK

```
1. Save current project (mini-close)
2. Re-read previous project.md (updated in Step 1, so it's fresh)
3. Re-load that project's skills
4. "Back to [project]. We were discussing [X]. ¿Seguimos?"
```

---

*Part of Orion OS v1.5.0 — smart session init with skill loading*
