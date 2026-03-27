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
Deploy:     Vercel (serverless)
DB:         Neon PostgreSQL (production)
DB local:   Docker port 5434
```

### Frontend — `Mjosuex85/gameon`
```
Framework:  Angular 21 (100% standalone components)
Styles:     TailwindCSS + SCSS
State:      Angular Signals
Deploy:     Vercel
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
- `POST /organizations` requires ADMIN

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

---

## TECHNICAL RULES

- All issues live in `gameon-api` — including frontend ones
- Module name is `matchs` (with s) — do not rename
- `MatchParticipant` is its own entity — NOT simple ManyToMany
- No SnakeNamingStrategy — new entities must use explicit `name` in snake_case on camelCase columns (D69, D70)
- `@CreateDateColumn()`, `@UpdateDateColumn()`, `@DeleteDateColumn()` generate snake_case automatically
- Migrations always versioned in repo. Never rely on `synchronize: true`
- Migration command (PowerShell):
  ```powershell
  $env:DATABASE_URL="your_neon_url"; npm run db:migrate
  ```

---

## STATUS — March 27, 2026

**Production:**
- ✅ Backend in production (Vercel)
- ✅ Frontend in production (Vercel)
- ✅ Resend working (email)

**Recently closed:**
- ✅ #61 — isLoading signal
- ✅ #64 — admin.component.scss
- ✅ #73 — admin matches pagination
- ✅ #75 — ORGANIZER role + Match visibility + PLAN_LIMITS
- ✅ #76 — Organizations module
- ✅ #77 — Match visibility enforcement
- ✅ #78 — Tournaments module

**Open — backend:**
- 🔴 #66 — Google OAuth refresh token fails in production

**Open — frontend:**
- 🔴 #74 — Google OAuth opens popup instead of redirecting
- 🔴 #79 — Two match creation flows: free (simple) vs organizer (full)

**Open — pending definition:**
- 📋 #71 — profile.component.scss over budget
- 💻 #72 — componentize admin HTML (1019 lines)
- 📊 #70 — design system GameOn

---

## TARGET USERS

**Demo 1:** Football community organizer in Madrid. Uses WhatsApp + paper today. GameOn replaces that with organizations, public matches, tournaments.

**Demo 2:** Group of friends who organize casual football matches. Need: create match, share link, friends join, split cost via Bizum.

---

*Part of Orion OS — updated March 27, 2026*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
