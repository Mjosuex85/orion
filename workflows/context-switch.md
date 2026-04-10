# Context Switch Protocol — Orion OS

> How Orion switches between projects in a single session.
> Designed for when Mario wants to work on multiple projects in one sitting.

---

## WHEN TO SWITCH

Mario says something like:
- "Cambiemos a [project]"
- "Ahora trabajemos en [project]"
- "Switch to [project]"

---

## THE PROTOCOL

### Step 1 — Save current project state

Before switching away from the current project:

```
1. Update projects/<current-project>.md with current status
2. Append partial session entry to logs/sessions.jsonl (if significant work was done)
3. Note any open threads: "We were discussing X, pending Y"
```

This is a mini-close, not a full session close. No METRICS.md update needed.

### Step 2 — Load new project context

```
1. Read projects/<new-project>.md from Mjosuex85/orion (main)
2. Read projects/<new-project>-decisions.md if it exists
3. Run health check for the new project's repos
4. Brief status summary of the new project
```

### Step 3 — Confirm switch

```
"Switched to [project]. [Status summary]. ¿Por dónde arrancamos?"
```

---

## RULES

1. **Never mix contexts.** After switching, Orion's decisions and recommendations are based on the active project's decisions file, not the previous one.

2. **Issues stay in their repo.** Never create an issue for Project A in Project B's repo.

3. **Decisions go to the right file.** Universal decisions → `DECISIONS.md`. Project-specific → `projects/<project>-decisions.md`.

4. **Session log is unified.** One `sessions.jsonl` entry per session, but the `project` field can note multiple projects: `"project": "gameon + project-b"`.

5. **Health check is per-project.** When switching, check the new project's repos, not the old one's.

6. **Max 2 In Progress applies per project, not globally.** Each project can have up to 2 issues in progress.

---

## SWITCHING BACK

If Mario says "volvamos a [previous project]":

```
1. Save current project state (mini-close)
2. Re-read previous project.md (it was updated in Step 1, so it's fresh)
3. Resume from where we left off
4. "Back to [project]. We were discussing [X]. ¿Seguimos con eso?"
```

---

## EDGE CASES

### Cross-project decision
If a decision affects multiple projects (e.g., changing a universal rule):
- Add to `DECISIONS.md` (universal)
- Note in both project files that the decision exists
- Each project's agents will pick it up via AGENT_RULES.md on next bootstrap

### Agent working on multiple projects
Nestor and Olga can work on different projects in the same day, but:
- They must re-bootstrap via CLAUDE.md each time they switch projects
- Mario gives them the new CLAUDE.md explicitly
- Issues from different projects must never be mixed in a single commit

---

*Part of Orion OS v1.4.0 — seamless multi-project management*
