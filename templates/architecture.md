# [PROJECT NAME] — Architecture Document

> Template for Orion OS. Copy to `projects/[project]-architecture.md` for each new project.
> Living document — updated by Orion at the end of each major session.
> Purpose: onboarding for new developers, agents, or AI instances.

---

## 1. WHAT IS [PROJECT NAME]

[One paragraph: what it does, who it's for, what makes it different.]

**Pitch:** "[One sentence that explains the value to a non-technical person.]"

### Business model
```
[Free/paid tiers, monetization logic, user types]
```

---

## 2. REPOSITORIES

```
[owner]/[repo-backend]    → Backend
[owner]/[repo-frontend]   → Frontend
[owner]/orion             → Orion OS
```

**Issue tracker:** [which repo holds all issues, and why]

---

## 3. STACK

### Backend

| Layer | Technology |
|---|---|
| Runtime | |
| Framework | |
| ORM / DB access | |
| Database (prod) | |
| Database (local) | |
| Auth | |
| Email | |
| Deploy | |
| Tests | |
| Static analysis | |
| CI | |

### Frontend

| Layer | Technology |
|---|---|
| Framework | |
| Styles | |
| State | |
| HTTP client | |
| Deploy | |

---

## 4. INFRASTRUCTURE & ENVIRONMENTS

```
PRODUCTION
  Frontend   →  [URL] — branch: main
  Backend    →  [URL] — branch: main
  Database   →  [provider + db name]

STAGING
  Frontend   →  [URL] — branch: staging
  Backend    →  [URL] — branch: staging
  Database   →  [provider + db name]

LOCAL
  Backend    →  port [X]
  Frontend   →  port [X]
  Database   →  [provider] port [X]
```

### Deploy pipeline
```
develop  →  [what happens on push]
    ↓ PR
staging  →  [what happens on PR merge]
    ↓ PR
main     →  [what happens on PR merge]
```

---

## 5. BACKEND ARCHITECTURE

### Module structure

```
src/
├── modules/
│   └── [module]/   → [responsibility]
├── common/
│   ├── guards/
│   ├── decorators/
│   └── interceptors/
└── [entry point]
```

### Key entities and relationships

```
[EntityA]
  ├── [relationship]
  └── [relationship]

[EntityB]
  └── [relationship]
```

### Authentication flow

```
[Describe auth flow: register, login, token strategy, OAuth if applicable]
```

### Error handling pattern

```
[Describe global vs component-level error handling]
```

---

## 6. FRONTEND ARCHITECTURE

### Structure

```
src/
├── app/
│   ├── core/
│   ├── features/
│   └── shared/
└── environments/
```

### Key rules

- [Rule 1]
- [Rule 2]

---

## 7. CI / QUALITY PIPELINE

```
On every PR:
1. [step]
2. [step]
3. [step]
```

### Testing coverage targets

```
[Service/module]  →  [coverage %] target
```

---

## 8. ROLES & PERMISSIONS

```
[ROLE]  →  [what they can do]
```

---

## 9. KEY ARCHITECTURAL DECISIONS

| Decision | Rule | Reference |
|---|---|---|
| [topic] | [rule] | [D-number or doc] |

---

## 10. ONBOARDING — NEW DEVELOPER OR AGENT

### To understand the project
1. Read this file
2. Read `projects/[project].md` — current sprint, open issues
3. Read `DECISIONS.md` — the why behind architectural rules
4. Read `ORION.md` — team structure and workflow

### To run locally
```bash
[commands]
```

### To deploy to staging
```
[steps]
```

### To deploy to production
```
[steps]
```

---

*Part of Orion OS — built by Mario Vidal + Orion*
*Template version: 1.0 — April 2026*
