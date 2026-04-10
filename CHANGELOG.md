# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH

---

## v1.3.0 — Agent Intelligence (April 10, 2026)

**Objetivo:** Agentes más efectivos sin aumentar intervención manual. Calidad del issue garantizada antes de asignar. Aprendizaje continuo de cada error y cada issue completado.

### Cambios

**`workflows/issue-quality.md` — NEW — Issue quality score**
- 10-criterion checklist: context, prompt, test plan, agent, size, files, skills, subagents, commit, tests
- Scoring: ≥ 8 = assign, 5-7 = Orion completes first, <5 = rewrite
- Orion is the quality gate — Mario never thinks about issue format
- Includes examples of READY vs INCOMPLETE issues

**`workflows/skill-injection.md` — NEW — Skill & subagent injection map**
- Complete trigger map: which skills/subagents to inject for each issue type
- Injection rules by size: S = universal only, M = +skills, L = +subagents, XL = everything
- Agent reading protocol: how agents consume skills and use subagents as self-review
- Guide for creating new skills when patterns emerge

**`workflows/post-mortem.md` — NEW — Post-mortem protocol**
- 5 triggers: Bruno failure, issue reopened, bug in staging, testing failure, agent blocked
- Structured JSON log in `logs/post-mortems.jsonl`
- 6 categories: issue_quality, missing_test, wrong_assumption, missing_skill, protocol_violation, technical_gap
- Pattern detection: 3+ same category triggers systemic improvement
- Goal: same mistake never happens twice

**`workflows/agent-feedback.md` — NEW — Agent feedback loop**
- 5 criteria: protocol compliance, code quality, tests, first-attempt success, scope respect
- Structured JSON log in `logs/agent-feedback.jsonl`
- Overall ratings: excellent / good / needs_improvement / problematic
- Pattern detection: 2+ yellows in same criterion triggers system update
- Loop: issue → evaluate → log → detect pattern → update prompt/skill → next issue improves

**`templates/issue-template.md` — UPDATED**
- Added `## DO NOT touch` section (explicit scope boundaries)
- Added `## Subagents` section
- Added `## Unit tests` section (was implicit, now explicit)
- Added quality score checklist at the bottom
- References `workflows/issue-quality.md` and `workflows/skill-injection.md`

**`agents/AGENT_RULES.md` — UPDATED**
- Mandatory protocol now starts with reading skills and subagents (steps 1-2)
- Self-review with subagent criteria before "Ready to test" (step 4)
- New section: SKILLS AND SUBAGENTS — how to read and apply them
- MCP ALLOWED list now includes `skills/*.md` and `agents/subagents/*.md`
- Issue reading section updated to include skills and subagents sections

**ORION.md — UPDATED**
- New section: HOW I CREATE ISSUES (quality gate workflow)
- New section: HOW I EVALUATE COMPLETED ISSUES (feedback + post-mortem)
- New rules: issue quality gate, post-mortem on failure, feedback after completion
- Agent bootstrap chain updated: step 5 = read skills/subagents from issue
- Session log references all 3 log files

**New log files:**
- `logs/post-mortems.jsonl` — initialized (empty)
- `logs/agent-feedback.jsonl` — initialized (empty)

### Migration impact
- Issue creation: Orion now runs quality score before assigning
- Agent workflow: agents now read skills/subagents listed in the issue before implementing
- After completion: Orion evaluates and logs agent performance
- On failure: Orion creates post-mortem entry and applies prevention
- Existing skills and subagents are now actively used (they were dormant before)

---

## v1.2.0 — Metrics & Observability (April 10, 2026)

**Objetivo:** Saber cómo rinde el sistema con datos reales.

### Cambios
- `metrics/METRICS.md` — system dashboard
- `logs/sessions.jsonl` — structured session log
- `workflows/health-check.md` — session start health check
- `workflows/commit-log.yml` — improved (type, scope, branch, jq)
- `templates/session-close.md` — session close checklist

---

## v1.1.0 — Separation of Concerns (April 10, 2026)

**Objetivo:** Separar Orion OS de GameOn. Listo para multi-proyecto.

### Cambios
- ORION.md: ~17KB → ~5KB (identity + universal rules only)
- DECISIONS.md: split into universal + `projects/gameon-decisions.md`
- Templates: updated to CLAUDE.md flow + `claude-md.md` template

---

## v1.0.0 — Foundation (March 26 – April 9, 2026)

Director + CTO + 2 execution agents + QA + 1 project in production.

---

## ROADMAP

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

*Orion OS — Built by Mario Vidal + Orion*
*Last updated: April 10, 2026 — Session 21*
