# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH

---

## v1.4.0 — Multi-Project Ready (April 10, 2026)

**Objetivo:** Arrancar un segundo proyecto en < 30 minutos con calidad desde el día 1. Cambiar entre proyectos sin perder contexto.

### Cambios

**`workflows/project-init.md` — NEW — Project initialization guide**
- 5-phase step-by-step: prerequisites → Orion OS files → repo config → agent config → validation
- Orion handles phases 1, 2, and 4. Mario handles 3 and 5.
- Includes complete first issue template for backend setup
- Target: project operational in 15-30 minutes (Orion OS side)

**`workflows/context-switch.md` — NEW — Project switching protocol**
- 3-step protocol: save current state → load new context → confirm switch
- Mini-close (not full session close) when switching away
- Rules: contexts never mix, issues stay in their repo, decisions go to right file
- D7 applies per project (max 2 in-progress each)
- Agent re-bootstrap required on project switch

**`templates/project-context.md` — NEW — Template for projects/<n>.md**
- Complete structure: pitch, stack, modules, business rules, infra, CI, testing, status
- Copy and fill — ensures consistency across all projects

**`templates/project-decisions.md` — NEW — Template for projects/<n>-decisions.md**
- Sections: backend arch, frontend arch, infrastructure, testing, operational rules
- Every new project gets its own decisions file from day 1

**`templates/ci-backend.yml` — NEW — CI template for NestJS backends**
- Lint → Build → Tests with coverage → Security audit → SonarCloud
- Copy to `.github/workflows/ci.yml` and adapt

**`templates/ci-frontend.yml` — NEW — CI template for Angular frontends**
- Lint → Build → Tests with coverage → SonarCloud
- Copy to `.github/workflows/ci.yml` and adapt

**`templates/bruno.yml` — NEW — Bruno QA template**
- Test runner + PR comment (✅/❌) + auto-issue on failure
- Notes for cross-repo configuration (frontend → backend issues)
- Copy to `.github/workflows/bruno.yml` in both repos

**`templates/new-project.md` — REWRITTEN**
- Now references all templates and workflows
- 7-step checklist: define → repos → Orion OS files → repo config → platform → first issues → validation
- Clear separation: what Orion does vs what Mario does

**ORION.md — UPDATED**
- New section: HOW I START A NEW PROJECT
- New section: HOW I SWITCH PROJECTS
- Active projects list with primary project concept
- Session start supports multi-project (load primary or specified)
- Compressed session log (grouped by period)
- Version bumped to v1.4.0

**DECISIONS.md — UPDATED**
- D88: multi-project rules (8 sub-rules covering isolation, context switch, agent re-bootstrap)
- D7 clarified: per project, not global
- D40 marked as active since v1.4.0
- Compressed format for established decisions

### Migration impact
- New projects: follow `workflows/project-init.md` + use all new templates
- Existing projects (GameOn): no changes needed — already conforms to structure
- Multi-project sessions: use `workflows/context-switch.md` when Mario asks to switch
- DECISIONS.md: D88 now governs all multi-project behavior

---

## v1.3.0 — Agent Intelligence (April 10, 2026)

Issue quality scoring, skill/subagent injection, post-mortem protocol, agent feedback loop.

---

## v1.2.0 — Metrics & Observability (April 10, 2026)

System dashboard, structured session logs, health checks, improved commit tracking.

---

## v1.1.0 — Separation of Concerns (April 10, 2026)

ORION.md refactored, DECISIONS split, project-specific decisions, templates updated.

---

## v1.0.0 — Foundation (March 26 – April 9, 2026)

Director + CTO + 2 agents + QA + 1 project in production.

---

## ROADMAP

### v2.0.0 — Semi-Autonomous Orchestration
- Orion API (issue creation, project status via Claude API)
- Automated issue assignment based on agent availability
- GitHub webhook integration (PR/merge/failure notifications)
- Slack/Discord notifications for the Director
- Bruno v2: Claude API for failure analysis and fix suggestions

---

*Orion OS — Built by Mario Vidal + Orion*
*Last updated: April 10, 2026 — Session 21*
