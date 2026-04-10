# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH

---

## v1.2.0 — Metrics & Observability (April 10, 2026)

**Objetivo:** Saber cómo rinde el sistema con datos reales, no con intuición. Detectar problemas al inicio de cada sesión.

### Cambios

**`metrics/METRICS.md` — NEW — System dashboard**
- System overview: version, projects, sessions, agents, RFCs
- Per-project: production status, CI, issues, test coverage
- Session velocity: issues closed per week, decisions per period
- Agent performance: last tasks, pending, compliance
- Health check results: recorded every session
- Tech debt tracker: prioritized list with origin date
- Trends & observations: what's working, what needs attention

**`logs/sessions.jsonl` — NEW — Structured session log**
- Each session = one JSON line
- Fields: session number, date, project, issues closed/created, decisions, summary, OS version
- Machine-parseable for future analysis and automation
- Replaces the free-text session log as the data source (ORION.md keeps human-readable summary)

**`workflows/health-check.md` — NEW — Session start protocol**
- 6 checks: CI status, stale PRs, unassigned issues, branch sync, Bruno status, in-progress limit
- Runs before status summary at every session start
- Results recorded in METRICS.md
- Severity levels: green → continue, warning → mention, critical → fix first

**`workflows/commit-log.yml` — IMPROVED**
- Now captures: commit type, scope, branch (not just agent/issue/size)
- Triggers on `staging` branch too (not just develop/main)
- Uses `jq` for proper JSON construction (avoids escaping issues)
- Recognizes `[BRUNO]` and `[ORION]` commits

**`templates/session-close.md` — NEW — Session close checklist**
- Step-by-step: project.md → sessions.jsonl → ORION.md → decisions → METRICS.md → confirm
- Compression rules for short sessions
- Never skip sessions.jsonl

**ORION.md updated**
- Session start protocol now includes health check (step 4)
- Session close protocol references `templates/session-close.md`
- Session log entries compressed to one line each (details in sessions.jsonl)
- Version bumped to v1.2.0

### Migration impact
- Session start: now includes health check before status summary
- Session close: now includes sessions.jsonl entry + METRICS.md update
- Existing session history migrated to sessions.jsonl (sessions 12-21)
- commit-log.yml must be copied to project repos' `.github/workflows/` to capture agent commits

---

## v1.1.0 — Separation of Concerns (April 10, 2026)

**Objetivo:** Separar Orion OS (framework universal) de GameOn (proyecto específico). Listo para multi-proyecto.

### Cambios
- ORION.md: ~17KB → ~5KB (identity + universal rules only)
- DECISIONS.md: split into universal + `projects/gameon-decisions.md`
- projects/gameon.md: absorbed all GameOn state from ORION.md
- Templates: updated to CLAUDE.md flow + new `claude-md.md` template

---

## v1.0.0 — Foundation (March 26 – April 9, 2026)

**El sistema funciona.** Director + CTO + 2 execution agents + QA + 1 project in production.

### Lo que se construyó
- Memory system (ORION.md + DECISIONS.md + project.md)
- Agent definitions (Nestor, Olga, Bruno, 7 subagents)
- Templates (new-project, issue, architecture)
- Skills library (universal, backend, frontend)
- GitFlow + CI/CD + SonarCloud + Bruno QA
- RFC flow for structured decision-making

---

## ROADMAP

### v1.3.0 — Agent Intelligence
- Skill injection automática en issues
- Issue quality score antes de asignar
- Post-mortem automático en failures
- Agent feedback loop

### v1.4.0 — Multi-Project Ready
- CLI de scaffolding (`orion init <project>`)
- Context switching entre proyectos en una sesión
- Shared vs project decisions propagation

### v2.0.0 — Semi-Autonomous Orchestration
- Orion API (issue creation, project status)
- Automated issue assignment
- GitHub webhook integration
- Slack/Discord notifications

---

## VERSIONING RULES

- Cada mejora se documenta aquí con fecha y descripción
- Las versiones se tagean en el repo: `git tag vX.X.X`
- README.md muestra la versión actual
- ORION.md referencia la versión en su header

---

*Orion OS — Built by Mario Vidal + Orion*
*Last updated: April 10, 2026 — Session 21*
