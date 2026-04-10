# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH
> - MAJOR: cambio fundamental en cómo opera el sistema
> - MINOR: nueva capacidad significativa
> - PATCH: mejoras, fixes, documentación

---

## v1.1.0 — Separation of Concerns (April 10, 2026)

**Objetivo:** Separar lo que es Orion OS (framework universal) de lo que es GameOn (proyecto específico). Listo para multi-proyecto.

### Cambios

**ORION.md refactored (~17KB → ~5KB)**
- Stripped to: identity, universal rules, session protocol, agent bootstrap, session log
- Removed: all GameOn-specific state, infrastructure, checklists, testing plans, Atomic Design rules
- Added: version header, dynamic project loading in session start protocol
- Session close protocol now writes to correct decisions file (universal or project)

**DECISIONS.md split**
- `DECISIONS.md` — universal Orion OS decisions only (D1-D14, D35-D40, D43, D49-D56, D60-D68, D73-D74, D77-D78, D81-D82, D87)
- `projects/gameon-decisions.md` — NEW — all GameOn-specific decisions (D16-D33, D44-D48, D57-D59, D62, D67, D69-D72, D75-D76, D79-D80, D83-D86)
- Removed obsolete entries: D50 (superseded by D74), D39 (pending MCPs from early sessions)

**projects/gameon.md updated**
- Absorbed: infrastructure details, staging checklist, testing status, Atomic Design system, Bruno config, SonarCloud config
- Added: reference to `gameon-decisions.md`
- Cleaner structure: stack → modules → business rules → infra → CI → testing → status

**Templates updated**
- `templates/new-project.md` — reflects CLAUDE.md bootstrap flow, CI/CD setup, `.gitattributes`, project-specific decisions file
- `templates/claude-md.md` — NEW — standard CLAUDE.md template for any project repo

### Migration impact
- Orion session start: no change (still reads ORION.md → DECISIONS.md → project.md)
- Orion also reads `projects/gameon-decisions.md` when working on GameOn
- Agent bootstrap: no change (CLAUDE.md → agent.md → AGENT_RULES.md)
- New projects: use updated `new-project.md` template + `claude-md.md` template

---

## v1.0.0 — Foundation (March 26 – April 9, 2026)

**El sistema funciona.** Un director, un CTO (Orion), dos agentes de ejecución (Nestor, Olga), un QA automatizado (Bruno), y un proyecto real en producción (GameOn v1.3.0).

### Lo que se construyó

**Core — Memoria y contexto**
- `ORION.md` — cerebro de Orion
- `DECISIONS.md` — registro de decisiones técnicas (87 decisiones)
- `projects/gameon.md` — contexto del proyecto activo
- Protocolos de inicio/cierre de sesión

**Agentes**
- `agents/NESTOR.md`, `agents/OLGA.md` — system prompts
- `agents/BRUNO.md` — QA agent (CI-based, Phase 1)
- `agents/DIRECTOR.md` — founder profile
- `agents/AGENT_RULES.md` — universal rules
- `agents/subagents/` — 7 specialized subagents

**Infraestructura**
- `templates/` — new-project, issue-template, architecture
- `skills/` — universal, backend, frontend, projects
- `workflows/commit-log.yml` — activity metrics
- `rfcs/` — RFC flow (D87)

**Proceso probado**
- GitFlow: develop → staging → main
- CI/CD: lint, build, test, audit, SonarCloud
- Bruno QA: automatic tests on every PR
- Issue management: single repo, Orion closes, agents execute
- RFC flow for decisions requiring analysis

---

## ROADMAP

### v1.2.0 — Metrics & Observability
- Dashboard de métricas (issues/week, resolution time, reopen ratio)
- Session log estructurado (`logs/sessions.jsonl`)
- Health check de repos al inicio de sesión

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
- Las versiones se tagean en el repo: `git tag v1.1.0`
- El README.md muestra la versión actual
- ORION.md referencia la versión en su header

---

*Orion OS — Built by Mario Vidal + Orion*
*Last updated: April 10, 2026 — Session 21*
