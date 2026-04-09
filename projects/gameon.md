# GameOn — Project Context

> Technical context for Orion, Nestor and Olga.
> Business vision and ideas live in `projects/gameon-ideas.md`.

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
- `PRIVATE` = not in public listings, but accessible to anyone with the direct link (D71)
- `GET /matches` requires auth, returns only creator's matches
- `GET /matches/:id` is public — anyone with the ID can view and join
- Free users: 1 match/day. Organizers: 4 matches/day (PLAN_LIMITS)
- Free users can set a price — it's an organizational tool (Bizum), not monetization (D72)
- `organizationId` optional — links match to an organization if visibility = ORGANIZATION

### Organizations
- Entity with `name`, `slug`, `logoUrl`, `description`, `plan` (FREE/PRO)
- Member roles: `OWNER` | `MANAGER` | `STAFF`
- OWNER created automatically when organization is created
- Public endpoints: `GET /organizations`, `GET /organizations/:slug`, `GET /organizations/:id/matches`
- `GET /organizations/my` — returns the organization where the authenticated user is OWNER
- `POST /organizations` requires ADMIN — manual creation by Mario for early clients
- `GET /organizations/:id/matches` — default shows only today's matches; `?showAll=true` for all

### Tournaments
- Belongs to an Organization
- `TournamentTeam`: name + captain (players added later)
- `TournamentMatch`: separate entity with scores, round, scheduledAt
- Limit: 2 tournaments/week per organization
- Public: listing and detail
- GameMode reused from Match entity

### Leagues
- Planned. Same model as tournaments but points-based. Issue pending.

---

## BUSINESS RULES

### Match visibility
- `PRIVATE`: only appears in creator's list. Anyone with the link can view and join.
- `ORGANIZATION`: appears in `GET /organizations/:id/matches`. Public.
- `PUBLIC`: future — visible to everyone without a link.

### Plan limits (src/common/constants/plan-limits.ts)
```typescript
USER:       { matchesPerDay: 1 }
ORGANIZER:  { matchesPerDay: 4, tournamentsPerWeek: 2, leaguesPerMonth: 1 }
```

### Pricing
- Free users can set a price on their match (tool for splitting costs via Bizum)
- No commission for GameOn on free matches
- Price restriction for free users revisited when Pro Free plan is defined

### How someone becomes ORGANIZER
- **Demo phase:** Mario assigns role manually in Neon + creates org via Postman
- **Post-demo target:** payment (Stripe) triggers automatic role assignment
- **Enterprise clients:** manual onboarding via phone/contact

---

## TECHNICAL RULES

- All issues live in `gameon-api` — including frontend ones
- Module name is `matchs` (with s) — do not rename
- `MatchParticipant` is its own entity — NOT simple ManyToMany
- No SnakeNamingStrategy — new entities must use explicit `name` in snake_case on camelCase columns (D69, D70)
- `@CreateDateColumn()`, `@UpdateDateColumn()`, `@DeleteDateColumn()` generate snake_case automatically
- `synchronize: false` always — migrations control the schema
- Match date/time column in entity is `dateTime`, NOT `scheduledAt` (D86)
- **Migrations run automatically via `migrate.yml` on merge to `main` and `migrate-staging.yml` on merge to `staging`**
- Migration command if manual run needed (PowerShell):
  ```powershell
  npm run build
  $env:DATABASE_URL="your_neon_url"
  npm run db:migrate
  $env:DATABASE_URL=""
  ```

---

## STATUS — April 8, 2026 (v1.3.0)

**Production (v1.3.0 — deployed April 3, 2026):**
- ✅ Backend live on Vercel (serverless)
- ✅ Frontend live on Vercel (gameon-nu.vercel.app)
- ✅ Neon PostgreSQL (gameon-db)
- ✅ Staging environment fully operational (gameon-db-pre)
- ✅ Automated migrations via `migrate.yml` + `migrate-staging.yml`
- ✅ CI pipeline (build + tests) on every PR — backend + frontend
- ✅ SonarCloud connected — backend + frontend (Quality Gate 50% new code)
- ✅ Semantic versioning via `release.yml`

**Closed since Session 12 (April 1 → April 8, 2026):**
- ✅ #96  — Organizer panel (dashboard + matches + create match)
- ✅ #100, #103, #104, #105 — UX + payment method selection + venue management
- ✅ #110 — Fix límite partidos por organización
- ✅ #26  — Jest MatchService tests (78%+)
- ✅ #111 — AuthService tests (97%+, 20/20)
- ✅ #112 — OrganizationsService tests (76%+, 13/13)
- ✅ #91  — CI backend workflow
- ✅ #93  — SonarCloud backend
- ✅ #116 — Jest frontend: 30/30 tests, 82.88% cobertura (AuthService, ErrorInterceptor, Guards)
- ✅ #92  — CI frontend workflow + SonarCloud
- ✅ #115 — Atomic Design refactor (atoms modernized + molecules created)
- ✅ #69, #72 — Admin issues obsoletos cerrados
- ✅ #102 — Filtros en GET /organizations/:id/matches (backend + MatchFiltersComponent frontend)
- ✅ #119 — priceMin/priceMax filters on organization matches
- ✅ #121 — Default dateFrom=hoy + orderBy dateTime ASC
- ✅ #122 — Fix dateTo=hoy para mostrar solo partidos del día

**Active now (Session 20 — April 8, 2026):**
- 🔄 #120 — Redesign MatchFiltersComponent (Olga)
- 🔄 #123 — Organization-detail layout two-column desktop (Olga)
- 🔄 #124 — showAll param en OrgMatchFiltersDto (Nestor)
- 🔄 #125 — Pasar showAll=true desde OrganizerMatchesComponent (Olga, depende de #124)

**Open backlog:**
- 📋 #113 — GitHub Team branch protection (pending first paying client)
- 📋 #117 — Arquitectura multi-deporte: campo `sport` en Match
- 📋 #118 — Posiciones nombradas en pizarra: catálogo 19 posiciones
- 📋 #106 — Assign OWNER in org should auto-set UserRole to ORGANIZER (XS)

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today. Goal: collect honest feedback, not close a sale. Mario prepares account manually before demo. Date pending confirmation.

**Demo 2:** Group of friends who organize casual football matches. Need: create match, share link, friends join, split cost via Bizum.

---

*Part of Orion OS — updated April 8, 2026 (Session 20)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
