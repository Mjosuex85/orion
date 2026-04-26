# GameOn — Project Context

> Technical context for Orion, Nestor and Olga.
> Business vision and ideas live in `projects/gameon-ideas.md`.
> Project-specific decisions live in `projects/gameon-decisions.md`.

---

## WHAT IS GAMEON

GameOn is a **player identity platform** for amateur sports. The differentiator is not managing matches — it's that when someone touches your spot on the field, your FIFA card appears with your photo, country, history, and stats.

**The right pitch:** "Replaces WhatsApp and paper for organizing matches."

### The two business layers
```
Free layer (social hook)
  └ Create matches (1/day), profile, share link with friends
  └ Browse organizations and join their matches

Paid layer (monetization)
  └ Organizations manage matches (4/day), tournaments (2/week), leagues (1/month)
  └ Players unlock real stats when participating in org tournaments/leagues
```

---

## STACK

### Backend — `Mjosuex85/gameon-api`
```
Runtime:    Node.js + NestJS 10
ORM:        TypeORM + PostgreSQL (Neon)
Auth:       JWT (access 15min, refresh 7d) + Google OAuth
Deploy:     Vercel (serverless) via api/index.ts
DB prod:    Neon PostgreSQL
DB local:   Docker port 5434
```

### Frontend — `Mjosuex85/gameon`
```
Framework:  Angular 21 (100% standalone components)
Styles:     TailwindCSS + SCSS
State:      Angular Signals
Deploy:     Vercel (gameon-nu.vercel.app)
```

---

## MODULES IMPLEMENTED

### Users & Auth
- JWT + Google OAuth
- Roles: `USER`, `ORGANIZER`, `ADMIN`
- Soft delete, email verification, password reset via Resend
- Adding OWNER to org → auto-assigns ORGANIZER role (#106)
- `username` field on `User` entity (auto-generated `user<random>` for existing rows)
- `isPublic` field on `UserProfile` (default `true`) — privacy control

### Public Profile
- `GET /users/:id/profile` — public endpoint, optional auth
- Returns `PublicProfileResponseDto`: `id`, `username`, `firstName`, `lastName`, `avatarUrl`, `country`, `flagUrl`, `bio`, `isPublic`
- If `isPublic = false` → 403 for non-owners, 200 for owner
- `PATCH /users/me/settings` — authenticated, updates `isPublic`

### Matches
- `visibility`: `PRIVATE` | `PUBLIC` | `ORGANIZATION`
- `PRIVATE` = not in public listings, but accessible with direct link (D71)
- `GET /matches` requires auth, returns only creator's matches
- `GET /matches/:id` is public
- Free users: 1 match/day. Organizers: 4 matches/day (PLAN_LIMITS)
- Free users can set a price (D72)
- `organizationId` optional — links match to org if visibility = ORGANIZATION

### Organizations
- Entity with `name`, `slug`, `logoUrl`, `description`, `plan` (FREE/PRO)
- Member roles: `OWNER` | `MANAGER` | `STAFF`
- OWNER created automatically when org is created
- Public endpoints: list, slug detail, org matches
- `GET /organizations/:id/matches` — default: today's matches; `?showAll=true`: all
- `POST /organizations` requires ADMIN — manual creation for early clients

### Tournaments
- Belongs to Organization
- `TournamentTeam`: name + captain
- `TournamentMatch`: separate entity with scores, round, scheduledAt
- Limit: 2/week per org

### Leagues
- Planned. Points-based. Issue pending.

### Settings (Frontend)
- `/profile/settings` — standalone page, `authGuard` protected
- Sections: Account (Privacy), About (Version), Danger zone (Logout)
- `UserService` created — owns non-auth user API calls
- Privacy toggle: `PATCH /users/me/settings` → updates `isPublic` inline with spinner feedback
- Toggle uses `<span role="switch">` + `.slider.checked` CSS class (accessible, Sonar-compliant)

---

## BUSINESS RULES

### Plan limits (src/common/constants/plan-limits.ts)
```typescript
USER:       { matchesPerDay: 1 }
ORGANIZER:  { matchesPerDay: 4, tournamentsPerWeek: 2, leaguesPerMonth: 1 }
```

### How someone becomes ORGANIZER
- **Demo phase:** Adding user as OWNER of an org → auto-assigns ORGANIZER role (#106)
- **Post-demo:** payment (Stripe) triggers automatic role assignment

---

## INFRASTRUCTURE

```
PRODUCTION: v1.5.0 (API) + v1.5.1 (Frontend) — DEPLOYED Session 30
  Frontend  →  Vercel (gameon-nu.vercel.app) — branch: main — v1.5.1
  Backend   →  Vercel (serverless) — branch: main — v1.5.0
  Database  →  Neon PostgreSQL (gameon-db)
  Migrations AddUsernameToUsers + AddIsPublicToUserProfiles → run manually via Neon SQL (Session 30)

STAGING: OPERATIONAL
  Frontend  →  Vercel Preview — branch: staging
  Backend   →  Vercel Preview — branch: staging
  Database  →  Neon PostgreSQL (gameon-db-pre)
```

### Deploy rules
```
develop  →  NO deploy (Ignored Build Step)
staging  →  Preview deploy (automatic on push)
main     →  Production deploy via Vercel Deploy Hook (triggered by release.yml job 2)
            ⚠️ Auto-deploy on push to main must be DISABLED in Vercel dashboard
            ⚠️ Secret VERCEL_DEPLOY_HOOK_API must be set in gameon-api GitHub secrets
```

### Migration protocol (updated Session 30 — Opción B)

**gameon-api `release.yml` now has 2 sequential jobs:**
```
Job 1: migrate
  - runs on PR merged to main
  - npm ci → build → db:migrate (DATABASE_URL → Neon prod)
  - if fails → workflow stops, Vercel deploy hook NOT triggered

Job 2: release (needs: migrate)
  - bump version (skips if already at target)
  - commit + tag
  - POST to VERCEL_DEPLOY_HOOK_API → triggers production deploy
  - create GitHub Release
```

**For staging migrations (still manual workflow_dispatch):**
```
GitHub Actions → gameon-api → "Migrate Staging DB" → Run workflow
Uses: DATABASE_URL_STAGING → gameon-db-pre
Run BEFORE merging develop → staging
```

**Emergency SQL fallback (if workflow fails):**
```sql
-- Run directly in Neon SQL console
-- Then register in TypeORM migrations table:
INSERT INTO "migrations" ("timestamp", "name") VALUES
  (<timestamp>, '<MigrationClassName>')
ON CONFLICT DO NOTHING;
```

### GitFlow
```
develop  →  free (agents commit directly)
    PR down
staging  →  CI runs + Quality Gate enforced
    PR down
main     →  CI runs + Quality Gate enforced + migrate job runs first
```

### Pending Vercel setup (BLOCKING for next release)
```
⚠️ Vercel dashboard → gameon-api → Settings → Git → Deploy Hooks
   → Create hook: name "GitHub Release", branch: main
   → Copy URL → GitHub secret: VERCEL_DEPLOY_HOOK_API

⚠️ Vercel dashboard → gameon-api → Settings → Git
   → Disable auto-deploy on production branch (main)
   → Without this, Vercel still deploys on push BEFORE migrations finish
```

---

## STAGING VALIDATION CHECKLIST

Before merging staging → main:

```
Backend staging URL: gameon-api-git-staging-mjosuex85s-projects.vercel.app
Frontend staging URL: gameon-git-staging-mjosuex85s-projects.vercel.app

✅ Auth
  POST /auth/login           → returns access_token
  GET  /auth/me              → returns user profile

✅ Matches
  GET  /matches              → returns creator's matches (requires auth)
  GET  /matches/:id          → public, returns match detail

✅ Organizations
  GET  /organizations                         → public list
  GET  /organizations/:slug                   → public detail
  GET  /organizations/:id/matches             → only today's matches
  GET  /organizations/:id/matches?showAll=true → all matches
  GET  /organizations/my                      → requires ORGANIZER role

✅ Public Profile + Privacy
  GET  /users/:id/profile    → returns PublicProfileResponseDto (no token)
  GET  /users/:id/profile    → 403 if isPublic=false and no token
  PATCH /users/me/settings   → updates isPublic (requires JWT)
  Settings page              → toggle works end-to-end with spinner + toast
```

---

## CI / QUALITY

### gameon-api CI
- Lint + Build + Tests + Audit (critical, prod only) + SonarCloud
- **Quality Gate blocking: ACTIVE**
- SonarQube for IDE mandatory for Nestor before every commit

### gameon CI
- Lint + Build + Tests + SonarCloud
- **Quality Gate blocking: ACTIVE**
- SonarQube MCP mandatory for Olga before every commit
- Automatic Analysis: DISABLED — analysis runs via GitHub Actions only

### Bruno QA
- `bruno.yml` in both repos — triggers on PR to staging/main
- ✅ on PR → Mario can merge. ❌ → fix first.

### SonarCloud — 3-layer strategy (D92)
```
Layer 1 — IDE:     Nestor (VSCode SonarQube) + Olga (Antigravity SonarQube MCP) ✅ mandatory
Layer 2 — CI:      Quality Gate blocks PR on both repos ✅
Layer 3 — Manual:  Orion triages only on critical deploys
```

---

## TESTING STATUS

### Backend
```
MatchService          → 78%+ (complete)
AuthService           → 97%+ (complete)
OrganizationsService  → 76%+ (complete)
UsersService          → getPublicProfile + mapToPublicProfile + updatePrivacySettings + edge cases
CI + SonarCloud       → active + Quality Gate blocking
```

### Frontend
```
core/ (services, interceptors, guards) → 82.88%
UserService    → updatePrivacySettings covered
SettingsComponent → togglePrivacy + logout + error path covered
AuthService    → refreshUserProfile covered
CI + SonarCloud → active + Quality Gate blocking
```

Rule: every new feature issue includes its tests.

---

## FRONTEND — ATOMIC DESIGN SYSTEM

```
src/app/shared/
  ui/
    atoms/     → button, input, modal, loader, toast
    molecules/ → stat-card, match-row, empty-state, match-filters, confirm-dialog
  components/
    event-card/  → not migrated yet
    main-layout/ → not migrated yet
```

Rules:
- CSS uses design tokens from styles.scss — never hardcoded values
- Features import from shared/ui/ — never define own UI primitives
- Responsive: Tailwind-first, mobile-first
- Angular: `output()` not `@Output()`, `signal()` for state, `@if`/`@for` syntax
- Tailwind: `[class.class-name]` bindings, not ternary
- Icons: Material Symbols via CDN (`crossorigin="anonymous"` on link tag)

---

## STATUS — April 26, 2026 (Session 30)

**Production:**
- API: v1.5.0 ✅ (gameon-api — deployed Session 30)
- Frontend: v1.5.1 ✅ (gameon — deployed Session 30)

**Completed this session (Session 30):**
- ✅ PR #18 → develop → staging (SCSS toggle fix) → CI verde, mergeado
- ✅ PR #19 → staging → main frontend (v1.5.1) → merged + deployed
- ✅ PR #158 → staging → main backend (v1.5.0) → merged + deployed
- ✅ Migrations AddUsernameToUsers + AddIsPublicToUserProfiles → run via Neon SQL prod
- ✅ fix(ci): release.yml ambos repos — skip npm bump si versión ya coincide
- ✅ feat(ci): gameon-api release.yml — Opción B: migrate job antes de deploy
- ✅ migrate.yml separado (workflow_dispatch) — se mantiene para emergencias

**CI fixes aplicados en ambos repos:**
```
release.yml — Bump package.json version:
  ANTES: npm version $VERSION_CLEAN --no-git-tag-version  → falla si ya coincide
  AHORA: compara CURRENT vs VERSION_CLEAN, salta si igual
```

**Incidencia documentada — lección aprendida:**
```
Problema:   API desplegada en prod con código que requería columnas inexistentes
Causa:      Migraciones no corrieron antes del deploy (workflow_dispatch manual olvidado)
Impacto:    GET /users/:id/profile daba error en prod hasta correr SQL manual
Solución:   Opción B — release.yml job 1: migrate → job 2: release + deploy hook
Pendiente:  Configurar VERCEL_DEPLOY_HOOK_API + deshabilitar auto-deploy en Vercel
```

**Open / Pending:**
- ⚠️ CRÍTICO: Configurar Vercel Deploy Hook + secret VERCEL_DEPLOY_HOOK_API (antes del próximo release)
- 📋 Session 31: dummy migration para probar el pipeline completo migrate → deploy
- 📋 #113 — GitHub Team branch protection (post first client)
- 📋 #117 — Multi-sport foundation (backlog)
- 📋 RFC match-lifecycle — Pending
- 🔔 Demo with Jose (SoccerMix) — date TBD
- ⚠️ `nestor-gameon-agent` + `olga-gameon-agent` → expire May 9, 2026 — renew before

**Next session priorities (Session 31):**
1. Configurar Vercel Deploy Hook en dashboard (Mario) + añadir secret VERCEL_DEPLOY_HOOK_API
2. Deshabilitar auto-deploy en Vercel main (Mario)
3. Crear dummy migration en develop (inocua — ej: comentario en columna o índice redundante)
4. Flujo completo: develop → staging → main → verificar que job migrate corre primero, luego Vercel hook
5. Definir siguiente ciclo de features

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today.

**Demo 2:** Group of friends who organize casual football matches.

---

*Part of Orion OS v1.5.0 — updated April 26, 2026 (Session 30)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
