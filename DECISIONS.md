# DECISIONS.md — GameOn + Orion OS

Este archivo documenta las decisiones técnicas, de proceso y de equipo tomadas en el proyecto. El **por qué** detrás de cada regla. Lo mantiene Orion y lo lee todo el equipo.

---

## 1. EQUIPO Y ROLES

**D1. Mario es el único tomador de decisiones de negocio.**

**D2. Orion actúa como arquitecto y coordinador técnico.**

**D3. Nestor ejecuta solo en backend. Olga solo en frontend.**

**D4. Mario siempre prueba antes de que se cierre un issue.**

---

## 2. FLUJO DE TRABAJO

**D5. Todos los issues viven en `gameon-api`, incluyendo los del frontend.**

**D6. Cada issue incluye: descripción + prompt para el agente + plan de pruebas.**

**D7. Máximo 2 issues en In Progress simultáneamente.**

**D8. Cuando Orion dice "después lo hacemos", se crea un issue en el momento.**

**D9. Cuando Mario propone una idea, Orion la analiza técnicamente antes de crear el issue.**

**D52. Los commits usan `ref #XX` — nunca `closes #XX` ni `fixes #XX`.**

**D53. Orion cierra los issues — nunca se cierran automáticamente.**

**D55. Cambios de configuración o documentación — Mario los recibe con `git pull` después de que Orion los sube.**

**D56. Mario da luz verde antes de que Orion use cualquier MCP.**

---

## 3. GIT Y RAMAS

**D10. `develop` es la rama de trabajo activa. `main` es producción.**

**D11. `main` NO SE TOCA — excepto en deploys planificados a producción.**

**D12. Nestor, Olga y Mario hacen `git pull origin develop` antes de empezar a trabajar.**
Razón: Orion puede haber subido cambios directamente a GitHub (configuración, seguridad, documentación). El pull garantiza que todos parten del estado real del repo.

**D13. Los commits del repo `gameon` referencian issues con `Mjosuex85/gameon-api#numero`.**

**D14. Orion puede hacer cambios directos en GitHub en `develop` para:**
- Configuración (`.npmrc`, `.env.example`, etc.)
- Documentación (`CLAUDE.md`, `AGENTS.md`, `README`)
- Correcciones urgentes de arquitectura decididas con Mario
- Archivos de Orion OS (`ORION.md`, `DECISIONS.md`, subagentes, etc.)

Para lógica de negocio o código de aplicación → issue para Nestor/Olga siempre.

**D44. Antes de conectar cualquier plataforma de deploy a `main`, verificar que `develop` y `main` están sincronizados.**

---

## 4. DEPLOYS A PRODUCCIÓN

**D49. Los deploys a producción se hacen en fechas planificadas.**

**D50. Durante un deploy activo con problemas críticos, Orion puede hacer cambios directos en `main`.**
Siempre aplicar el mismo cambio a `develop` después.

**D51. Orion NUNCA hace cambios en `main` fuera de un deploy activo.**

---

## 5. SEGURIDAD

**D35. Nunca commitear credenciales, tokens o secrets.**

**D36. Nunca usar `any` en TypeScript sin justificación explícita.**

**D37. Nunca romper contratos de DTOs que el frontend ya consume.**

**D73. `ignore-scripts=true` en `.npmrc` en todos los proyectos.**
Razón: Previene la ejecución automática de scripts maliciosos de paquetes npm durante `npm install`. Es una práctica de seguridad estándar aplicable a todos los proyectos de Orion OS.
Si algún paquete necesita scripts de compilación nativos (ej: `bcrypt`), evaluar reemplazarlo por alternativa pure-JS (ej: `bcryptjs`) o justificar explícitamente la excepción.

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

**D46. `GOOGLE_CALLBACK_URL` en la Google Strategy — nunca URLs hardcodeadas.**

**D47. `FRONTEND_URL` en el auth controller para el redirect post-OAuth.**

**D59. `password` field con `select: false` nunca se carga en queries normales.**

**D69. NO usar SnakeNamingStrategy en TypeORM.**
Razón: Las entidades existentes tienen columnas en camelCase real en la DB. Activar la strategy las rompería en producción.

**D70. En entidades nuevas, toda propiedad camelCase DEBE tener `name` explícito en snake_case.**
```typescript
@Column({ name: 'logo_url', nullable: true })
logoUrl?: string;
```
Excepción: `@CreateDateColumn()`, `@UpdateDateColumn()`, `@DeleteDateColumn()` generan snake_case automáticamente.

**D71. `Match.visibility = PRIVATE` significa "no aparece en listados públicos" — NO "solo el creador puede verlo".**
- `GET /matches/:id` — público, cualquiera con el ID puede verlo y registrarse
- `PRIVATE` solo afecta los listados

**D72. El usuario free puede poner precio a su partido sin plan de pago.**
Es una herramienta organizativa (Bizum entre amigos), no monetización de GameOn.

---

## 7. ARQUITECTURA — FRONTEND

**D26. Todas las llamadas HTTP van a través de `ApiService`.**

**D27. La URL del backend vive en `environment.ts` / `environment.prod.ts`.**

**D28. El proyecto es 100% standalone components — sin NgModules.**

**D29. Fallback para participantes con `position == null` — asignar por orden de array.**

**D57. El manejo del 401 vive únicamente en `auth.interceptor.ts`.**

**D62. Límites de tamaño de componentes Angular:**
- HTML: máx 150 líneas / TS: máx 200 líneas / SCSS: máx 20kB

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
GOOGLE_CALLBACK_URL, FRONTEND_URL, ORIGIN, NODE_ENV, RESEND_API_KEY
```

---

## 9. HERRAMIENTAS Y MCPS

**D38. GitHub MCP conectado a Claude.**

**D39. MCP de PostgreSQL local — pendiente (#31). MCP de Notion — pendiente (#33).**

**D68. Nestor y Olga usan el MCP de GitHub para leer el issue asignado (body + comentarios).**
Para leer código → IDE local siempre (VSCode para Nestor, Antigravity para Olga).

---

## 10. AGENTES Y MODELOS

**D54. Escalar de Sonnet 4.6 a Opus 4.6 cuando:**
- El codebase supera los 500k tokens
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

## 11. FUTURO

**D40. Orion como arquitecto multi-proyecto.**

**D41. Kubernetes cuando GameOn tenga +500 usuarios activos o hosting > $50/mes.**

**D43. GitHub Actions CI/CD + SonarCloud + Branch protection — después de la demo.**

---

## 12. ORION OS

**D60. ORION.md vive en `Mjosuex85/orion` (main) — no en `gameon-api`.**

**D61. CLAUDE.md en cada repo = cómo trabajar (≤150 líneas). Business Rules en `orion/projects/gameon.md`.**

**D65. Subagentes viven en `orion/agents/subagents/` — reutilizables en otros proyectos.**

---

*Última actualización: 28 de marzo de 2026 — Orion*
*Decisiones nuevas esta sesión: D73, D14 redefinida*
