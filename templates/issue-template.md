# Template: Standard Issue

Every issue created by Orion must follow this format exactly.
Orion validates every issue against `workflows/issue-quality.md` before assigning.

---

## Title
```
type: Short description [BACKEND/FRONTEND] — Agent
```

Examples:
```
fix: Refresh token not persisting in production [BACKEND] — Nestor
feat: FIFA card component with real stats [FRONTEND] — Olga
```

---

## Issue body

```markdown
## Context
Why does this issue exist? What problem does it solve?

## Prompt for [NESTOR/OLGA]
[Exact and detailed instructions of what to implement.
Include: files to modify, reference code if applicable, constraints.]

## DO NOT touch
[Files or areas that are explicitly out of scope. Optional but recommended for L/XL.]

## Skills to inject
[Orion fills this using workflows/skill-injection.md]
- `skills/universal/git-flow.md`
- `skills/backend/[relevant].md` or `skills/frontend/[relevant].md`

## Subagents
[For L/XL issues. Orion fills this using workflows/skill-injection.md]
- `agents/subagents/[relevant].md`

## Files to modify
- `path/to/file.ts`

## Unit tests
[What tests to write or update as part of this issue.]
- Test: [description of what to test]
- File: `path/to/file.spec.ts`

## Expected commit
[AGENT] type(scope): description ref #XX | size: S/M/L/XL

## Test plan for the Director
1. Step-by-step how to verify it works
2. What to see on screen / console
3. Error cases to test
```

---

## Issue quality score

Before assigning, Orion checks (see `workflows/issue-quality.md`):

| Check | Status |
|-------|--------|
| Context | ✅/❌ |
| Prompt | ✅/❌ |
| Test plan | ✅/❌ |
| Agent assigned | ✅/❌ |
| Size estimated | ✅/❌ |
| Files listed | ✅/❌ |
| Skills injected | ✅/❌ |
| Subagents (L/XL) | ✅/❌/N/A |
| Expected commit | ✅/❌ |
| Unit tests | ✅/❌ |

Score ≥ 8 → assign. Score < 8 → Orion completes first.

---

## Token sizes

| Size | Est. tokens | When to use |
|------|-------------|-------------|
| S | ~1,000 | Simple bug fix, config change, 1-5 lines |
| M | ~3,000 | Small feature, refactor, 1 file |
| L | ~8,000 | Complex feature, multiple files |
| XL | ~15,000 | Architecture, deep analysis, new system |

---

## Standard labels

| Label | Use |
|-------|-----|
| `bug` | Something isn't working |
| `feature` | New functionality |
| `refactor` | Code improvement without behavior change |
| `devops` | Infrastructure, deploy, CI/CD |
| `architecture` | System design decisions |
| `nestor` | Assigned to Nestor |
| `olga` | Assigned to Olga |
| `priority: high` | Current sprint |
| `priority: medium` | Next sprint |
| `priority: low` | When there's time |
| `future` | Documented for the future |

---

*Part of Orion OS v1.3.0 — following this format ensures agents have the right context.*
