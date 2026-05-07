# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH

---

## v2.1.0 — Bootstrap Modes + Modular Structure (May 7, 2026)

**Objetivo:** Optimizar el bootstrap por tipo de sesión. Modularizar la estructura de proyectos. Crear la fuente de verdad del crecimiento del sistema.

### Cambios

**Bootstrap por modos:**
- MODO CTO: `"despierta Orion"` — carga identidad + decisiones + evolución. Sin proyecto, sin skills.
- MODO PROYECTO: `"despierta Orion, vamos con <X>"` — carga contexto completo del proyecto.
- Eliminado GameOn como proyecto por defecto. Orion pregunta si no se especifica.

**Estructura modular de proyectos:**
- `projects/<name>/` — carpeta por proyecto con 4 archivos estándar.
- `<name>.md`, `<name>-decisions.md`, `<name>-architecture.md`, `<name>-ideas.md`.
- Archivos planos en `projects/` raiz convertidos en redirects.

**Nuevos archivos:**
- `ORION-EVOLUTION.md` — fuente de verdad del crecimiento de Orion. Pendientes, patrones aprendidos, decisiones abiertas de sistema. Se lee en MODO CTO.
- `workflows/config.md` — convenciones de configuración universal (Kanban setup, vinculación de repos).

**INC-01 resuelto:**
- PortfolioMV y NutriApp boards vinculados a sus repos en GitHub Projects.
- Convención documentada en `workflows/config.md`.

**Archivos actualizados:**
- `ORION.md` — v2.1.0, bootstrap por modos, rutas modulares, ORION-EVOLUTION en cierre de sesión.
- `BOOT.md` — v2.1.0, dos modos, rutas correctas.
- `workflows/commands.md` — COMMAND 2 reescrito, COMMAND 1 con rutas modulares.
- `README.md` — estructura actualizada, v2.1.0.

---

## v2.0.0 — Public System Layer (May 1, 2026)

**Objetivo:** Extraer la capa de sistema a un repositorio público independiente.

### Cambios estructurales
- Nuevo repo: `Mjosuex85/orion-os` (público, MIT).
- Esta instancia (`Mjosuex85/orion`) sigue siendo la instancia primaria de Mario.
- D94, D95 añadidos.
- RFC-001 abierto: Instance Contract.

---

## v1.5.0 — Smart Session Init + Multi-Framework (April 15, 2026)

Soporte múltiples proyectos con stacks distintos. Skills project-scoped. NutriApp (React) coexiste con GameOn (Angular).

---

## v1.4.0 — Multi-Project Ready (April 10, 2026)

Project-init wizard, context-switch protocol, plantillas reutilizables.

---

## v1.3.0 — Agent Intelligence (April 10, 2026)

Issue quality scoring, skill injection, post-mortem protocol, agent feedback loop.

---

## v1.2.0 — Metrics & Observability (April 10, 2026)

System dashboard, structured session logs, health checks.

---

## v1.1.0 — Separation of Concerns (April 10, 2026)

ORION.md refactored, DECISIONS split, project-specific decisions.

---

## v1.0.0 — Foundation (March 26 – April 9, 2026)

Director + CTO + 2 agents + QA + 1 project in production.

---

## ROADMAP

### v2.x — System Layer Maturation
- `gameon-state.md` per project — estado denso y pequeño, separado del contexto histórico.
- Few-shot examples en `ORION-PATTERNS.md`.
- Migrar agents, skills, workflows al repo público progresivamente.
- Resolver RFC-001 (Instance Contract).

### v3.0.0 — Orion OS Web Interface
- OAuth GitHub + wizard de creación de proyecto.
- Sustitución de Claude+MCP+markdown por API + DB-backed memory.

---

*Orion OS — Built by Mario Vidal + Orion*
*Last updated: May 7, 2026 — Session 36*
