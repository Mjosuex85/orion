# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH

---

## v2.0.0 — Public System Layer (May 1, 2026)

**Objetivo:** Extraer la capa de sistema (agents, skills, workflows, templates, decisiones universales) a un repositorio público independiente. Validar que la separación system/instance es real y soporta el camino hacia v3.0.0.

### Cambios estructurales

**Nuevo repo: `Mjosuex85/orion-os` (público)**
- Repo nuevo from scratch — sin historia compartida con esta instancia privada.
- Razón: el repo privado tiene historia mezclada con proyectos personales y sesiones de aprendizaje. El repo público es un producto, no un diario.
- Genericización híbrida: código del sistema genérico ("the founder", "the project"), `/examples` para proyectos reales anonimizados.
- Sync mechanism con esta instancia: **diferido**. Las primeras 2-3 migraciones son cherry-pick manual, a propósito. La data nos dirá qué automatizar.

**Esta instancia (`Mjosuex85/orion`, privada)**
- Sigue siendo la instancia primaria de Mario.
- Conserva projects/, logs/, metrics/, ORION-OS-HACKATON.txt y todo el contenido instance-specific.
- DECISIONS.md privado mantiene la historia completa con D94, D95.

### Esqueleto público inicial

El repo público nace con lo mínimo, no con un dump:
- README.md — presentación pública del proyecto
- LICENSE — MIT, copyright Mario Vidal
- ROADMAP.md — visión hacia v3.0.0 (genericizada)
- DECISIONS.md — D1–D93 universales, sin instance-specific
- MIGRATION.md — cómo nació v2.0.0 y cómo se migra el resto
- /agents, /skills, /workflows, /templates, /examples — cada uno con README placeholder describiendo qué contendrá

### Decisiones nuevas
- **D94** — orion-os público creado, repo nuevo from scratch, sync mechanism diferido.
- **D95** — Genericización híbrida: system code genérico, /examples con proyectos reales anonimizados.
- **D68 ampliado** — Comentarios documentados como limitación actual del MCP, no como decisión permanente. Cuando GitHub arregle la lectura de comentarios de issues, el flujo natural será body + comments con comments autoritativos.

### Re-shuffle del roadmap previo

El viejo "v2.0.0 — Semi-Autonomous Orchestration" (Orion API, webhooks, Slack, Bruno v2) **ya no es v2.0.0**. Ese conjunto de features:
- Algunas pasan a v2.x como migraciones de la capa system al repo público.
- Otras se replantean cuando v3.0.0 (web interface) sustituya el flujo Claude+MCP por OAuth+API+DB.
- Bruno v2 sigue siendo deseable pero no bloquea v3.0.0.

### Migration impact
- Sesiones de Mario: ningún cambio. La instancia privada sigue siendo el lugar donde trabaja.
- Lecturas de Orion al arrancar sesión: ningún cambio (ORION-OS-ROADMAP.md, DECISIONS.md, ORION.md privados).
- Cuando un workflow o skill madure y se demuestre genérico, se migra a `orion-os` público vía cherry-pick.

---

## v1.5.0 — Smart Session Init + Multi-Framework (April 15, 2026)

**Objetivo:** Soportar múltiples proyectos con stacks distintos. Cargar skills correctas automáticamente. NutriApp (React) coexiste con GameOn (Angular) sin contaminación.

### Cambios
- Smart session init: Orion carga el contexto y skills correctas según el proyecto.
- Comandos de sesión: "iniciar proyecto", "vamos con X", "cierra sesión".
- Soporte monorepo + polyrepo en project-init.
- skills/frontend/react-patterns.md y angular-patterns.md.
- Skills son project-scoped: nunca se mezclan entre proyectos.
- D89 (reversible by default), D90 (scaffold via MCP), D91 (HOW-TO-USE living doc).
- D92 (SonarCloud quality strategy en 3 capas).
- D93 (security incident response, primer incidente: Vercel CDN breach).

---

## v1.4.0 — Multi-Project Ready (April 10, 2026)

**Objetivo:** Arrancar un segundo proyecto en < 30 minutos con calidad desde el día 1. Cambiar entre proyectos sin perder contexto.

### Cambios

**`workflows/project-init.md` — NEW**
- 5-phase step-by-step: prerequisites → Orion OS files → repo config → agent config → validation
- Orion handles phases 1, 2, and 4. Mario handles 3 and 5.
- Includes complete first issue template for backend setup.
- Target: project operational in 15-30 minutes (Orion OS side).

**`workflows/context-switch.md` — NEW**
- 3-step protocol: save current state → load new context → confirm switch.
- Mini-close (not full session close) when switching away.
- D7 applies per project (max 2 in-progress each).
- Agent re-bootstrap required on project switch.

**`templates/project-context.md`, `templates/project-decisions.md`, `templates/ci-backend.yml`, `templates/ci-frontend.yml`, `templates/bruno.yml` — NEW**
- Plantillas reutilizables para arrancar proyectos con CI/QA desde el día 1.

**`templates/new-project.md` — REWRITTEN**
- Now references all templates and workflows.
- 7-step checklist: define → repos → Orion OS files → repo config → platform → first issues → validation.

**ORION.md — UPDATED**
- New sections: HOW I START A NEW PROJECT, HOW I SWITCH PROJECTS.
- Active projects list with primary project concept.

**DECISIONS.md — UPDATED**
- D88: multi-project rules (8 sub-rules).
- D7 clarified: per project, not global.
- D40 marked as active since v1.4.0.

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

### v2.x — System Layer Maturation
- Migrar agents, skills, workflows, templates de la instancia privada al repo público, archivo a archivo, generalizando.
- /examples poblado con proyectos reales anonimizados.
- Sync mechanism privado ↔ público definido y probado.
- Bruno v2 (Claude API para failure analysis) cuando aporte valor real.

### v3.0.0 — Orion OS Web Interface
- OAuth GitHub + wizard de creación de proyecto.
- Sustitución de Claude+MCP+markdown por API + DB-backed memory.
- Cualquiera usa Orion OS sin conocimiento técnico.

---

*Orion OS — Built by Mario Vidal + Orion*
*Last updated: May 1, 2026 — Session 33*
