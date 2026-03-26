# Template: Standard Issue

Every issue created by Orion must follow this format exactly.

---

## Title
```
type: Short description [BACKEND/FRONTEND/FULLSTACK] — Agent
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

## Skills to inject
- `skills/universal/git-flow.md`
- `skills/backend/[relevant].md` or `skills/frontend/[relevant].md`
- `skills/projects/[project]/[relevant].md`

## Files to modify
- `path/to/file.ts`

## Expected commit
[AGENT] type(scope): description ref #XX | size: S/M/L/XL

## Test plan for the Director
1. Step-by-step how to verify it works
2. What to see on screen / console
3. Error cases to test
```

---

## Token sizes

| Size | Est. tokens | When to use |
|------|-------------|-------------|
| S    | ~1,000      | Simple bug fix, config change, 1-5 lines |
| M    | ~3,000      | Small feature, refactor, 1 file |
| L    | ~8,000      | Complex feature, multiple files |
| XL   | ~15,000     | Architecture, deep analysis, new system |

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

*Part of Orion OS — following this format ensures agents have the right context.*
