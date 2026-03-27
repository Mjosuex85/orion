# DECISIONS.md — GameOn + Orion OS

Este archivo documenta las decisiones técnicas, de proceso y de equipo tomadas en el proyecto. El **por qué** detrás de cada regla. Lo mantiene Orion y lo lee todo el equipo.

---

## 1. EQUIPO Y ROLES

**D1. Mario es el único tomador de decisiones de negocio.**
Razón: La visión del producto es suya. El equipo técnico ejecuta, analiza y propone — pero no decide.

**D2. Orion actúa como arquitecto y coordinador técnico.**
Razón: Centralizar el análisis en un punto evita inconsistencias entre lo que hacen Nestor y Olga.

**D3. Nestor ejecuta solo en backend. Olga solo en frontend.**
Razón: La especialización evita errores de contexto.

**D4. Mario siempre prueba antes de que se cierre un issue.**
Razón: El agente no puede probar la app real. Un issue no está Done hasta que Mario lo confirma.

---

## 2. FLUJO DE TRABAJO

**D5. Todos los issues viven en `gameon-api`, incluyendo los del frontend.**

**D6. Cada issue incluye: descripción + prompt para el agente + plan de pruebas.**

**D7. Máximo 2 issues en In Progress simultáneamente.**

**D8. Cuando Orion dice "después lo hacemos", se crea un issue en el momento.**

**D9. Cuando Mario propone una idea, Orion la analiza técnicamente antes de crear el issue.**

**D52. Los commits usan `ref #XX` — nunca `closes #XX` ni `fixes #XX`.**
Razón: GitHub cierra issues automáticamente con `closes`. El cierre lo hace Orion manualmente.

**D53. Orion cierra los issues — nunca se cierran automáticamente.**

**D55. Cambios de pocas líneas — Mario los hace directamente.**

**D56. Mario da luz verde antes de que Orion use cualquier MCP.**

---

## 3. GIT Y RAMAS

**D10. `develop` es la rama de trabajo activa. `main` es producción.**

**D11. `main` NO SE TOCA — excepto en deploys planificados a producción.**

**D12. Nestor y Olga siempre hacen `git pull origin develop` antes de empezar.**

**D13. Los commits del repo `gameon` referencian issues con `Mjosuex85/gameon-api#numero`.**

**D44. Antes de conectar cualquier plataforma de deploy a `main`, verificar que `develop` y `main` están sincronizados.**

---

## 4. DEPLOYS A PRODUCCIÓN

**D49. Los deploys a producción se hacen en fechas planificadas.**

**D50. Durante un deploy activo con problemas críticos, Orion puede hacer cambios directos en `main`.**
Razón: Emergencias. Siempre aplicar el mismo cambio a `develop` después.

**D51. Orion NUNCA hace cambios en `main` fuera de un deploy activo.**

---

## 5. LÍMITES DE CAMBIO DIRECTO

**D14. Orion hace cambios en GitHub (en `develop`) cuando son ≤ 4 líneas — solo si Mario lo pide.**

**D15. Cambios de documentación siempre van a `develop`.**

---

## 6. ARQUITECTURA — BACKEND

**D16. `MatchParticipant` es una entidad intermedia propia, no ManyToMany simple.**

**D17. `position` en `MatchParticipant` es el índice 0-based del spot en el campo.**

**D18. `@DeleteDateColumn()` para soft delete — nunca hard delete.**

**D19. `synchronize: false` en producción. Las migraciones manejan el schema.**

**D20. Las migraciones siempre se versionan en el repo.**

**D21. PostgreSQL para usuarios — definitivo.**

**D22. Stats migrarán a Redis en el futuro.**

**D23. Reviews irán a microservicio separado.**

**D24. No cambiar el nombre del módulo `matchs`.**

**D25. `mapToDto` expone `avatarUrl`, `country`, `flagUrl` de creator y participants.**

**D45. Con `@DeleteDateColumn`, nunca usar `.where('deletedAt IS NULL')` manual.**
Razón: Causa 500s en producción por columna ambigua en JOINs.

**D46. `GOOGLE_CALLBACK_URL` en la Google Strategy — nunca URLs hardcodeadas.**

**D47. `FRONTEND_URL` en el auth controller para el redirect post-OAuth.**

**D59. `password` field con `select: false` nunca se carga en queries normales.**
Razón: No usar `!user.password` en condiciones. Solo verificar `user.provider`.

**D69. NO usar SnakeNamingStrategy en TypeORM.**
Razón: Las entidades existentes tienen columnas en camelCase real en la DB (`emailVerified`, `providerId`, `lastLoginAt`, `birthDate`, `flagUrl`, `avatarUrl`, `phoneNumber`). Activar la strategy rompería esas columnas en producción.

**D70. En entidades nuevas, toda propiedad camelCase DEBE tener `name` explícito en snake_case.**
```typescript
// CORRECTO
@Column({ name: 'logo_url', nullable: true })
logoUrl?: string;

@Column({ name: 'organization_id', type: 'uuid' })
organizationId: string;

// INCORRECTO — TypeORM usa el nombre literal de la propiedad
@Column()
logoUrl?: string; // busca columna "logoUrl" en DB, no "logo_url"
```
Excepción: `@CreateDateColumn()`, `@UpdateDateColumn()`, `@DeleteDateColumn()` generan snake_case automáticamente — no necesitan `name`.
Razón: Detectado en issue #76 — las migraciones generaban `logo_url` pero TypeORM buscaba `logoUrl` en runtime, fallando silenciosamente sin error en build.

---

## 7. ARQUITECTURA — FRONTEND

**D26. Todas las llamadas HTTP van a través de `ApiService`.**

**D27. La URL del backend vive en `environment.ts` / `environment.prod.ts`.**

**D28. El proyecto es 100% standalone components — sin NgModules.**

**D29. Fallback para participantes con `position == null` — asignar por orden de array.**

**D57. El manejo del 401 vive únicamente en `auth.interceptor.ts`.**
Razón: Dos interceptores manejando el 401 causan condición de carrera.

**D62. Límites de tamaño de componentes Angular — obligatorio para Olga:**
- HTML: máx 150 líneas
- TS: máx 200 líneas
- SCSS: máx 20kB

**D67. Parciales SCSS de un componente viven en una carpeta `styles/` dentro del propio componente.**

---

## 8. DEPLOY

**D30. Backend en Vercel (serverless). Frontend en Vercel (free tier).**

**D31. Autodeploy activado en `main` en Vercel.**

**D32. Antes de cualquier deploy, Orion revisa: `app.module.ts`, `main.ts`, `package.json`, strategies de auth y variables de entorno.**

**D33. `migrationsRun: true` en producción — las migraciones corren al arrancar.**

**D48. Variables de entorno necesarias en producción (Vercel):**
```
DATABASE_URL
JWT_ACCESS_SECRET, JWT_REFRESH_SECRET
JWT_ACCESS_EXPIRES_IN=900, JWT_REFRESH_EXPIRES_IN=604800
GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET
GOOGLE_CALLBACK_URL=https://<backend>.vercel.app/auth/google/callback
FRONTEND_URL=https://<frontend>.vercel.app
ORIGIN=https://<frontend>.vercel.app
NODE_ENV=production
RESEND_API_KEY=re_xxxxxxxxxxxx
```

---

## 9. CALIDAD Y SEGURIDAD

**D35. Nunca commitear credenciales, tokens o secrets.**

**D36. Nunca usar `any` en TypeScript sin justificación explícita.**

**D37. Nunca romper contratos de DTOs que el frontend ya consume.**

---

## 10. HERRAMIENTAS Y MCPS

**D38. GitHub MCP conectado a Claude.**

**D39. MCP de PostgreSQL local — pendiente (#31). MCP de Notion — pendiente (#33).**

**D68. Nestor y Olga usan el MCP de GitHub SOLO para leer el issue asignado.**
Para leer código → IDE local siempre (VSCode para Nestor, Antigravity para Olga).

---

## 11. AGENTES Y MODELOS

**D54. Escalar de Sonnet 4.6 a Opus 4.6 cuando:**
- El codebase supera los 500k tokens
- Queremos paralelizar trabajo en un feature grande
- Sonnet no resuelve arquitectura compleja después de dos intentos

**D58. Nestor y Olga deben usar Sonnet 4.6 mínimo.**

**D63. Estrategia de tokens: Copilot/Gemini para tareas S/M, Claude CLI para L/XL.**

**D64. El valor del sistema está en el contexto, no en el modelo.**

**D66. Selección de modelo por complejidad de task:**

| Task | Modelo mínimo |
|------|--------------|
| Refactor mecánico con pasos definidos | Gemini Flash / Haiku |
| Bug analysis + diagnóstico | Gemini Pro / Sonnet |
| Arquitectura, features nuevas | Gemini Pro / Sonnet |
| Decisiones de arquitectura global | Claude Opus / Orion |

---

## 12. FUTURO

**D40. Orion como arquitecto multi-proyecto.**

**D41. Kubernetes cuando GameOn tenga +500 usuarios activos o hosting > $50/mes.**

**D43. GitHub Actions CI/CD + SonarCloud + Branch protection — después de la demo.**

---

## 13. ORION OS

**D60. ORION.md vive en `Mjosuex85/orion` (main) — no en `gameon-api`.**

**D61. CLAUDE.md en cada repo = cómo trabajar (≤150 líneas). Business Rules en `orion/projects/gameon.md`.**

**D65. Subagentes viven en `orion/agents/subagents/` — reutilizables en otros proyectos.**

---

*Última actualización: 27 de marzo de 2026 — Orion*
*Decisiones nuevas esta sesión: D66, D67, D68, D69, D70*
