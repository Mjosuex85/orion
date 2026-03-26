# DECISIONS.md — GameOn + Orion OS

Este archivo documenta las decisiones técnicas, de proceso y de equipo tomadas en el proyecto. El **por qué** detrás de cada regla. Lo mantiene Orion y lo lee todo el equipo.

---

## 1. EQUIPO Y ROLES

**D1. Mario es el único tomador de decisiones de negocio.**
Razón: La visión del producto es suya. El equipo técnico ejecuta, analiza y propone — pero no decide.

**D2. Orion actúa como arquitecto y coordinador técnico.**
Razón: Centralizar el análisis en un punto evita inconsistencias entre lo que hacen Nestor y Olga. Orion lee el código real antes de proponer cambios.

**D3. Nestor ejecuta solo en backend. Olga solo en frontend.**
Razón: La especialización evita errores de contexto. Cada uno conoce profundamente su repo.

**D4. Mario siempre prueba antes de que se cierre un issue.**
Razón: El agente no puede probar la app real. Mario es el único que puede validar la experiencia de usuario. Un issue no está Done hasta que Mario lo confirma — sin excepción.

---

## 2. FLUJO DE TRABAJO

**D5. Todos los issues viven en `gameon-api`, incluyendo los del frontend.**
Razón: Un solo lugar para gestionar el trabajo evita fragmentación y facilita la visibilidad del proyecto completo.

**D6. Cada issue incluye: descripción + prompt para el agente + plan de pruebas.**
Razón: Sin el prompt, Nestor/Olga no tienen contexto suficiente. Sin el plan de pruebas, Mario no sabe qué verificar.

**D7. Máximo 2 issues en In Progress simultáneamente.**
Razón: Limitar el WIP (Work In Progress) evita contexto fragmentado y mejora el foco.

**D8. Cuando Orion dice "después lo hacemos", se crea un issue en el momento.**
Razón: Las ideas que no se documentan se pierden. El issue captura la idea con su contexto mientras está fresco.

**D9. Cuando Mario propone una idea, Orion la analiza técnicamente antes de crear el issue.**
Razón: No todas las ideas son buenas decisiones técnicas. El análisis previo evita deuda técnica disfrazada de "buenas ideas".

**D52. Los commits de Nestor y Olga usan `ref #XX` — nunca `closes #XX` ni `fixes #XX`.**
Razón: GitHub cierra issues automáticamente con `closes`. Eso saltaría la validación de Mario. El cierre siempre lo hace Orion manualmente, después de que Mario confirma que funciona.

**D53. Orion cierra los issues — nunca se cierran automáticamente.**
Razón: El cierre de un issue es la señal de que el trabajo está validado por Mario. Es un acto deliberado, no un efecto secundario de un commit.

**D55. Cambios de pocas líneas en un solo archivo — Mario los hace directamente.**
Razón: Es más eficiente y mantiene a Mario en control del código. Orion solo hace cambios directos si Mario lo pide explícitamente, o durante emergencias de deploy activo.

**D56. Mario da luz verde antes de que Orion use cualquier MCP.**
Razón: Los MCPs consumen tokens y recursos. Mario decide cuándo vale la pena usarlos.

---

## 3. GIT Y RAMAS

**D10. `develop` es la rama de trabajo activa. `main` es producción.**
Razón: Separar el trabajo en curso de lo que está en producción protege la estabilidad del sistema.

**D11. `main` NO SE TOCA — excepto en deploys planificados a producción.**
Razón: Vercel tiene autodeploy en `main`. Cualquier commit dispara un redeploy innecesario.

**D12. Nestor y Olga siempre hacen `git pull origin develop` antes de empezar.**
Razón: Los conflictos de merge se generaron por no hacer pull antes de trabajar.

**D13. Los commits del repo `gameon` referencian issues con `Mjosuex85/gameon-api#numero`.**
Razón: Mantiene la trazabilidad entre el código del frontend y el issue que lo originó.

**D44. Antes de conectar cualquier plataforma de deploy a `main`, verificar que `develop` y `main` están sincronizados.**
Razón: En el primer deploy el merge no incluyó el código real del frontend. Vercel desplegó una versión vieja.

---

## 4. DEPLOYS A PRODUCCIÓN

**D49. Los deploys a producción se hacen en fechas planificadas.**
Razón: No se hace merge a `main` de forma espontánea.

**D50. Durante un deploy activo con problemas críticos, Orion puede hacer cambios directos en `main`.**
Razón: En emergencias de producción la velocidad importa. Siempre aplicar el mismo cambio a `develop` después.

**D51. Orion NUNCA hace cambios en `main` fuera de un deploy activo.**
Razón: Cualquier commit a `main` dispara autodeploy.

---

## 5. LÍMITES DE CAMBIO DIRECTO

**D14. Orion hace cambios directamente en GitHub (en `develop`) cuando son ≤ 4 líneas — solo si Mario lo pide explícitamente.**
Razón: Mario tiene control del código.

**D15. Cambios de documentación siempre van a `develop`.**
Razón: La documentación no es urgente para producción.

---

## 6. ARQUITECTURA — BACKEND

**D16. `MatchParticipant` es una entidad intermedia propia, no ManyToMany simple.**
Razón: Necesitamos campos propios como `registeredAt` y `position`.

**D17. `position` en `MatchParticipant` es el índice 0-based del spot en el campo.**
Razón: Permite renderizar al jugador en su spot real al recargar la página.

**D18. `@DeleteDateColumn()` para soft delete — nunca hard delete.**
Razón: Los datos borrados pueden necesitarse para auditoría o recuperación.

**D19. `synchronize: false` en producción. Las migraciones manejan el schema.**
Razón: `synchronize: true` puede eliminar columnas en producción.

**D20. Las migraciones siempre se versionan en el repo.**
Razón: Sin las migraciones no se puede reproducir el schema en una DB nueva.

**D21. PostgreSQL para usuarios — definitivo.**

**D22. Stats migrarán a Redis en el futuro — no implementar en PostgreSQL de forma compleja.**

**D23. Reviews irán a microservicio separado.**

**D24. No cambiar el nombre del módulo `matchs`.**
Razón: Hay referencias en toda la codebase.

**D25. `mapToDto` expone `avatarUrl`, `country`, `flagUrl` de creator y participants.**
Razón: El frontend necesita estos datos para la FIFA card.

**D45. Con `@DeleteDateColumn`, nunca usar `.where('deletedAt IS NULL')` manual.**
Razón: TypeORM filtra los soft-deleted automáticamente. El WHERE manual causa 500s en producción.

**D46. `GOOGLE_CALLBACK_URL` en la Google Strategy — nunca URLs hardcodeadas.**
Razón: URLs hardcodeadas → `undefined` en producción → Google OAuth falla.

**D47. `FRONTEND_URL` en el auth controller para el redirect post-OAuth.**
Razón: El fallback a `localhost:4200` hace que OAuth redirija al local en producción.

---

## 7. ARQUITECTURA — FRONTEND

**D26. Todas las llamadas HTTP van a través de `ApiService`.**

**D27. La URL del backend vive en `environment.ts` / `environment.prod.ts`.**

**D28. El proyecto es 100% standalone components — sin NgModules.**

**D29. Fallback para participantes con `position == null` — asignar por orden de array.**
Razón: Participantes registrados antes del issue #19 tienen `position: null`.

**D57. El manejo del 401 vive únicamente en `auth.interceptor.ts` — nunca en `error.interceptor.ts`.**
Razón: Dos interceptores manejando el 401 causan condición de carrera. El `auth.interceptor` intenta el refresh primero; si falla, redirige al login.

---

## 8. DEPLOY

**D30. Backend en Vercel (serverless). Frontend en Vercel (free tier).**

**D31. Autodeploy activado en `main` en Vercel.**
Razón: Por eso `main` no se toca excepto en deploys planificados.

**D32. Antes de cualquier deploy, Orion revisa obligatoriamente: `app.module.ts`, `main.ts`, `package.json`, strategies de auth y variables de entorno.**

**D33. `migrationsRun: true` en producción — las migraciones corren al arrancar.**
Razón: El entorno serverless no tiene Shell.

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
Razón: Permite a Orion leer y escribir en ambos repos directamente.

**D39. MCP de PostgreSQL local — pendiente (#31). MCP de Notion — pendiente (#33).**

---

## 11. AGENTES Y MODELOS

**D54. Escalar de Sonnet 4.6 a Opus 4.6 cuando:**
- El codebase supera los 500k tokens
- Queremos paralelizar trabajo de Nestor y Olga en un feature grande
- Sonnet no resuelve un problema de arquitectura compleja después de dos intentos

**D58. Nestor y Olga deben usar Sonnet 4.6 mínimo.**
Razón: Haiku 4.5 no sigue reglas complejas — se salta el protocolo de "Ready to test" y commitea sin aprobación.

**D59. `password` field con `select: false` nunca se carga en queries normales.**
Razón: No usar `!user.password` en condiciones de negocio. Solo verificar `user.provider`.

---

## 12. FUTURO

**D40. Orion como arquitecto multi-proyecto a futuro.**
Razón: El framework establecido en GameOn es replicable en otros proyectos. Orion OS.

**D41. Kubernetes cuando GameOn tenga +500 usuarios activos o el hosting cueste más de $50/mes.**

**D43. GitHub Actions CI/CD (#23) + SonarCloud (#24) + Branch protection (#25) — después de la demo.**

---

## 13. ORION OS (NUEVO — SESIÓN 7)

**D60. ORION.md vive en `Mjosuex85/orion` (main) — no en `gameon-api`.**
Razón: Orion OS es multi-proyecto. La memoria de Orion no pertenece a un proyecto específico.

**D61. CLAUDE.md en cada repo = cómo trabajar (≤150 líneas). Business Rules en `orion/projects/gameon.md`.**
Razón: El CLAUDE.md debe ser mínimo — solo lo que el agente necesita para operar. El contexto profundo del producto vive en el repo orion, inyectable por issue cuando se necesita.

---

*Última actualización: 26 de marzo de 2026 — Orion*
*Decisiones nuevas esta sesión: D58, D59, D60, D61*
