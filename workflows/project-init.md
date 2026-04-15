# Project Initialization — Orion OS

> Step-by-step guide for Orion to initialize a new project.
> Estimated time: 15-30 minutes for the Orion OS setup.
> Mario handles repo creation and deploy platform configuration separately.

---

## STEP 0 — Choose project topology

Before anything, Mario and Orion decide:

### Topology A — Polyrepo (default, like GameOn)
```
Mjosuex85/<project>-api   → backend repo
Mjosuex85/<project>       → frontend repo
```
When to use: backend and frontend have independent deploy cycles, different agents, or the project is expected to scale.

### Topology B — Monorepo (like NutriApp)
```
Mjosuex85/<project>
  api/   → backend (NestJS, Express, or none)
  app/   → frontend (React, Angular, Vue, etc.)
```
When to use: personal tools, small products, single developer, or backend is minimal/absent (Firebase/Supabase SDK direct).

---

## STEP 1 — Choose stack

Document in `projects/<project>-decisions.md`:

```
Frontend framework: [ Angular | React | Vue | Svelte ]
Backend:            [ NestJS | Express | None (Firebase/Supabase direct) ]
Database:           [ PostgreSQL | Firebase Firestore | Supabase | SQLite ]
Auth:               [ JWT | Firebase Auth | Supabase Auth | None ]
Deploy:             [ Vercel | Firebase Hosting | Railway | Other ]
```

---

## STEP 2 — Prerequisites (Mario does this)

**Polyrepo:**
- [ ] Create `Mjosuex85/<project>-api` with main branch + README
- [ ] Create `Mjosuex85/<project>` with main branch + README

**Monorepo:**
- [ ] Create `Mjosuex85/<project>` with main branch + README
- [ ] Confirm folder structure: `api/` and `app/` inside repo

---

## STEP 3 — Orion OS files (Orion does this)

### 3.1 Create project context
```
Copy templates/project-context.md → projects/<project>.md
Fill in: name, pitch, stack, topology, target users
Declare skills array based on chosen stack
```

### 3.2 Create project decisions
```
Copy templates/project-decisions.md → projects/<project>-decisions.md
Fill in: initial architecture decisions
```

### 3.3 Update ORION.md
```
Add project to REPOS section
Add to active projects list in session start protocol
```

---

## STEP 4 — Repo configuration (Orion pushes)

### Polyrepo — backend repo (`<project>-api`)
```
CLAUDE.md               → adapted for backend + chosen stack
.npmrc                  → ignore-scripts=true
.gitattributes          → * text=auto eol=lf
.env.example            → all required env vars
.github/workflows/ci.yml
sonar-project.properties
```

### Polyrepo — frontend repo (`<project>`)
```
CLAUDE.md               → adapted for frontend + chosen framework
.npmrc                  → ignore-scripts=true
.gitattributes          → * text=auto eol=lf
.github/workflows/ci.yml
sonar-project.properties
```

### Monorepo — single repo (`<project>`)
```
CLAUDE.md               → covers both api/ and app/ with their stacks
.npmrc                  → ignore-scripts=true (root)
.gitattributes          → * text=auto eol=lf
api/.env.example        → backend env vars (if api exists)
.github/workflows/ci.yml → runs both api and app checks
sonar-project.properties
```

---

## STEP 5 — Agent configuration (Mario does this)

- [ ] Verify Nestor PAT has Read+Write on new repos + orion
- [ ] Verify Olga/React agent PAT has Read+Write on new repos + orion
- [ ] Add GitHub Secrets: `SONAR_TOKEN`, `GH_PAT`
- [ ] Connect repo to deploy platform
- [ ] Configure environment variables
- [ ] Branch protection on `main`

---

## STEP 6 — First issue (Orion creates)

Create issue #1 in the primary repo:
- Backend: health check endpoint + project scaffold
- Frontend-only (Firebase/Supabase): project scaffold + Firebase init + first working route

Use `templates/issue-template.md`. Score ≥ 8 before assigning.

---

## STEP 7 — Validation

- [ ] App starts locally
- [ ] CI runs on a PR to `staging`
- [ ] SonarCloud receives first analysis

Once validated: project is operational under Orion OS.

---

*Part of Orion OS v1.5.0 — monorepo + polyrepo + multi-framework support*
