# GameOn — Architecture Document

> Living document. Updated by Orion at the end of each major session.
> Purpose: onboarding for new developers, agents, or anyone who needs to understand how GameOn is built.
> Operational context (sprints, issues, status) → `projects/gameon.md`
> Business ideas and roadmap → `projects/gameon-ideas.md`

---

## 1. WHAT IS GAMEON

GameOn is a **player identity platform** for amateur sports. The core differentiator is not event management — it's that every player has a persistent identity: profile, stats, FIFA-style card, history.

**Pitch:** "Replaces WhatsApp and paper for organizing matches."

### Business model
```
Free layer (social hook)
  └ Individuals create profiles, join matches, build history
  └ 1 match/day, share link with friends

Paid layer (organizations)
  └ Businesses manage matches (4/day), tournaments (2/week), leagues (1/month)
  └ Players unlock real stats inside org tournaments/leagues
  └ Orgs grow organically from communities already inside GameOn
```

---

## 2. REPOSITORIES

```
Mjosuex85/gameon-api   → Backend (NestJS)
Mjosuex85/gameon       → Frontend (Angular)
Mjosuex85/orion        → Orion OS (memory, decisions, architecture)
```

**Single issue tracker:** all issues (backend + frontend) live in `gameon-api`.

---

## 3. STACK

### Backend — `gameon-api`

| Layer | Technology |
|---|---|
| Runtime | Node.js 20 + NestJS 10 |
| ORM | TypeORM |
| Database (prod) | Neon PostgreSQL (serverless) |
| Database (local) | Docker PostgreSQL — port 5434 |
| Auth | JWT (access 15min / refresh 7d) + Google OAuth 2.0 |
| Email | Resend |
| Deploy | Vercel (serverless) via `api/index.ts` |
| Tests | Jest + ts-jest |
| Static analysis | SonarCloud |
| CI | GitHub Actions (`ci.yml`) |

### Frontend — `gameon`

| Layer | Technology |
|---|---|
| Framework | Angular 21 — 100% standalone components |
| Styles | TailwindCSS + SCSS |
| State | Angular Signals |
| HTTP | ApiService (centralized — D26) |
| Deploy | Vercel |

---

## 4. INFRASTRUCTURE & ENVIRONMENTS

```
PRODUCTION
  Frontend   →  Vercel (gameon-nu.vercel.app) — branch: main
  Backend    →  Vercel serverless — branch: main
  Database   →  Neon PostgreSQL (gameon-db)

STAGING
  Frontend   →  Vercel Preview — branch: staging
  Backend    →  Vercel Preview — branch: staging
  Database   →  Neon PostgreSQL (gameon-db-pre)

LOCAL
  Backend    →  NestJS port 3000
  Frontend   →  Angular port 4200 (--host 0.0.0.0 for ngrok)
  Database   →  Docker port 5434
```

### Deploy pipeline
```
develop  →  agents commit freely. No deploy.
    ↓ PR
staging  →  CI runs (lint + build + tests + SonarCloud). Vercel Preview auto-deploys.
    ↓ PR
main     →  CI runs. Vercel Production auto-deploys on merge.
```

### Branch protection
- `main` and `staging`: PR required before merge
- Enforcement requires GitHub Team (#113 — pending first client)
- Until then: discipline manual. Mario is the only one who merges to staging/main.

---

## 5. BACKEND ARCHITECTURE

### Module structure

```
src/
├── modules/
│   ├── auth/           → JWT + Google OAuth, login, register, refresh, password reset
│   ├── users/          → User entity, profile, roles, soft delete
│   ├── matchs/         → Match CRUD, visibility, participants, plan limits
│   ├── organizations/  → Org entity, members, roles, slug
│   └── tournaments/    → Tournament + TournamentTeam + TournamentMatch
├── common/
│   ├── constants/      → plan-limits.ts, roles enum
│   ├── guards/         → JwtAuthGuard, RolesGuard, OrganizerGuard
│   ├── decorators/     → @CurrentUser(), @Roles()
│   └── interceptors/   → error.interceptor.ts (global error handling)
├── migrations/         → TypeORM migrations (versioned, never auto-run in prod)
└── api/
    └── index.ts        → Vercel serverless entry point
```

### Key entities and relationships

```
User
  ├── has many Match (as creator)
  ├── has many MatchParticipant
  └── has many OrganizationMember

Match
  ├── belongs to User (creator)
  ├── has many MatchParticipant
  ├── belongs to Organization (optional)
  └── visibility: PRIVATE | ORGANIZATION | PUBLIC

MatchParticipant   ← own entity, NOT simple ManyToMany
  ├── belongs to Match
  ├── belongs to User
  └── position: 0-based field index

Organization
  ├── has many OrganizationMember
  ├── has many Match
  └── has many Tournament

OrganizationMember
  ├── belongs to Organization
  ├── belongs to User
  └── role: OWNER | MANAGER | STAFF

Tournament
  ├── belongs to Organization
  ├── has many TournamentTeam
  └── has many TournamentMatch
```

### Authentication flow

```
Local auth:
  POST /auth/register → creates user → sends verification email (Resend)
  POST /auth/login    → validates credentials → returns access + refresh tokens (httpOnly cookies)
  POST /auth/refresh  → validates refresh token → returns new access token

Google OAuth:
  GET /auth/google          → redirects to Google consent
  GET /auth/google/callback → Google redirects here → creates/finds user → sets cookies → redirects to frontend

Token storage:
  Both tokens in httpOnly cookies
  sameSite: 'lax' (local) | 'none' + secure: true (production)
  Access token: 15 minutes
  Refresh token: 7 days
```

### Error handling pattern (D79)

```
error.interceptor.ts  →  global errors (403, 404, 500, network)
                         shows toast automatically
                         always rethrows ORIGINAL API message

Component            →  business logic errors (rate limits, domain-specific)
                         handles its own toast
                         route must be in SILENT_URLS in the interceptor
```

### Migrations rule

- `synchronize: false` always in production
- Migrations run automatically via `migrate.yml` on merge to `main`
- Migrations run automatically via `migrate-staging.yml` on merge to `staging`
- New entities: camelCase properties MUST have explicit `name` in snake_case (D70)
- Exception: `@CreateDateColumn()`, `@UpdateDateColumn()`, `@DeleteDateColumn()` auto snake_case

---

## 6. FRONTEND ARCHITECTURE

### Structure

```
src/
├── app/
│   ├── core/
│   │   ├── services/     → ApiService, AuthService, ToastService
│   │   ├── guards/       → authGuard, organizerGuard
│   │   └── interceptors/ → auth.interceptor.ts (401 handling)
│   ├── features/
│   │   ├── auth/         → login, register, OAuth callback
│   │   ├── matches/      → match creation, detail, join
│   │   ├── organizer/    → organizer panel, dashboard, match management
│   │   └── profile/      → user profile, stats
│   └── shared/
│       └── components/   → reusable UI components
└── environments/
    ├── environment.ts          → localhost:3000
    ├── environment.staging.ts  → staging backend URL (staging branch only)
    └── environment.prod.ts     → production backend URL
```

### Key rules

- All HTTP calls go through `ApiService` (D26)
- 401 handling exclusively in `auth.interceptor.ts` (D57)
- `ChangeDetectionStrategy.OnPush` — always use enum, never numeric value
- Component size limits: HTML ≤150 lines / TS ≤200 lines / SCSS ≤20kB (D62)
- SCSS partials in `styles/` folder inside the component (D67)
- `environment.staging.ts` NEVER merged to `main`

---

## 7. CI / QUALITY PIPELINE

```
On every PR to develop, staging, or main:

1. npm ci --ignore-scripts
2. npm run lint
3. npm run build
4. npm run test:cov      → generates coverage/lcov.info
5. npm audit --audit-level=high
6. SonarCloud scan       → reads lcov.info, runs quality gate
```

### Testing coverage (backend)

```
MatchService         → 78%+ (Fase 1 — #26)
AuthService          → 97%+ (Fase 2 — #111)
OrganizationsService → 76%+ (Fase 3 — #112)
```

### SonarCloud

- Connected to `gameon-api` via GitHub Actions
- `sonar-project.properties` in repo root (develop + staging)
- `SONAR_TOKEN` in GitHub Secrets
- Quality gate: 0 new bugs, 0 new vulnerabilities, coverage ≥80% on new code

---

## 8. ROLES & PERMISSIONS

```
USER       → default. Can create matches (1/day), join matches, view public orgs.
ORGANIZER  → can create matches (4/day), tournaments (2/week), manage org.
ADMIN      → full access. Mario only. Creates organizations manually.
```

### How a user becomes ORGANIZER
- Demo phase: Mario assigns role manually in Neon + creates org via Postman
- Post-demo: Stripe payment triggers automatic role assignment
- Enterprise: manual onboarding

---

## 9. KEY ARCHITECTURAL DECISIONS

| Decision | Rule | Reference |
|---|---|---|
| MatchParticipant is own entity | NOT simple ManyToMany | D16 |
| Soft delete everywhere | `@DeleteDateColumn()` | D18 |
| No SnakeNamingStrategy | Explicit `name` on new columns | D69, D70 |
| PRIVATE visibility | Not in listings, but link is public | D71 |
| Free users can set match price | Cost-splitting tool, not monetization | D72 |
| `ignore-scripts=true` in .npmrc | Security — prevents malicious npm scripts | D73 |
| Error interceptor pattern | Two layers, never overlap | D79 |
| Migrations self-contained | Never assume seed has run | D80 |
| Orion asks before code changes | Only config/docs can be changed directly | D81 |

---

## 10. ONBOARDING — NEW DEVELOPER OR AGENT

### To understand the project
1. Read this file
2. Read `projects/gameon.md` — current sprint, open issues, business rules
3. Read `DECISIONS.md` — the why behind every architectural rule
4. Read `ORION.md` — team structure, workflow, session log

### To run locally
```powershell
# Backend
cd gameon-api
npm install
# Start Docker PostgreSQL on port 5434
npm run start:dev

# Frontend
cd gameon
npm install
ng serve --host 0.0.0.0
```

### To deploy to staging
```
1. Commit to develop
2. Open PR: develop → staging
3. CI must pass
4. Mario merges
5. Vercel deploys automatically
```

### To deploy to production
```
1. PR: staging → main
2. Title must follow: "release: vX.Y.Z — description"
3. CI must pass
4. Mario merges
5. Vercel deploys + release.yml creates GitHub Release + bumps package.json
```

---

*Part of Orion OS — built by Mario Vidal + Orion*
*Last updated: April 4, 2026 — Session 15*
