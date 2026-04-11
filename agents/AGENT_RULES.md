# AGENT RULES — Universal

> Every agent in Orion OS reads this file before starting any task.
> These rules apply to ALL agents regardless of role or project.

---

## ⛔ MANDATORY PROTOCOL — no exceptions, no shortcuts

Every task follows this exact sequence. Do NOT skip or reorder steps:

```
1. Read skills listed in the issue's "Skills to inject" section
2. Read subagents listed in the issue's "Subagents" section (if any)
3. Implement the fix or feature
4. Self-review using subagent criteria (if subagents were injected)
5. Run the build to verify no errors
6. Say "Ready to test" with the Ready to Test Report (if applicable) — then STOP
7. Wait for the Director to approve
8. Only after approval: git commit + git push
```

**"Ready to test" means: I am done, I am waiting, I will not do anything else.**

If you push before the Director approves → protocol violation.
If you commit before the Director approves → protocol violation.

There are no exceptions. Not even if you are confident the code is correct.

---

## ✅ READY TO TEST REPORT (if applicable)

When saying "Ready to test", include this report **only when the issue involves logic changes, new features, or bug fixes**. Skip it for pure config, formatting, or dependency updates.

```
## Ready to test

### What I did
- [one line per meaningful change]

### How to test manually
- [step by step — endpoint, action, expected result]

### Tests added (if any)
- [file → test name]
```

**When NOT to include the report:** lint fixes, dependency bumps, config changes, formatting.
**When to include it:** features, bug fixes, logic changes, new endpoints, migrations.

---

## SKILLS AND SUBAGENTS

### Skills
If the issue has a `## Skills to inject` section:
1. Read each listed skill via GitHub MCP from `Mjosuex85/orion` (main branch)
2. Apply the patterns and constraints from each skill to your implementation
3. If a skill conflicts with the issue prompt → the issue prompt wins (it's more specific)

### Subagents
If the issue has a `## Subagents` section:
1. Read each listed subagent file from `agents/subagents/` via GitHub MCP
2. Use the subagent's criteria as a **self-review checklist** BEFORE saying "Ready to test"
3. If any criterion fails → fix it before declaring ready

If neither section exists in the issue, proceed normally — skills and subagents are optional.

---

## BRUNO — QA AGENT (automated)

Bruno runs automatically on every PR to `staging` or `main`. You do not interact with Bruno.

**Rules:**
- Bruno runs Jest tests on every PR
- If Bruno reports ❌ on your PR → fix the failing tests before asking Mario to merge
- If Bruno reports ✅ → Mario can proceed to review and merge
- Never ask Mario to merge if Bruno has not reported ✅
- Bruno opens issues automatically on failure — do not close them, Orion handles that

---

## COMMUNICATION

- Implementation complete → say: **"Ready to test"** + report (if applicable), then stop and wait
- Blocking issue → say only: **"Blocked: [one line]"**
- After Director approves and commit is done → say only: **"Done. Committed."**
- No extra explanations or walkthroughs unless the Director explicitly asks

Reason: Token efficiency. The Director reads the code, not the explanation.

---

## GIT

- Always `git pull origin develop` before starting
- ALL work goes to `develop` — NEVER touch `main`
- NEVER use `closes #XX` — only `ref #XX`
- **Do NOT commit until the Director explicitly says to commit**
- Commit format: `[AGENT] type(scope): description ref #XX | size: S/M/L/XL`

---

## MCP USAGE — STRICT RULE

**The MCP GitHub tool is used for reading Orion OS files and the assigned issue only.**

```
ALLOWED:   Read agents/*.md and AGENT_RULES.md from orion repo (wake-up protocol)
ALLOWED:   Read skills/*.md from orion repo (skill injection)
ALLOWED:   Read agents/subagents/*.md from orion repo (subagent injection)
ALLOWED:   Read the assigned issue body from the issues repo
FORBIDDEN: Read any source code file via GitHub MCP
FORBIDDEN: List files or directories via GitHub MCP
FORBIDDEN: Any other GitHub MCP call
```

**For ALL code reading → use the project open in your IDE.**

---

## WINDOWS / POWERSHELL ENVIRONMENT

The Director works on **Windows 11 with PowerShell**.

- NEVER use `&&` to chain commands
- Use `;` instead: `git add .; git commit -m "..."; git push origin develop`
- For environment variables: `$env:VAR="value"` not `export VAR=value`

---

## ISSUE READING

Every Orion issue has these sections:
1. **Context** — why it exists
2. **Prompt** — exactly what to implement
3. **Skills to inject** — knowledge to read before starting (optional)
4. **Subagents** — self-review criteria (optional, L/XL)
5. **Test plan** — what the Director verifies

If Context, Prompt, or Test plan is missing → **"Blocked: issue incomplete"**

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

- Commit or push without explicit Director approval
- Touch `main` directly
- Close issues — that's Orion's job
- Change issue scope without notifying Orion
- Use GitHub MCP to read source code
- Merge a PR when Bruno has reported ❌
- Include the Ready to Test report when it's not applicable (lint, deps, config)

---

*Part of Orion OS v1.4.0 — read this file at the start of every session.*
