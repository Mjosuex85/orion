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
- `showAll=true` used by organizer panel (dashboard + matches list) to see all matches regardless of date

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

## CI / QUALITY

### gameon-api CI pipeline (ci.yml)
- Lint: `eslint` — requires `eslint-plugin-prettier` + `eslint-config-prettier` in devDependencies
- Build: `nest build`
- Tests: `jest --coverage` — coverage threshold 50% on service files only (auth, matchs, organizations)
- Audit: `npm audit --audit-level=critical --omit=dev` — high vulns exist as tracked debt (#141)
- SonarCloud: Quality Gate 80% new code — exclusions in `sonar-project.properties` cover controllers, entities, dtos, strategies, guards, decorators, migrations, seed files

### Bruno QA
- `bruno.yml` in both repos — triggers on PR to `staging` or `main`
- Backend: comments ✅ on PR if tests pass; opens issue if tests fail
- Frontend: same, but opens issue in `gameon-api` via `GH_PAT_CROSS_REPO`
- Permissions required: `contents: read`, `issues: write`, `pull-requests: write`

### Known CI issues to avoid
- Orion OS commits (`orion@orion-os.app`) break Vercel deploy and sometimes CI checkout on PRs — always do `git pull` + empty commit from Mario's machine after Orion pushes to develop
- `npm ci` requires package-lock.json in sync — any new dep must be `npm install`-ed locally and lock committed
- `collectCoverageFrom` in package.json must list only the 3 service files — not all `src/**` (inflates uncovered files)

---

## STATUS — April 9, 2026 (Session 20 close)

**Production (v1.3.0 — deployed April 3, 2026):**
- ✅ Backend + Frontend live on Vercel
- ✅ Neon PostgreSQL (gameon-db)
- ✅ Staging environment fully operational (gameon-db-pre)
- ✅ Automated migrations via `migrate.yml` + `migrate-staging.yml`
- ✅ CI pipeline fully operational — backend + frontend
- ✅ SonarCloud connected — backend + frontend
- ✅ Bruno QA active — backend (#126) + frontend (#127)
- ✅ RFC flow defined (D87) — `orion/rfcs/` created

**Closed this session (Session 20):**
- ✅ #124 — showAll param in OrgMatchFiltersDto (Nestor)
- ✅ #125 — showAll=true from OrganizerMatchesComponent (Olga)
- ✅ #126 — bruno.yml backend (Nestor)
- ✅ #127 — bruno.yml frontend (Olga)
- ✅ #129 — organizer dashboard showing 0 matches fixed (Olga)
- ✅ CI gameon-api fully green after extensive debugging:
  - eslint-plugin-prettier + eslint-config-prettier added to package.json
  - coverage scope narrowed to 3 service files
  - hasSpots test fixed (in-memory filter, not SQL)
  - audit level lowered to critical
  - sonar coverage exclusions added

**Open / Active:**
- 🔄 #120 — Redesign MatchFiltersComponent (Olga)
- 🔄 #123 — Organization-detail layout two-column (Olga)
- 📋 #138 — Lint: remove unused imports (Nestor, XS) — unblocked
- 📋 #141 — npm high vulnerabilities (Nestor, S) — before demo
- 📋 #106 — Auto-assign ORGANIZER on OWNER member add (Nestor, XS)
- 📋 #113 — GitHub Team branch protection
- 📋 #117 — Multi-sport foundation
- 📋 RFC match-lifecycle — Mario completing

**Next session priorities:**
1. PR develop → staging in `gameon` (frontend) — same flow as today
2. Fix CI frontend (same pattern: eslint, coverage, sonar)
3. Merge both to staging → test staging → PR staging → main as `release: v1.4.0`
4. Confirm demo date with Jose (SoccerMix)
5. #138 + #141 → Nestor (unblock before v1.4.0 if possible)

---

## TARGET USERS

**Demo 1 — Jose (SoccerMix):** Football community organizer in Madrid. Uses WhatsApp + paper today. Goal: collect honest feedback, not close a sale. Mario prepares account manually before demo.

**Demo 2:** Group of friends who organize casual football matches.

---

*Part of Orion OS — updated April 9, 2026 (Session 20 close)*
*Ideas and product roadmap → `projects/gameon-ideas.md`*
