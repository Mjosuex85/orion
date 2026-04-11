# Release Workflow

> Orion reads this before every `staging → main` PR.

---

## Who does what

- **Orion** — decides the version type (patch/minor/major) based on what's in the release
- **Mario** — approves the version decision
- **Nestor or Olga** — edits `package.json` in the staging branch before the PR is opened (whoever did the last work on that repo)

---

## Version rules

| Type | When | Example |
|------|------|---------|
| `patch` | Bug fixes, hotfixes, config changes only | 1.3.0 → 1.3.1 |
| `minor` | New features, no breaking changes | 1.3.0 → 1.4.0 |
| `major` | Breaking API changes, major architecture change | 1.3.0 → 2.0.0 |

**Default:** when in doubt → `minor`. Never skip a version.

---

## Process

### Step 1 — Orion decides
Before opening the PR, Orion reviews all issues merged in this release cycle and declares:
```
Release type: minor
New version: v1.4.0
Reason: #106 (auto-assign ORGANIZER), #138 (lint), #144 (hotfix NestJS)
```

### Step 2 — Mario approves
Mario confirms or adjusts the version decision.

### Step 3 — Agent bumps `package.json`
The agent who opens the PR edits `package.json` on the staging branch:
```json
"version": "1.4.0"
```
Then commits:
```
chore(release): bump version to v1.4.0
```

### Step 4 — PR title
Mario opens the PR `staging → main` with the exact title:
```
release: v1.4.0
```

### Step 5 — Merge
Bruno ✅ → Mario merges → Vercel deploys to production.

---

## What Orion never does

- Skip the version decision step — every release has an explicit version
- Let an agent decide the version — that's Orion + Mario
- Use `major` without explicit discussion with Mario

---

*Part of Orion OS v1.4.0*
