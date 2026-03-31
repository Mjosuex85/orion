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
- **Migrations MUST be run manually before every production deploy** (Vercel serverless — migrationsRun does not work)
- Migration command (PowerShell):
  ```powershell
  npm run build
  $env:DATABASE_URL="your_neon_url"
  npm run db:migrate
  $env:DATABASE_URL=""
  ```
- Automated migrations via GH Actions planned in issue #90 (post-demo)

---

## STATUS — March 31, 2026 (v1.2.0)

**Production:**
- ✅ Backend live on Vercel (serverless)
- ✅ Frontend live on Vercel (gameon-nu.vercel.app)
- ✅ Neon PostgreSQL — schema updated with 8 migrations from v1.2.0
- ✅ Resend working (email)

**Closed this cycle (#74–#87):**
- ✅ #74 — Google OAuth redirect fix
- ✅ #71 — SCSS budget resolved
- ✅ #80 — admin SCSS consolidated
- ✅ #82 — public organization pages
- ✅ #83 — GET /organizations/my endpoint
- ✅ #84 — two match creation flows (free vs organizer)
- ✅ #86, #87 — match DTO bug fixes

**Sprint 1 — Demo with Jose (SoccerMix) — ACTIVE:**
- 🔴 #96 — Organizer panel: dashboard + matches table + players + create match (Olga, DEMO BLOCKER)
- 🟡 #95 — CHANGELOG.md (Nestor, this session)

**Open — post-demo:**
- 📋 #97 — Match templates (Nestor + Olga)
- 📋 #85 — Organizer match creation form backend (Nestor)
- 📋 #91 — CI backend workflow (Nestor)
- 📋 #92 — CI frontend workflow (Olga)
- 📋 #93 — SonarCloud integration
- 📋 #94 — Sprint system + staging environment
- 📋 #90 — Automated migrations via GH Actions
- 📋 #35 — Initial proper migration (replace synchronize:true history)
- 📋 #36 — Email provider integration
- 📋 #6  — Roles system refinement

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today. Goal: collect honest feedback, not close a sale. Mario prepares account manually before demo.

**Demo 2:** Group of friends who organize casual football matches. Need: create match, share link, friends join, split cost via Bizum.

---

*Part of Orion OS — updated March 31, 2026 (Session 11)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
