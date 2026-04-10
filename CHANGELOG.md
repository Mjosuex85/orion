# CHANGELOG — Orion OS

> Versioning del sistema operativo. Cada versión marca un hito real en las capacidades del framework.
> Las versiones siguen semver: MAJOR.MINOR.PATCH
> - MAJOR: cambio fundamental en cómo opera el sistema
> - MINOR: nueva capacidad significativa
> - PATCH: mejoras, fixes, documentación

---

## v1.0.0 — Foundation (Marzo 26 – Abril 9, 2026)

**El sistema funciona.** Un director, un CTO (Orion), dos agentes de ejecución (Nestor, Olga), un QA automatizado (Bruno), y un proyecto real en producción (GameOn v1.3.0).

### Lo que existe hoy

**Core — Memoria y contexto**
- `ORION.md` — cerebro de Orion (identidad, reglas, sesiones, infra)
- `DECISIONS.md` — registro de decisiones técnicas (87 decisiones documentadas)
- `projects/gameon.md` — contexto del proyecto activo
- `projects/gameon-ideas.md` — backlog de ideas de producto
- `projects/gameon-architecture.md` — arquitectura técnica
- Protocolo de inicio de sesión (leer 3 archivos en orden)
- Protocolo de cierre de sesión (actualizar estado sin preguntar)

**Agentes**
- `agents/NESTOR.md` — system prompt backend tech lead
- `agents/OLGA.md` — system prompt frontend tech lead
- `agents/BRUNO.md` — QA agent (CI-based, Phase 1)
- `agents/DIRECTOR.md` — perfil del fundador
- `agents/AGENT_RULES.md` — reglas universales para todos los agentes
- `agents/subagents/` — 7 subagentes especializados (angular, nestjs, security, etc.)

**Infraestructura del framework**
- `templates/new-project.md` — checklist para arrancar un proyecto nuevo
- `templates/issue-template.md` — formato estándar de issues
- `templates/architecture.md` — template de documentación de arquitectura
- `skills/` — skills reutilizables (universal, backend, frontend, projects)
- `workflows/commit-log.yml` — GitHub Action para métricas de actividad
- `rfcs/` — Request for Comments (flujo D87)

**Proceso probado**
- GitFlow: develop → staging → main
- CI/CD: lint, build, test, audit, SonarCloud (ambos repos)
- Bruno QA: tests automáticos en cada PR
- Issue management: todo en gameon-api, Orion cierra, agentes ejecutan
- Protocolo de corrección: update issue body, nunca comments (D68)
- RFC flow para decisiones que necesitan análisis (D87)

---

## ANÁLISIS — Estado actual y áreas de mejora

### ✅ Lo que funciona bien

1. **Memoria estructurada** — ORION.md + DECISIONS.md + gameon.md dan contexto suficiente para arrancar cualquier sesión sin perder estado.
2. **Separación de roles clara** — Director decide, Orion coordina, agentes ejecutan. No hay confusión.
3. **Issue-driven development** — GitHub Issues como canal único de comunicación con agentes. Funciona.
4. **Quality gates** — CI + Bruno + SonarCloud atrapan errores antes de producción.
5. **Protocolo de sesión** — Inicio y cierre estandarizados. El estado se persiste.

### ⚠️ Problemas detectados

1. **ORION.md está sobrecargado** (~17KB)
   - Mezcla identidad, reglas, infra, sesiones, checklists, y estado del proyecto
   - Cada sesión lo hace más grande → eventualmente será un cuello de botella de contexto
   - Las reglas que aplican a todos los proyectos están mezcladas con reglas GameOn-specific

2. **DECISIONS.md crece sin estructura de búsqueda**
   - 87 decisiones en un archivo plano
   - No hay forma de saber cuáles son universales vs project-specific
   - Algunas decisiones son obsoletas (D33 corregida, D50 superseded) pero siguen ahí

3. **No hay separación entre Orion OS core y GameOn-specific**
   - ORION.md tiene staging checklist de GameOn
   - DECISIONS.md mezcla reglas de TypeORM con principios universales
   - Un segundo proyecto heredaría ruido irrelevante

4. **Skills infrautilizados**
   - La carpeta `skills/` existe pero no hay evidencia de que los agentes los consuman sistemáticamente
   - Los issues no siempre incluyen la sección "Skills to inject"
   - No hay un mecanismo para que el agente sepa qué skills son relevantes para su tarea

5. **Templates desactualizados**
   - `new-project.md` referencia `AGENTS.md` y `DECISIONS.md` en los repos de proyecto — pero el sistema real usa `CLAUDE.md`
   - No refleja el flujo actual (CLAUDE.md → agent.md → AGENT_RULES.md)

6. **No hay métricas del sistema**
   - No sabemos: cuántas sesiones por semana, cuántos issues resueltos, ratio de reopen, tiempo promedio de issue
   - `commit-log.yml` existe pero no se procesa

7. **Onboarding de un proyecto nuevo es manual y largo**
   - El template dice "under 1 hour" pero la realidad con GameOn tomó semanas de iteración
   - No hay un script o automatización — todo es copiar-pegar y adaptar manualmente

8. **Subagentes sin protocolo de activación**
   - Los 7 subagentes existen como archivos .md pero no hay un mecanismo documentado para cuándo y cómo se activan
   - ¿Los inyecta Orion en el issue? ¿Los lee el agente por cuenta propia?

9. **Sin versionado formal**
   - No hay CHANGELOG, no hay tags, no hay forma de saber "qué versión de Orion OS estoy usando"
   - Si alguien (o un futuro Orion) forkea el repo, no tiene contexto de madurez

10. **Dependencia total de un solo LLM provider**
    - Orion vive en Claude.ai Pro. Si la sesión se corta, no hay forma de continuar
    - No hay export/import de estado que funcione cross-provider

---

## ROADMAP — Orion OS

### v1.1.0 — Separation of Concerns (próximo)

**Objetivo:** Separar lo que es Orion OS (framework universal) de lo que es GameOn (proyecto específico). Que el día que arranques un segundo proyecto, no arrastres nada de GameOn.

**Cambios propuestos:**

1. **Refactor ORION.md → dividir en 3 archivos:**
   - `ORION.md` — solo identidad, principios, cómo arranco, cómo cierro (~3KB máx)
   - `ORION_STATE.md` — estado global del sistema (infra, agentes activos, sesión actual)
   - `projects/gameon.md` — absorbe todo lo GameOn-specific que hoy vive en ORION.md

2. **Refactor DECISIONS.md → dividir en 2:**
   - `DECISIONS.md` — solo decisiones universales de Orion OS (D1-D14, D52-D68, D73, D77-D78, D82, D87)
   - `projects/gameon-decisions.md` — decisiones específicas de GameOn (D16-D51, D69-D72, D79, D83-D86)

3. **Actualizar templates:**
   - `new-project.md` → reflejar el flujo real (CLAUDE.md → agent.md → AGENT_RULES.md)
   - Agregar: `templates/claude-md.md` — template base para CLAUDE.md de cualquier repo

4. **Protocolo de subagentes:**
   - Documentar en AGENT_RULES.md cuándo un agente debe leer un subagente
   - Orion incluye en el issue qué subagentes aplican (igual que skills)

### v1.2.0 — Metrics & Observability

**Objetivo:** Saber cómo rinde el sistema. Datos reales, no intuición.

1. **Dashboard de métricas (GitHub Action o script):**
   - Issues creados/cerrados por semana
   - Tiempo promedio de resolución por tamaño (S/M/L/XL)
   - Ratio de issues reabiertos
   - Commits por agente por semana
   - Cobertura de tests (trend)

2. **Session log estructurado:**
   - Migrar el session log de ORION.md a `logs/sessions.jsonl`
   - Cada entrada: fecha, duración estimada, issues trabajados, decisiones tomadas
   - Permite análisis automático después

3. **Health check de repos:**
   - Script que valida: CI green, coverage threshold, no PRs stale, no issues sin assignee
   - Se ejecuta al inicio de sesión y reporta anomalías

### v1.3.0 — Agent Intelligence

**Objetivo:** Agentes más autónomos dentro de los guardrails definidos.

1. **Skill injection automática:**
   - Orion analiza el issue y sugiere skills relevantes automáticamente
   - El agente los lee como parte de su protocolo de inicio de tarea

2. **Issue quality score:**
   - Antes de asignar un issue, Orion lo evalúa: ¿tiene contexto? ¿prompt claro? ¿test plan?
   - Si falta algo → lo completa antes de asignar

3. **Post-mortem automático:**
   - Cuando un issue se reabre o Bruno falla, Orion crea una entrada en `logs/incidents.md`
   - Patrón: qué falló, por qué, qué regla faltaba

4. **Agent feedback loop:**
   - Después de cada issue completado, Orion evalúa: ¿el agente siguió el protocolo? ¿el código requirió correcciones?
   - Resultado alimenta mejoras al system prompt del agente

### v1.4.0 — Multi-Project Ready

**Objetivo:** Arrancar un segundo proyecto en < 30 minutos con calidad desde el día 1.

1. **CLI de scaffolding:**
   - `orion init <project-name>` → genera repos, CLAUDE.md, project.md, issues iniciales
   - Configura GitHub Actions, branch protection, CI pipeline
   - Todo basado en los templates del repo orion

2. **Context switching:**
   - Orion puede cambiar entre proyectos en la misma sesión
   - `projects/<name>.md` se carga dinámicamente
   - Estado de cada proyecto es independiente

3. **Shared decisions vs project decisions:**
   - Cambios en DECISIONS.md universal se propagan como awareness a todos los proyectos
   - Cada proyecto puede override con justificación

### v2.0.0 — Semi-Autonomous Orchestration

**Objetivo:** Orion como API, no solo como chat.

1. **Orion API:**
   - Endpoint que recibe: "crea un issue para [descripción]" → genera issue formateado
   - Endpoint que recibe: "estado del proyecto" → retorna resumen estructurado
   - Base: Claude API + contexto de Orion OS

2. **Automated issue assignment:**
   - Orion detecta issues sin asignar → evalúa complejidad → asigna al agente correcto
   - Respeta el límite de 2 in-progress

3. **Integration hooks:**
   - GitHub webhook → Orion se entera de PRs, merges, failures
   - Slack/Discord notifications para el Director

---

## VERSIONING RULES

- Cada mejora a Orion OS se documenta aquí con fecha y descripción
- Las versiones se tagean en el repo: `git tag v1.1.0`
- El README.md muestra la versión actual
- ORION.md referencia la versión en su header

---

*Orion OS v1.0.0 — Built by Mario Vidal + Orion*
*Analysis date: April 10, 2026 — Session 21*
