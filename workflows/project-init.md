# Project Initialization — Orion OS

> Step-by-step guide for Orion to initialize a new project.
> Estimated time: 15-30 minutes for the Orion OS setup.
> Mario handles repo creation and deploy platform configuration separately.

---

## PREREQUISITES

Before starting, Mario must have:
- [ ] Created the backend repo: `Mjosuex85/<project>-api`
- [ ] Created the frontend repo: `Mjosuex85/<project>`
- [ ] Both repos initialized with `main` branch and a basic README

---

## PHASE 1 — Orion OS Files (Orion does this)

### 1.1 Create project context
```
Copy templates/project-context.md → projects/<project>.md
Fill in: project name, pitch, stack, target users
```

### 1.2 Create project decisions
```
Copy templates/project-decisions.md → projects/<project>-decisions.md
Fill in: initial architecture decisions specific to this project
```

### 1.3 Update ORION.md
```
Add project to REPOS section
If this becomes the active project, update session start protocol
```

---

## PHASE 2 — Repo Configuration (Orion pushes to both repos)

### 2.1 Backend repo (`<project>-api`)

Push these files to `develop` branch:

```
CLAUDE.md                       → from templates/claude-md.md (adapt for backend)
.npmrc                          → ignore-scripts=true
.gitattributes                  → * text=auto eol=lf
.env.example                    → list all required env vars
.github/workflows/ci.yml        → from templates/ci-backend.yml (adapt)
.github/workflows/bruno.yml     → from templates/bruno.yml (adapt)
sonar-project.properties        → configure for this project
```

### 2.2 Frontend repo (`<project>`)

Push these files to `develop` branch:

```
CLAUDE.md                       → from templates/claude-md.md (adapt for frontend)
.npmrc                          → ignore-scripts=true
.gitattributes                  → * text=auto eol=lf
.github/workflows/ci.yml        → from templates/ci-frontend.yml (adapt)
.github/workflows/bruno.yml     → from templates/bruno.yml (adapt)
sonar-project.properties        → configure for this project
```

---

## PHASE 3 — Agent Configuration (Mario does this)

### 3.1 Token access
- [ ] Verify Nestor's PAT has Read+Write on new repos + orion
- [ ] Verify Olga's PAT has Read+Write on new repos + orion
- [ ] Add GitHub Secrets to both repos: `SONAR_TOKEN`, `GH_PAT`, `GH_PAT_CROSS_REPO`

### 3.2 Deploy platform
- [ ] Connect repos to Vercel (or deploy platform of choice)
- [ ] Configure environment variables in deploy platform
- [ ] Set up staging database (if applicable)
- [ ] Verify: `develop` = no deploy, `staging` = preview, `main` = production

### 3.3 Branch protection
- [ ] Protect `main` branch: no direct push, require PR
- [ ] Mario is sole merge approver

---

## PHASE 4 — First Issue (Orion creates)

Orion creates issue #1 in `<project>-api`:

```markdown
## Context
Initial project setup — base configuration, folder structure, first endpoint/component.
This is the foundation everything else builds on.

## Prompt for NESTOR
1. Initialize NestJS project (if not done) with:
   - TypeORM + PostgreSQL configuration
   - JWT auth module (if applicable)
   - Health check endpoint: GET /health → { status: 'ok', timestamp: ... }
2. Verify: `npm run build` succeeds
3. Verify: `npm run test` runs (even if 0 tests)
4. Document all env vars in .env.example

## Skills to inject
- skills/universal/git-flow.md
- skills/backend/nestjs-patterns.md

## Test plan for the Director
1. `npm run start:dev` → server starts on port 3000
2. GET http://localhost:3000/health → returns 200 with status ok
3. `npm run build` → no errors
4. .env.example lists all required variables

Expected commit: [NESTOR] feat(setup): initial project configuration ref #1 | size: M
```

---

## PHASE 5 — Validation

After Phase 4 is complete:
- [ ] Backend starts locally
- [ ] Frontend starts locally
- [ ] CI runs on a PR to `staging`
- [ ] Bruno triggers and reports
- [ ] SonarCloud receives first analysis

Once validated: the project is operational under Orion OS.

---

*Part of Orion OS v1.4.0 — new project in under 30 minutes*
