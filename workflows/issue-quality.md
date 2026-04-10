# Issue Quality Score — Orion OS

> Before assigning any issue to an agent, Orion validates it against this checklist.
> An incomplete issue wastes tokens and creates rework. Quality in = quality out.

---

## THE SCORE

Orion evaluates every issue before assignment. Each criterion is pass/fail.

| # | Criterion | Required? | Check |
|---|-----------|-----------|-------|
| 1 | **Context section exists** | ✅ Yes | Why does this issue exist? What problem does it solve? |
| 2 | **Prompt section exists** | ✅ Yes | Exact instructions for the agent |
| 3 | **Test plan exists** | ✅ Yes | How the Director verifies it works |
| 4 | **Agent assigned** | ✅ Yes | [BACKEND] → Nestor, [FRONTEND] → Olga |
| 5 | **Size estimated** | ✅ Yes | S / M / L / XL |
| 6 | **Files to modify listed** | ⚠️ Recommended | Specific file paths (not required for S issues) |
| 7 | **Skills injected** | ⚠️ Recommended | Relevant skills from `skills/` (see skill-injection.md) |
| 8 | **Subagents specified** | ⚠️ For L/XL | Which subagents should the agent activate |
| 9 | **Expected commit message** | ⚠️ Recommended | `[AGENT] type(scope): desc ref #XX \| size: X` |
| 10 | **Unit tests included** | ✅ Yes | Tests are part of the issue, not a separate issue |

---

## SCORING

```
✅ READY (8-10 criteria met)      → Assign immediately
⚠️ NEEDS WORK (5-7 criteria met)  → Orion completes the missing parts, then assigns
❌ INCOMPLETE (<5 criteria met)   → Do not assign — rewrite the issue first
```

---

## WHEN TO RUN

- **Always:** Before assigning any issue to Nestor or Olga
- **Never skip:** Even for XS/S issues — the check takes 10 seconds, a bad issue wastes 10 minutes
- **Retroactive:** If an agent reports "Blocked: issue incomplete" → run the score, fix, reassign

---

## ORION'S RESPONSIBILITY

Orion is the quality gate. If an issue scores below READY:

1. Orion fills in the missing parts (context, prompt detail, test plan, skills)
2. Orion does NOT ask Mario to complete the issue unless it requires a business decision
3. Only issues that require Mario's input get flagged: "This issue needs a decision before I can complete it: [specific question]"

The goal: **Mario never has to think about issue format.** He says what he wants, Orion structures it.

---

## EXAMPLES

### ✅ READY issue
```markdown
## Context
Organizer panel shows 0 matches on dashboard because the API call 
doesn't pass showAll=true.

## Prompt for OLGA
1. In `organizer-dashboard.component.ts`, update the `loadStats()` method
2. Add `?showAll=true` param to the GET /organizations/:id/matches call
3. File: src/app/features/organizer/dashboard/organizer-dashboard.component.ts

## Skills to inject
- skills/frontend/api-service.md

## Test plan for the Director
1. Open organizer panel → dashboard shows real match count
2. Create a new match → count updates
3. Network tab shows showAll=true in the request

Expected commit: [OLGA] fix(organizer): dashboard match count ref #129 | size: S
```

### ❌ INCOMPLETE issue
```markdown
## Context
Fix the dashboard.

## Prompt for OLGA
The dashboard is broken, fix it.
```
❍ Missing: test plan, files, skills, size, expected commit, unit tests.

---

*Part of Orion OS v1.3.0 — quality in = quality out*
