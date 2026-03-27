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

## MCP USAGE — STRICT RULE

**The MCP GitHub tool is used for ONE purpose only: reading the assigned issue.**

```
ALLOWED:   Read the issue from GitHub MCP to get the task description
FORBIDDEN: Read any source code file via GitHub MCP
FORBIDDEN: List files or directories via GitHub MCP
FORBIDDEN: Any GitHub MCP call after the issue is read
```

**For ALL code reading → use the project open in your IDE.** You work inside an IDE (Antigravity, VSCode, etc.) that already has the project loaded. Read files directly from there — no MCP needed.

Context: Today agents work with the project cloned locally in the IDE. In the future, when agents operate fully autonomously without a local project, GitHub MCP may be used for code reading. For now: IDE only.

Reason: The IDE already has the full project context. Using GitHub MCP to read code that is already open in the IDE wastes tokens and adds latency.

---

## WINDOWS / POWERSHELL ENVIRONMENT

The Director works on **Windows 11 with PowerShell**. Always follow these rules for terminal commands:

- NEVER use `&&` to chain commands — it does NOT work in PowerShell
- Use `;` instead: `git add .; git commit -m "..."; git push origin develop`
- For environment variables use: `$env:VAR="value"` not `export VAR=value`
- For multiline commands, use separate lines — not chained with `&&`

```powershell
# CORRECT
git add .
git commit -m "[OLGA] fix(match-create): convert isLoading to signal ref Mjosuex85/gameon-api#61 | size: S"
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

- Close issues — that's Orion's job
- Commit without Director approval
- Touch `main` directly
- Change issue scope without notifying Orion
- Explain what was done — just say "Ready to test"
- Use `&&` in terminal commands — use `;` or separate lines instead
- **Use GitHub MCP to read source code — open the file in the IDE instead**

---

*Part of Orion OS — read this file at the start of every session.*
