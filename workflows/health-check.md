# Health Check Protocol

> Run by Orion at the start of every session, BEFORE giving the status summary.
> Results are recorded in `metrics/METRICS.md`.

---

## PURPOSE

Detect problems early. A 2-minute check at session start prevents wasted hours debugging something that was already broken.

---

## THE CHECKS

Orion runs these checks using GitHub MCP and reports results to Mario.

### 1. CI Status

**What:** Check if the latest CI run on `develop` is green for each active project repo.

**How:** 
- Look at the most recent commit on `develop` in each repo
- Check if CI workflows passed (via GitHub Actions status)
- If a repo has failing CI → flag immediately

**Report:**
```
✅ gameon-api CI: green
❌ gameon CI: failing (lint errors in match-filters.component.ts)
```

### 2. Stale PRs

**What:** Any open PR older than 3 days.

**How:** List open PRs in each project repo. Flag any updated > 3 days ago.

**Report:**
```
✅ No stale PRs
⚠️ gameon-api PR #42: open 5 days (develop → staging)
```

### 3. Unassigned Issues

**What:** Open issues in the issues repo that have no agent label.

**How:** List open issues without `nestor`, `olga`, or `future` labels.

**Report:**
```
✅ All issues assigned or labeled
⚠️ #142: no agent assigned — needs triage
```

### 4. Branch Sync

**What:** Check if `develop` is ahead of `staging` or `main` by more than 10 commits.

**How:** Compare branch heads.

**Report:**
```
✅ develop/staging in sync
⚠️ gameon: develop is 15 commits ahead of staging — PR needed
```

### 5. Bruno Status

**What:** Verify Bruno QA ran on the last PR.

**How:** Check the last PR to `staging` or `main` for Bruno's comment (✅ or ❌).

**Report:**
```
✅ Bruno ran on last PR: all tests passed
❌ Bruno did not run on last PR — check workflow permissions
```

### 6. In-Progress Limit

**What:** Verify max 2 issues are In Progress (D7).

**How:** Count issues with "In Progress" status in the project board.

**Report:**
```
✅ 2 issues in progress (#120, #123)
❌ 3 issues in progress — exceeds D7 limit
```

---

## WHEN TO RUN

- **Always:** At the start of every session, after reading ORION.md + DECISIONS.md + project.md
- **Before:** Giving the status summary to Mario
- **Extra:** Before any PR from staging → main (pre-release check)

---

## OUTPUT FORMAT

Orion presents the health check as part of the session opening:

```
Health check:
  ✅ CI green (both repos)
  ✅ No stale PRs
  ✅ All issues assigned
  ⚠️ gameon frontend: develop 12 commits ahead of staging
  ✅ Bruno operational
  ✅ 2 issues in progress

Estado rápido: [normal status summary]
```

If everything is green, compress to one line:
```
Health check: all green ✅
```

---

## ESCALATION

| Severity | Action |
|----------|--------|
| ✅ All green | Continue normally |
| ⚠️ Warning | Mention to Mario, add to session priorities if relevant |
| ❌ Critical (CI failing, Bruno broken) | Address FIRST before any other work |

---

*Part of Orion OS v1.2.0 — health check runs every session start*
