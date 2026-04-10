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

---

## BUSINESS RULES

### Plan limits (src/common/constants/plan-limits.ts)
```typescript
USER:       { matchesPerDay: 1 }
ORGANIZER:  { matchesPerDay: 4, tournamentsPerWeek: 2, leaguesPerMonth: 1 }
```

### How someone becomes ORGANIZER
- **Demo phase:** Mario assigns role manually
- **Post-demo:** payment (Stripe) triggers automatic role assignment

---

## INFRASTRUCTURE

```
PRODUCTION: v1.3.0 FULLY DEPLOYED (April 3, 2026)
  Frontend  →  Vercel (gameon-nu.vercel.app) — branch: main
  Backend   →  Vercel (serverless) — branch: main
  Database  →  Neon PostgreSQL (gameon-db)

STAGING: FULLY OPERATIONAL
  Frontend  →  Vercel Preview — branch: staging
  Backend   →  Vercel Preview — branch: staging
  Database  →  Neon PostgreSQL (gameon-db-pre) — 250 countries seeded

LOCAL:
  Backend   →  NestJS port 3000
  Frontend  →  Angular port 4200 (--host 0.0.0.0)
  Database  →  Docker PostgreSQL port 5434
```

### Deploy rules
```
develop  →  NO deploy (Ignored Build Step)
staging  →  Preview deploy (automatic on push)
main     →  Production deploy (automatic on PR merge by Mario)
```

### GitFlow
```
develop  →  free (agents commit directly)
    PR down
staging  →  CI runs. Enforcement pending #113
    PR down
main     →  CI runs. Enforcement pending #113
```

---

## STAGING VALIDATION CHECKLIST

Before merging staging → main:

```
Backend staging URL: gameon-api-git-staging-mjosuex85s-projects.vercel.app

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
```

---

## CI / QUALITY

### gameon-api CI
- Lint + Build + Tests (coverage 50% on services) + Audit (critical) + SonarCloud

### gameon CI
- Lint + Build + Tests (coverage 50% on core/) + SonarCloud

### Bruno QA
- `bruno.yml` in both repos — triggers on PR to staging/main
- ✅ on PR → Mario can merge. ❌ → fix first.

---

## TESTING STATUS

### Backend
```
MatchService          → 78%+ (complete)
AuthService           → 97%+ (complete)
OrganizationsService  → 76%+ (complete)
CI + SonarCloud       → active
```

### Frontend
```
core/ (services, interceptors, guards) → 82.88% (30/30 tests)
CI + SonarCloud → active
```

Rule: every new feature issue includes its tests.

---

## FRONTEND — ATOMIC DESIGN SYSTEM

```
src/app/shared/
  ui/
    atoms/     → button, input, modal, loader, toast
    molecules/ → stat-card, match-row, empty-state, match-filters
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
- Icons: Material Symbols via CDN

---

## STATUS — April 10, 2026 (Session 21)

**Open / Active:**
- 🔄 #120 — Redesign MatchFiltersComponent (Olga)
- 🔄 #123 — Organization-detail layout two-column (Olga)
- 📋 #138 — Lint: remove unused imports (Nestor, XS)
- 📋 #141 — npm high vulnerabilities (Nestor, S)
- 📋 #106 — Auto-assign ORGANIZER on OWNER member add (Nestor, XS)
- 📋 #113 — GitHub Team branch protection
- 📋 #117 — Multi-sport foundation
- 📋 RFC match-lifecycle — 🟡 Pendiente

**Next priorities:**
1. Normalizar line endings (git rm --cached)
2. Validar staging API (checklist arriba)
3. PR develop → staging en `gameon` (frontend)
4. Fix CI frontend si hace falta
5. Merge → test staging → PR staging → main = `release: v1.4.0`
6. Demo con Jose (SoccerMix)
7. #138 + #141 → Nestor (antes de v1.4.0)

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today.

**Demo 2:** Group of friends who organize casual football matches.

---

*Part of Orion OS v1.1.0 — updated April 10, 2026 (Session 21)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
