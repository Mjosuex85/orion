# DECISIONS — GameOn Specific

> Decisions that apply **only to the GameOn project**.
> Universal decisions live in the root `DECISIONS.md`.
>
> Read by Orion, Nestor, and Olga when working on GameOn.
>
> ⚠️ NOTE: GameOn decisions use bare `D` prefix (legacy, predates multi-project system).
> Renaming to `GN-D` is deferred — references exist in issues and commits.
> New GameOn decisions from Session 34 onward use `GN-D` prefix.

---

## ARCHITECTURE — BACKEND

**D16. `MatchParticipant` is its own intermediate entity, not a simple ManyToMany.**

**D17. `position` in `MatchParticipant` is the 0-based index of the spot on the field.**

**D18. `@DeleteDateColumn()` for soft delete — never hard delete.**

**D19. `synchronize: false` in production. Migrations manage the schema.**

**D20. Migrations are always versioned in the repo.**

**D21. PostgreSQL for users — definitive.**

**D22. Stats will migrate to Redis in the future.**

**D23. Reviews will go to a separate microservice.**

**D24. Do not rename the `matchs` module.**

**D25. `mapToDto` exposes `avatarUrl`, `country`, `flagUrl` from creator and participants.**

**D45. With `@DeleteDateColumn`, never use `.where('deletedAt IS NULL')` manually.**

**D46. `GOOGLE_CALLBACK_URL` in the Google Strategy — never hardcoded URLs.**

**D47. `FRONTEND_URL` in the auth controller for the post-OAuth redirect.**

**D59. `password` field with `select: false` is never loaded in normal queries.**

**D69. Do NOT use SnakeNamingStrategy in TypeORM.**
Reason: Existing entities have camelCase column names in the real DB.

**D70. In new entities, every camelCase property MUST have an explicit `name` in snake_case.**
```typescript
@Column({ name: 'logo_url', nullable: true })
logoUrl?: string;
```
Exception: `@CreateDateColumn()`, `@UpdateDateColumn()`, `@DeleteDateColumn()` generate snake_case automatically.

**D71. `Match.visibility = PRIVATE` means "does not appear in public listings" — NOT "only the creator can see it".**
- `GET /matches/:id` — public, anyone with the ID can view and join
- `PRIVATE` only affects listings

**D72. Free users can set a price on their match without a paid plan.**
It is an organizational tool (splitting costs via Bizum), not GameOn monetization.

**D80. Migrations must be self-contained — never assume seed has run.**
`countriesService.seedCountries()` uses upsert — never DELETE + save.

**D86. The `Match` entity column for the match date/time is `dateTime`, NOT `scheduledAt`.**
- In QueryBuilder always use `match.dateTime`
- `scheduledAt` does not exist in the entity

---

## ARCHITECTURE — FRONTEND

**D26. All HTTP calls go through `ApiService`.**

**D27. The backend URL lives in `environment.ts` / `environment.prod.ts`.**

**D28. The project is 100% standalone components — no NgModules.**

**D29. Fallback for participants with `position == null` — assign by array order.**

**D57. 401 handling lives exclusively in `auth.interceptor.ts`.**

**D62. Angular component size limits:**
- HTML: max 150 lines / TS: max 200 lines / SCSS: max 20kB

**D67. SCSS partials for a component live in a `styles/` folder inside the component itself.**

**D79. Error handling pattern — interceptor vs component.**

```
error.interceptor.ts   →  global errors (403, 404, 500, network)
                          shows toast automatically
                          always rethrows the ORIGINAL API message

Component              →  business logic errors (rate limits, validation)
                          handles its own toast
                          must be added to SILENT_URLS in the interceptor
```

Rules:
- Specific error logic → add API route to `SILENT_URLS`
- Never read `err.error?.message` directly — always `err.message` (interceptor normalizes)
- Never show two toasts for the same error

Current SILENT_URLS:
```typescript
const SILENT_URLS = ['/auth/login', '/users/create', '/matches'];
```

---

## INFRASTRUCTURE

**D13. Commits in `gameon` repo reference issues with `Mjosuex85/gameon-api#number`.**

**D30. Production stack:**
- Frontend: Vercel (`gameon-nu.vercel.app`)
- Backend: Vercel (serverless)
- Database: Neon PostgreSQL

**D31. Autodeploy on `main` in Vercel — triggered by PR merge.**

**D32. Before any deploy PR, Orion reviews: `app.module.ts`, `main.ts`, `package.json`, env vars.**

**D33. Migrations via GitHub Actions — NOT `migrationsRun: true`.**
Vercel is serverless — TypeORM's `migrationsRun` silently fails.
Automated via `migrate.yml` (main) and `migrate-staging.yml` (staging).

**D44. Before connecting any deploy platform to `main`, verify develop and main are in sync.**

**D48. Environment variables required in production (Vercel):**
```
DATABASE_URL, JWT_ACCESS_SECRET, JWT_REFRESH_SECRET,
JWT_ACCESS_EXPIRES_IN=900, JWT_REFRESH_EXPIRES_IN=604800,
GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET,
GOOGLE_CALLBACK_URL, FRONTEND_URL, ORIGIN, NODE_ENV, RESEND_API_KEY
```

**D75. Render is NOT part of the stack.** Any reference to Render is outdated.

**D76. Local development stack:**
- Backend: NestJS port 3000
- Frontend: Angular port 4200 (`--host 0.0.0.0`)
- Database: Docker PostgreSQL port 5434

---

## TESTING

**D83. Frontend test runner: Jest + jest-preset-angular.**
- Angular 21 zoneless — `jest-preset-angular/setup-env/zoneless`
- Config: `jest.config.js` (not `.ts`)

**D84. Frontend testing priority — services before components.**

**D85. SonarCloud Quality Gate — frontend coverage 50% on new code.**
Target: raise to 80% as coverage grows.

---

## GAMEON-SPECIFIC OPERATIONAL RULES

- `environment.staging.ts` only exists in `staging` branch — never merge to `main`
- Vercel blocks deploys from commits by "Orion OS" — deploy is always the PR merge by Mario
- Orion commits break CI checkout on PRs — after Orion push, Mario does `git pull` + empty commit + push
- `collectCoverageFrom` in package.json: only the 3 service files with tests
- CI audit level: `critical` not `high` — high vulns tracked as debt (#141)
- SonarCloud coverage exclusions: controllers, entities, dtos, strategies, guards, decorators, migrations, seed files
- Line endings: LF only — `.gitattributes` enforces `eol=lf`
- `hasSpots` filter: in-memory on `matchParticipants.length` (TypeORM camelCase FK issue)
- Olga: always start with CLAUDE.md, not OLGA.md directly
- `ChangeDetectionStrategy.OnPush` — always use the enum, never the numeric value
- Issues repo: `gameon-api` (including frontend issues)

---

*Part of Orion OS v2.0.0 — updated May 4, 2026 (Session 34)*
*Legacy prefix note added. New decisions from Session 34 onward use GN-D prefix.*
