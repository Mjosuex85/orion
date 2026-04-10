# Template: New Project

Use this template to start any new project with Orion OS.
Full step-by-step guide: `workflows/project-init.md`.

---

## 1. Define the project

```
Project name:
What problem does it solve?
One-line pitch:
User types:
First target user:
Backend stack:
Frontend stack:
Database:
Deploy:
```

---

## 2. Mario creates repos

- [ ] Backend repo: `Mjosuex85/<project>-api` (init with README + main branch)
- [ ] Frontend repo: `Mjosuex85/<project>` (init with README + main branch)
- [ ] Create `develop` and `staging` branches in both repos

---

## 3. Orion initializes Orion OS files

Using `workflows/project-init.md` Phase 1:

- [ ] Create `projects/<project>.md` from `templates/project-context.md`
- [ ] Create `projects/<project>-decisions.md` from `templates/project-decisions.md`
- [ ] Update `ORION.md` REPOS section

---

## 4. Orion configures repos

Using `workflows/project-init.md` Phase 2:

### Backend repo
- [ ] `CLAUDE.md` from `templates/claude-md.md` (adapt for backend/Nestor)
- [ ] `.npmrc` → `ignore-scripts=true`
- [ ] `.gitattributes` → `* text=auto eol=lf`
- [ ] `.env.example` → list all required env vars
- [ ] `.github/workflows/ci.yml` from `templates/ci-backend.yml`
- [ ] `.github/workflows/bruno.yml` from `templates/bruno.yml`
- [ ] `sonar-project.properties`

### Frontend repo
- [ ] `CLAUDE.md` from `templates/claude-md.md` (adapt for frontend/Olga)
- [ ] `.npmrc` → `ignore-scripts=true`
- [ ] `.gitattributes` → `* text=auto eol=lf`
- [ ] `.github/workflows/ci.yml` from `templates/ci-frontend.yml`
- [ ] `.github/workflows/bruno.yml` from `templates/bruno.yml`
- [ ] `sonar-project.properties`

---

## 5. Mario configures platform

Using `workflows/project-init.md` Phase 3:

- [ ] Agent PATs: Read+Write on new repos + orion
- [ ] GitHub Secrets: `SONAR_TOKEN`, `GH_PAT`, `GH_PAT_CROSS_REPO`
- [ ] Deploy platform (Vercel/other): connect repos, set env vars
- [ ] Staging database provisioned
- [ ] Branch protection on `main`

---

## 6. Orion creates first issues

Using `workflows/project-init.md` Phase 4:

- [ ] Issue #1: Backend initial setup (Nestor)
- [ ] Issue #2: Frontend initial setup (Olga)

---

## 7. Validation

- [ ] Backend starts locally
- [ ] Frontend starts locally
- [ ] CI runs on first PR to `staging`
- [ ] Bruno triggers and reports
- [ ] SonarCloud receives first analysis

**Project is now operational under Orion OS.**

---

*Part of Orion OS v1.4.0 — new project with full infrastructure in under 30 minutes.*
