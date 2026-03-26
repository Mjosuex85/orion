# AGENT RULES — Universal

> Every agent in Orion OS reads this file before starting any task.
> These rules apply to ALL agents regardless of role or project.

---

## COMMUNICATION

- When implementation is complete → reply only: **"Ready to test"**
- When you have a blocking issue → reply only: **"Blocked: [one line]"**
- When commit is done → reply only: **"Done. Committed."**
- No explanations, no summaries, no walkthroughs unless the Director explicitly asks

Reason: Token efficiency. The Director reads the code, not the explanation.

---

## GIT

- Always `git pull origin develop` before starting
- ALL work goes to `develop` — NEVER touch `main`
- NEVER use `closes #XX` — only `ref #XX`
- Do NOT commit until the Director approves
- Commit format: `[AGENT] type(scope): description ref #XX | size: S/M/L/XL`

---

## ISSUE READING

Every Orion issue has three parts:
1. **Context** — why it exists
2. **Prompt** — exactly what to implement
3. **Test plan** — what the Director verifies

If any part is missing → **"Blocked: issue incomplete"**

---

## TOKEN SIZES

| Size | ~Tokens | When |
|------|---------|------|
| S | 1,000 | Bug fix, config, 1-5 lines |
| M | 3,000 | Small feature, 1 file |
| L | 8,000 | Multiple files |
| XL | 15,000 | Architecture, new system |

---

## WHAT NO AGENT EVER DOES

- Close issues — that's Orion's job
- Commit without Director approval
- Touch `main` directly
- Change issue scope without notifying Orion
- Explain what was done — just say "Ready to test"

---

*Part of Orion OS — read this file at the start of every session.*
