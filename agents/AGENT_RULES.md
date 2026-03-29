# AGENT RULES — Universal

> Every agent in Orion OS reads this file before starting any task.
> These rules apply to ALL agents regardless of role or project.

---

## ⛔ MANDATORY PROTOCOL — no exceptions, no shortcuts

Every task follows this exact sequence. Do NOT skip or reorder steps:

```
1. Implement the fix or feature
2. Run the build to verify no errors
3. Say ONLY: "Ready to test" — then STOP
4. Wait for the Director to approve
5. Only after approval: git commit + git push
```

**"Ready to test" means: I am done, I am waiting, I will not do anything else.**

If you push before the Director approves → protocol violation.
If you commit before the Director approves → protocol violation.
If you say anything other than "Ready to test" when done → protocol violation.

There are no exceptions. Not even if you are confident the code is correct.

---

## COMMUNICATION

- Implementation complete → say only: **"Ready to test"** then stop and wait
- Blocking issue → say only: **"Blocked: [one line]"**
- After Director approves and commit is done → say only: **"Done. Committed."**
- No explanations, no summaries, no walkthroughs unless the Director explicitly asks

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
ALLOWED:   Read the assigned issue body and comments from gameon-api repo
FORBIDDEN: Read any source code file via GitHub MCP
FORBIDDEN: List files or directories via GitHub MCP
FORBIDDEN: Any other GitHub MCP call
```

**For ALL code reading → use the project open in your IDE.**

---

## WINDOWS / POWERSHELL ENVIRONMENT

The Director works on **Windows 11 with PowerShell**. Always follow these rules for terminal commands:

- NEVER use `&&` to chain commands — it does NOT work in PowerShell
- Use `;` instead: `git add .; git commit -m "..."; git push origin develop`
- For environment variables use: `$env:VAR="value"` not `export VAR=value`

```powershell
# CORRECT
git add .
git commit -m "[OLGA] fix(auth): google oauth redirect ref Mjosuex85/gameon-api#74 | size: S"
git push origin develop

# WRONG — never do this
git add . && git commit -m "..." && git push origin develop
```

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

- Commit or push without explicit Director approval
- Touch `main` directly
- Close issues — that's Orion's job
- Change issue scope without notifying Orion
- Explain what was done — just say "Ready to test"
- Use `&&` in terminal commands — use `;` or separate lines
- Use GitHub MCP to read source code — open the file in the IDE instead

---

*Part of Orion OS — read this file at the start of every session.*
