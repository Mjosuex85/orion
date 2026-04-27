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

### Settings (Frontend)
- `/profile/settings` — standalone page, `authGuard` protected
- Sections: Account (Privacy), About (Version), Danger zone (Logout)
- Privacy toggle: `PATCH /users/me/settings` → updates `isPublic` inline with spinner + toast feedback ✅ v1.5.1
- `UserService` owns non-auth user API calls
- `AuthService.refreshUserProfile()` re-fetches profile and updates signal after toggle

### Matches
- `visibility`: `PRIVATE` | `PUBLIC` | `ORGANIZATION`
- `PRIVATE` = not in public listings, but accessible with direct link (D71)
- `GET /matches` requires auth, returns only creator's matches
- `GET /matches/:id` is public
- Free users: 1 match/day. Organizers: 4 matches/day (PLAN_LIMITS)
- Free users can set a price (D72)
- `organizationId` optional — links match to org if visibility = ORGANIZATION
- Event detail: proper not-found state when match is deleted (v1.5.2)

### Organizations
- Entity with `name`, `slug`, `logoUrl`, `description`, `plan` (FREE/PRO)
- Member roles: `OWNER` | `MANAGER` | `STAFF`
- OWNER created automatically when org is created
- Public endpoints: list, slug detail, org matches
- `GET /organizations/:id/matches` — default: today's matches; `?showAll=true`: all
- `POST /organizations` requires ADMIN — manual creation for early clients
- Organizer panel: match + venue action dropdowns working (v1.5.2)

### Tournaments
- Belongs to Organization
- `TournamentTeam`: name + captain
- `TournamentMatch`: separate entity with scores, round, scheduledAt
- Limit: 2/week per org

### Leagues
- Planned. Points-based. Issue pending.

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
PRODUCTION: v1.5.0 (API) + v1.5.2 (Frontend) — current
  Frontend  →  Vercel (gameon-nu.vercel.app) — branch: main — v1.5.2
  Backend   →  Vercel (serverless) — branch: main — v1.5.0
  Database  →  Neon PostgreSQL (gameon-db)

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

### Migration protocol (Opción B — active since Session 30)

**gameon-api `release.yml` — 2 sequential jobs:**
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

**For staging migrations (manual workflow_dispatch):**
```
GitHub Actions → gameon-api → "Migrate Staging DB" → Run workflow
Uses: DATABASE_URL_STAGING → gameon-db-pre
Run BEFORE merging develop → staging
```

### GitFlow
```
develop  →  free (agents commit directly)
    PR down
staging  →  CI runs + Quality Gate enforced
    PR down
main     →  CI runs + Quality Gate enforced + migrate job runs first
```

### Pending Vercel setup (BLOCKING for next backend release)
```
⚠️ Vercel dashboard → gameon-api → Settings → Git → Deploy Hooks
   → Create hook: name "GitHub Release", branch: main
   → Copy URL → GitHub secret: VERCEL_DEPLOY_HOOK_API

⚠️ Vercel dashboard → gameon-api → Settings → Git
   → Disable auto-deploy on production branch (main)
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
  GET  /event/:deleted-id    → shows not-found state (not broken page)

✅ Organizations
  GET  /organizations                         → public list
  GET  /organizations/:slug                   → public detail
  GET  /organizations/:id/matches             → only today's matches
  GET  /organizations/:id/matches?showAll=true → all matches
  GET  /organizations/my                      → requires ORGANIZER role

✅ Organizer panel
  Mis Partidos → three-dot dropdown opens correctly
  Venues       → action dropdown opens correctly

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
core/ (services, interceptors, guards) → 82.88%+
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

## STATUS — April 27, 2026 (Session 31)

**Production:**
- API: v1.5.0 ✅
- Frontend: v1.5.2 ✅ — deployed and validated

**Completed this session (Session 31):**
- ✅ Deploy Hook philosophy confirmed — Opción B is the right approach
- ✅ RFC restructure: `rfcs/` now organized by project (`rfcs/gameon/`, `rfcs/nutriapp/`)
- ✅ RFC `multi-sport-architecture.md` created — decision deferred, football first
- ✅ #156 + #7 closed — Privacy toggle shipped in v1.5.1
- ✅ #159 closed — Organizer matches dropdown fix (v1.5.2)
- ✅ #160 closed — Organizer venues dropdown fix (v1.5.2)
- ✅ #161 closed — Event detail not-found state (v1.5.2)
- ✅ v1.5.2 deployed to production and validated by Mario

**Open / Pending:**
- ⚠️ CRÍTICO: Configurar Vercel Deploy Hook + secret `VERCEL_DEPLOY_HOOK_API` (Mario — before next backend release)
- ⚠️ CRÍTICO: Deshabilitar auto-deploy en Vercel main backend (Mario)
- 📋 Next: dummy migration to test full pipeline migrate → deploy
- 📋 #151 — Re-enable SonarCloud Quality Gate blocking (frontend)
- 📋 #150 — OrganizationService unit tests (Olga)
- 📋 #152 — SonarQube IDE for Olga (Antigravity)
- 📋 #148 — Order by date on org matches (backend + frontend)
- 📋 #113 — GitHub Team branch protection (post first client)
- 📋 RFC match-lifecycle — pending Mario decisions
- 📋 RFC multi-sport-architecture — deferred, revisit when second sport is real
- 🔔 Demo with Jose (SoccerMix) — date TBD
- ⚠️ `nestor-gameon-agent` + `olga-gameon-agent` → expire May 9, 2026 — renew before

**Next session priorities (Session 32):**
1. Mario: Configurar Vercel Deploy Hook en dashboard + secret VERCEL_DEPLOY_HOOK_API
2. Mario: Deshabilitar auto-deploy en Vercel main (backend)
3. Crear dummy migration → probar pipeline completo migrate → deploy
4. Definir siguiente ciclo de features para la demo con Jose

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today.

**Demo 2:** Group of friends who organize casual football matches.

---

*Part of Orion OS v1.5.0 — updated April 27, 2026 (Session 31)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
