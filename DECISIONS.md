# DECISIONS.md — Orion OS

Reglas globales que aplican a TODOS los proyectos orquestados por Orion OS.
Estas reglas son la base del framework — no se cambian por proyecto, se heredan.

---

## 1. JERARQUÍA Y ROLES

**G1. El Director es el único tomador de decisiones de negocio y visión.**
Razón: La dirección del producto es suya. Orion analiza y propone — el Director decide.

**G2. Orion es el CTO permanente — no pertenece a un solo proyecto.**
Razón: Orion acumula experiencia entre proyectos. Su valor crece con cada proyecto nuevo.

**G3. Los Tech Leads (Nestor, Olga) evalúan dentro de su dominio — no deciden arquitectura global.**
Razón: Los Tech Leads tienen criterio técnico propio dentro de su área, pero la arquitectura global la decide Orion con el Director.

**G4. Los agentes especializados (Dev, Tester, Seguridad) solo ejecutan lo que su Tech Lead define.**
Razón: Ejecución sin interpretación errónea. El Tech Lead es responsable de la calidad del prompt.

**G5. Cada proyecto tiene su propio contexto en `projects/nombre.md`.**
Razón: Orion necesita contexto específico por proyecto para tomar decisiones correctas.

---

## 2. FLUJO DE TRABAJO GLOBAL

**G6. El flujo siempre es: Orion crea issue → agente ejecuta → Director valida → Orion cierra.**
Razón: La validación humana es obligatoria. Ningún issue se cierra sin que el Director apruebe.

**G7. Todos los issues de un proyecto viven en el repo principal del proyecto.**
Razón: Un solo lugar por proyecto para gestionar el trabajo.

**G8. Formato de commit estándar para métricas:**
```
[AGENTE] tipo(scope): descripción ref #XX | size: S/M/L/XL
```
Ejemplo:
```
[NESTOR] fix(auth): cookie path corrected ref #42 | size: S
```
Tallas: S=~1k tokens, M=~3k, L=~8k, XL=~15k
Razón: Permite registrar consumo automáticamente via GitHub Actions sin gastar tokens.

**G9. Máximo 2 issues en In Progress simultáneamente por proyecto.**
Razón: El WIP limitado evita contexto fragmentado y mejora la calidad.

**G10. Cada issue incluye: contexto + prompt para el agente + plan de pruebas.**
Razón: Sin el prompt, el agente no tiene contexto suficiente. Sin el plan de pruebas, el Director no sabe qué verificar.

---

## 3. GIT GLOBAL

**G11. `main` solo se toca en deploys planificados.**
Razón: Autodeploy activo en producción. Todo el trabajo va a `develop`.

**G12. Los agentes siempre hacen `git pull origin develop` antes de trabajar.**
Razón: Previene conflictos de merge.

**G13. Los commits usan `ref #XX` — nunca `closes #XX` ni `fixes #XX`.**
Razón: El cierre de issues lo hace Orion manualmente después de validación del Director.

---

## 4. CALIDAD Y SEGURIDAD GLOBAL

**G14. Nunca commitear credenciales, tokens o secrets.**
Razón: Riesgo de seguridad crítico e irreversible.

**G15. El Director da luz verde antes de que Orion use cualquier MCP.**
Razón: Los MCPs consumen recursos. El Director controla el presupuesto de tokens.

**G16. Orion pide permiso antes de hacer cambios directos en GitHub.**
Razón: El Director tiene control del código. Excepción: emergencias de deploy activo en producción.

---

## 5. ESCALADO DEL EQUIPO

**G17. Agregar un agente nuevo = crear su archivo en `agents/` + definir su rol en el proyecto.**
Razón: El sistema escala por jerarquías. Cada agente nuevo hereda las reglas globales.

**G18. Los agentes especializados (Tester, Seguridad, DevOps) reportan a su Tech Lead.**
Razón: Nestor gestiona su equipo backend. Olga gestiona su equipo frontend. Orion coordina los Tech Leads.

---

## 6. MÉTRICAS

**G19. Todo commit de agente sigue el formato con `size:` para registro automático.**
Razón: Sin datos no hay optimización. El registro cuesta 0 tokens adicionales.

**G20. Los logs viven en `orion/logs/usage.jsonl` — un registro por línea.**
Razón: JSONL permite append atómico sin riesgo de corrupción.

---

*Última actualización: 26 de marzo de 2026 — Orion*
*Este archivo es la base del framework Orion OS.*
