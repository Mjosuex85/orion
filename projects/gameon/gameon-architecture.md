# GameOn — Architecture Document

> Living document. Updated by Orion at the end of each major session.
> Purpose: onboarding for new developers, agents, or anyone who needs to understand how GameOn is built.
> Operational context (sprints, issues, status) → `projects/gameon/gameon.md`
> Business ideas and roadmap → `projects/gameon/gameon-ideas.md`

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

---

## 6. FRONTEND ARCHITECTURE

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

---

*Part of Orion OS — built by Mario Vidal + Orion*
*Last updated: Session 36 (modular folder structure)*
