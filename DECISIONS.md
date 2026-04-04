# DECISIONS.md — GameOn + Orion OS

This file documents the technical, process, and team decisions made in the project. The **why** behind each rule. Maintained by Orion and read by the whole team.

---

## 1. TEAM AND ROLES

**D1. Mario is the sole business decision maker.**

**D2. Orion acts as technical architect and coordinator.**

**D3. Nestor executes backend only. Olga executes frontend only.**

**D4. Mario always tests before an issue is closed.**

---

## 2. WORKFLOW

**D5. All issues live in `gameon-api`, including frontend ones.**

**D6. Each issue includes: description + agent prompt + test plan.**

**D7. Maximum 2 issues In Progress simultaneously.**

**D8. When Orion says "we'll do that later", an issue is created immediately.**

**D9. When Mario proposes an idea, Orion analyzes it technically before creating the issue.**

**D52. Commits use `ref #XX` — never `closes #XX` or `fixes #XX`.**

**D53. Orion closes issues — they are never closed automatically.**

**D55. Configuration or documentation changes — Mario receives them with `git pull` after Orion pushes them.**

**D56. Mario gives the green light before Orion uses any MCP.**

---

## 3. GIT AND BRANCHES

**D10. `develop` is the active working branch. `main` is production.**

**D11. `main` is NOT touched — except in planned production deploys via PR.**

**D12. Nestor, Olga, and Mario do `git pull origin develop` before starting work.**
Reason: Orion may have pushed changes directly to GitHub (configuration, security, documentation). The pull ensures everyone starts from the real state of the repo.

**D13. Commits in the `gameon` repo reference issues with `Mjosuex85/gameon-api#number`.**

**D14. Orion can make direct changes in GitHub on `develop` for:**
- Configuration (`.npmrc`, `.env.example`, etc.)
- Documentation (`CLAUDE.md`, `AGENTS.md`, `README`)
- Urgent architecture fixes decided with Mario
- Orion OS files (`ORION.md`, `DECISIONS.md`, subagents, etc.)

For business logic or application code → issue for Nestor/Olga always.

**D44. Before connecting any deploy platform to `main`, verify that `develop` and `main` are in sync.**

**D74. Branch protection is active on `main` in `gameon` and `gameon-api`.**
- No direct push to `main` — not by Nestor, Olga, Orion, or Mario
- All production deploys happen via PR from `develop` → `main`
- Mario is the only one who approves and merges the PR
- Reason: Vercel autodeploy is connected to `main` — any push triggers a production deploy. A PR-based flow ensures deploys are intentional and reviewed.
- In emergencies: Mario temporarily disables branch protection, applies the fix, then re-enables it.

---

## 4. PRODUCTION DEPLOYS

**D49. Production deploys are done on planned dates via PR from `develop` → `main`.**

**D50. SUPERSEDED by D74. All changes to `main` go through a PR approved by Mario.**

**D51. Orion NEVER makes changes to `main` outside of a planned deploy PR.**

---

## 5. SECURITY

**D35. Never commit credentials, tokens, or secrets.**

**D36. Never use `any` in TypeScript without explicit justification.**

**D37. Never break DTO contracts the frontend already consumes.**

**D73. `ignore-scripts=true` in `.npmrc` in all projects.**
Reason: Prevents automatic execution of malicious scripts from npm packages during `npm install`.
If a package needs native build scripts, evaluate replacing it with a pure-JS alternative or explicitly justify the exception.

---

## 6. ARCHITECTURE — BACKEND

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

---

## 7. ARCHITECTURE — FRONTEND

**D26. All HTTP calls go through `ApiService`.**

**D27. The backend URL lives in `environment.ts` / `environment.prod.ts`.**

**D28. The project is 100% standalone components — no NgModules.**

**D29. Fallback for participants with `position == null` — assign by array order.**

**D57. 401 handling lives exclusively in `auth.interceptor.ts`.**

**D62. Angular component size limits:**
- HTML: max 150 lines / TS: max 200 lines / SCSS: max 20kB

**D67. SCSS partials for a component live in a `styles/` folder inside the component itself.**

**D79. Error handling pattern — interceptor vs component.**

There are two layers of error handling. They must never overlap.

```
error.interceptor.ts   →  global errors (403, 404, 500, network)
                          shows toast automatically
                          always rethrows the ORIGINAL API message

Component              →  business logic errors (rate limits, validation,
                          domain-specific feedback)
                          handles its own toast
                          must be added to SILENT_URLS in the interceptor
```

**Rule for every new component:**
- If the component has specific `if (msg.includes(...))` error logic → add its API route to `SILENT_URLS` in `error.interceptor.ts`
- Never read `err.error?.message` directly — always read `err.message` (the interceptor normalizes this)
- Never show two toasts for the same error (one from interceptor + one from component)

**Why the interceptor always rethrows the original API message:**
Components need to match on the real backend string (e.g. `'only create 4 matches'`). If the interceptor rethrows a translated/generic message, component logic breaks silently.

**i18n compatibility:**
This architecture is i18n-ready. When ngx-translate is introduced:
- Interceptor: `toastService.showError(translate.instant('errors.' + statusCode))`
- Components: `if (msg.includes('...')) toastService.showError(translate.instant('errors.match_limit'))`
All message decisions are already centralized — no scattered hardcoded strings to hunt down.

**Current SILENT_URLS** (in `error.interceptor.ts`):
```typescript
const SILENT_URLS = [
  '/auth/login',
  '/users/create',
  '/matches',  // match-create + organizer-match-create
];
```
Update this list whenever a new component with specific error handling is created.

---

## 8. INFRASTRUCTURE & DEPLOY

**D30. Production stack:**
- Frontend: Vercel (`gameon-nu.vercel.app`)
- Backend API: Vercel (serverless)
- Database: Neon PostgreSQL

**D75. Render is NOT part of the stack.** Any reference to Render in documentation or code is outdated and must be corrected.

**D31. Autodeploy activated on `main` in Vercel — triggered by PR merge.**

**D32. Before any deploy PR, Orion reviews: `app.module.ts`, `main.ts`, `package.json`, auth strategies and environment variables.**

**D33. ~~`migrationsRun: true` in production~~ — CORRECTED.**
Vercel is serverless — there is no persistent startup process. `migrationsRun: true` silently does nothing because TypeORM cannot resolve the migrations path in the Vercel bundle.
**Migrations MUST be run manually from local before every deploy that includes schema changes:**
```powershell
npm run build
$env:DATABASE_URL="neon_connection_string"
npm run db:migrate
$env:DATABASE_URL=""
```
Automation via GitHub Actions is planned in issue #90 (post-demo).

**D48. Environment variables required in production (Vercel):**
```
DATABASE_URL
JWT_ACCESS_SECRET, JWT_REFRESH_SECRET
JWT_ACCESS_EXPIRES_IN=900, JWT_REFRESH_EXPIRES_IN=604800
GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET
GOOGLE_CALLBACK_URL, FRONTEND_URL, ORIGIN, NODE_ENV, RESEND_API_KEY
```

**D76. Local development stack:**
- Backend: NestJS on port 3000
- Frontend: Angular on port 4200 (`--host 0.0.0.0` for mobile testing via ngrok)
- Database: Docker PostgreSQL on port 5434
- `environment.ts` → `localhost:3000`
- `environment.prod.ts` → Vercel backend URL

---

## 9. TOOLS AND MCPS

**D38. GitHub MCP connected to Claude.**

**D39. Local PostgreSQL MCP — pending (#31). Notion MCP — pending (#33).**

**D68. Nestor and Olga use the GitHub MCP to read the assigned issue body only — comments are not accessible via MCP.**
Orion leaves corrections in the issue body, never in comments.

**D82. GitHub MCP — tool usage rules for Orion.**

Each tool has a single purpose. Never mix them:

| Tool | Use for |
|------|---------|
| `github:create_or_update_file` | Create or edit files in a repo filesystem |
| `github:update_issue` | Edit the body, title, or state of an existing issue |
| `github:create_issue` | Create a new issue |
| `github:push_files` | Push multiple files in a single commit |

`create_or_update_file` with an issue URL as path creates a literal file in the repo — it does NOT edit the issue. Always use `update_issue` to modify issue content.

---

## 10. AGENTS AND MODELS

**D54. Scale from Sonnet 4.6 to Opus 4.6 when:**
- The codebase exceeds 500k tokens
- Sonnet cannot resolve complex architecture after two attempts

**D58. Nestor and Olga must use Sonnet 4.6 minimum.**

**D63. Token strategy: Copilot/Gemini for S/M tasks, Claude CLI for L/XL.**

**D64. The value of the system is in the context, not the model.**

**D66. Model selection by task complexity:**

| Task | Minimum model |
|------|---------------|
| Mechanical refactor with defined steps | Gemini Flash / Haiku |
| Bug analysis + diagnosis | Gemini Pro / Sonnet |
| Architecture, new features | Gemini Pro / Sonnet |
| Global architecture decisions | Claude Opus / Orion |

---

## 11. FUTURE

**D40. Orion as multi-project architect.**

**D41. Kubernetes when GameOn has 500+ active users or hosting > $50/month.**

**D43. GitHub Actions CI/CD + SonarCloud — after the demo.**

---

## 12. ORION OS

**D60. ORION.md lives in `Mjosuex85/orion` (main) — not in `gameon-api`.**

**D61. CLAUDE.md in each repo = how to work (≤150 lines). Business Rules in `orion/projects/gameon.md`.**

**D65. Subagents live in `orion/agents/subagents/` — reusable across projects.**

---

## 13. ENGINEERING PRINCIPLES — ORION OS UNIVERSAL

**D77. Engineering principles — apply to every project under Orion OS.**

These are not rules for a specific stack. They are the foundation of how we build — for GameOn today, and for every future project.

① **Measure before optimizing.**
Never optimize without data. A perceived bottleneck is an opinion. A measured bottleneck is a fact. Profiling comes before any performance work.

② **Never guess the bottleneck.**
Assumptions about where the problem is are almost always wrong. Instrument, observe, then act.

③ **Avoid unnecessary complexity.**
A complex solution is a liability. If a simple approach solves the problem, the complex one is wrong — even if it's more elegant. Complexity has a maintenance cost that compounds over time.

④ **Simple things fail less.**
The fewer moving parts, the fewer failure modes. This applies to architecture, to code, and to process. When in doubt, choose the option with fewer dependencies.

⑤ **Data matters more than the algorithm.**
The right data model beats a clever algorithm every time. A well-structured schema, a well-designed DTO, a clear entity relationship — these age well. Clever logic written on top of a poor data model creates permanent debt.

---

## 14. DECISION FRAMEWORK — ORION OS UNIVERSAL

**D78. Orion always evaluates every technical decision through two lenses simultaneously.**

This is not optional — it is the default operating mode for every proposal, every issue, every architectural choice.

### Lens 1 — Scalability & technical health

Before proposing or implementing anything, Orion asks:
- Does this decision create technical debt? Is that debt justified now?
- Will this hold at 10x the current load / users / data?
- Does this couple things that should be independent?
- Is there a simpler data model that makes this naturally scalable?
- Are we building on a foundation that will need to be torn down later?

If the answer creates significant future cost → document the debt explicitly as an issue. Never leave invisible debt.

### Lens 2 — Demo / release constraints

Before proposing or implementing anything, Orion also asks:
- Is there a hard deadline (demo, release, client meeting)?
- Does the ideal scalable solution block shipping on time?\
- What is the minimum viable version that is not a lie — i.e. works correctly, does not create security holes, and can be replaced cleanly later?
- Is the shortcut taken now documented so it gets fixed?

If there is a deadline conflict → propose both options explicitly:
  - **Option A (scalable):** what it requires, estimated effort, when to do it
  - **Option B (demo-safe):** what corners it cuts, what debt it creates, issue created immediately

Mario decides. Orion never makes that tradeoff silently.

### The rule

```
Scalable by default.
Pragmatic when there is a real deadline.
Never silent about the tradeoff.
```

A shortcut taken consciously and documented is acceptable.
A shortcut taken silently is a bug waiting to happen.

---

## 15. TESTING — FRONTEND

**D83. Frontend test runner: Jest + jest-preset-angular.**
- Consistent with backend (Jest)
- Angular 21 zoneless — setup uses `jest-preset-angular/setup-env/zoneless`
- Config: `jest.config.js` (not `.ts` — avoids `ts-node` dependency in CI)
- Scripts: `test`, `test:cov`, `test:watch`

**D84. Frontend testing priority — services before components.**
Same philosophy as backend: test business logic first (services, interceptors, guards).
Components are tested only when they contain non-trivial logic.

**D85. SonarCloud Quality Gate — frontend coverage threshold.**
- Current threshold: **50% on new code** (set Session 17)
- Target: raise to 80% as test coverage grows with the product
- Reason for 50%: first CI setup — tests cover only `core/` (services, interceptors, guards). Features and UI components have no tests yet.
- This is a conscious tradeoff, not a permanent standard. Coverage grows organically with every new feature issue.

---

*Last updated: April 4, 2026 — Session 17*
*New decisions: D83, D84, D85 (frontend testing + SonarCloud Quality Gate)*
