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
PRODUCTION: v1.4.0 (API) + v1.5.1 (Frontend) — DEPLOYED
  Frontend  →  Vercel (gameon-nu.vercel.app) — branch: main — v1.5.1
  Backend   →  Vercel (serverless) — branch: main — v1.4.0
  Database  →  Neon PostgreSQL (gameon-db)

STAGING: OPERATIONAL — updated Session 29
  Frontend  →  Vercel Preview — branch: staging (PR #17 merged)
  Backend   →  Vercel Preview — branch: staging (PR #157 merged)
  Database  →  Neon PostgreSQL (gameon-db-pre) — migrations run manually Session 29
```

### Deploy rules
```
develop  →  NO deploy (Ignored Build Step)
staging  →  Preview deploy (automatic on push)
main     →  Production deploy (automatic on PR merge by Mario)
```

### Migration protocol (updated Session 29)
```
BEFORE merging to staging:
  1. GitHub Actions → "Migrate Staging DB" → Run workflow (workflow_dispatch)
  2. Uses secret DATABASE_URL_STAGING → gameon-db-pre

BEFORE merging to main:
  1. GitHub Actions → "Migrate Production DB" → Run workflow (workflow_dispatch)
  2. Uses secret DATABASE_URL → gameon-db

NOTE: workflow_dispatch only shows "Run workflow" button when workflow exists in main branch.
Both workflows updated to workflow_dispatch in Session 29.
```

### GitFlow
```
develop  →  free (agents commit directly)
    PR down
staging  →  CI runs + Quality Gate enforced
    PR down
main     →  CI runs + Quality Gate enforced
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

✅ Organizer panel
  Dashboard stats show real match counts (not 0)
  Match list shows all matches (showAll=true working)

✅ Public Profile + Privacy (#7 + #155 + #156)
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
- SonarQube for IDE mandatory for Nestor before every commit (Session 29)

### gameon CI
- Lint + Build + Tests + SonarCloud
- **Quality Gate blocking: ACTIVE**
- SonarQube MCP mandatory for Olga before every commit (Session 29)
- Automatic Analysis: DISABLED — analysis runs via GitHub Actions only

### Bruno QA
- `bruno.yml` in both repos — triggers on PR to staging/main
- ✅ on PR → Mario can merge. ❌ → fix first.

### SonarCloud — 3-layer strategy (D92)
```
Layer 1 — IDE:     Nestor (VSCode SonarQube) + Olga (Antigravity SonarQube MCP) ✅ mandatory Session 29
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
AuthService    → refreshUserProfile (3 new tests, Session 29)
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

## STATUS — April 25, 2026 (Session 29)

**Production:**
- API: v1.4.0 ✅ (gameon-api — deployed April 11)
- Frontend: v1.5.1 ✅ (gameon — deployed April 23, PR #16)

**Staging (updated this session):**
- Frontend: PR #17 merged → #155 + #156 + Sonar fixes + SCSS toggle fix (develop)
- Backend: PR #157 merged → #7 + ER diagram docs + Sonar fixes
- Migrations: `AddUsernameToUsers` + `AddIsPublicToUserProfiles` run manually on gameon-db-pre ✅

**Completed this session (Session 29):**
- ✅ #155 + #156 → staging (frontend PR #17)
- ✅ #7 → staging (backend PR #157)
- ✅ Sonar fixes: accessibility (a11y), coverage, duplicate imports, error handlers
- ✅ SonarQube mandatory pre-commit protocol added to CLAUDE.md (Olga + Nestor)
- ✅ agent-log.yml fix: contents:write permission in gameon frontend
- ✅ migrate workflows → workflow_dispatch (run before merge, not after)
- ✅ GitHub Secrets clarified: DATABASE_URL (prod) + DATABASE_URL_STAGING (staging)
- ✅ SCSS toggle fix: `.slider.checked` class instead of `input:checked + .slider`

**Open / Pending:**
- 🟡 SCSS toggle fix → still in develop, needs PR to staging (small, next session)
- 🟡 staging → main both repos (pending full staging validation)
- 🟡 Migrate Production DB → run before main merge (gameon-db)
- 📋 #113 — GitHub Team branch protection (post first client)
- 📋 #117 — Multi-sport foundation (backlog)
- 📋 RFC match-lifecycle — 🟡 Pending
- 🔔 Demo with Jose (SoccerMix) — date TBD
- ⚠️ `nestor-gameon-agent` + `olga-gameon-agent` → expire May 9, 2026 — renew before

**Next priorities:**
1. PR develop → staging (SCSS toggle fix)
2. Full staging validation checklist
3. Run Migrate Staging DB workflow → confirm OK
4. PR staging → main (both repos) + Migrate Production DB
5. Define next feature cycle

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today.

**Demo 2:** Group of friends who organize casual football matches.

---

*Part of Orion OS v1.5.0 — updated April 25, 2026 (Session 29)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
