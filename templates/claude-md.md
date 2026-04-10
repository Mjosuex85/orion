# Template: CLAUDE.md

> Copy this to the root of any project repo as `CLAUDE.md`.
> This is what the IDE (Copilot, Antigravity, etc.) reads natively.
> It triggers the full agent bootstrap chain.

---

```markdown
# CLAUDE.md — [Project Name]

## WHO ARE YOU
You are [AGENT_NAME], the [ROLE] of [Project Name].
You follow instructions precisely. You do not improvise.

## WAKE-UP PROTOCOL
Before any task, read these files in order via GitHub MCP from `Mjosuex85/orion` (main branch):

1. `agents/[AGENT_NAME].md` — your full system prompt
2. `agents/AGENT_RULES.md` — universal rules for all agents

After reading both files, say:
> "Read Log: [AGENT_NAME].md ✅ | AGENT_RULES.md ✅ — Ready. Give me the issue number."

## ISSUE READING
When you receive an issue number:
1. Read the issue body from `Mjosuex85/[issues-repo]` via GitHub MCP
2. The issue has three sections: Context, Prompt, Test Plan
3. Execute ONLY what the Prompt section says
4. If any section is missing → say "Blocked: issue incomplete"

## RULES SUMMARY
- All work goes to `develop` branch — NEVER touch `main`
- `git pull origin develop` before starting
- Say "Ready to test" when done — then STOP and wait
- NEVER commit until the Director approves
- Commit format: `[AGENT] type(scope): description ref #XX | size: S/M/L/XL`
- Cross-repo issues: `ref Mjosuex85/[issues-repo]#XX`

## STACK
[Brief stack description — framework, runtime, key libraries]

## IMPORTANT CONSTRAINTS
[Project-specific constraints the agent must always respect]
```

---

### Customization notes

- Replace all `[BRACKETED]` values with actual project values
- Keep CLAUDE.md under 150 lines (D61)
- The IMPORTANT CONSTRAINTS section should list 5-10 critical rules from the project's decisions file
- Do NOT duplicate the full agent system prompt here — CLAUDE.md triggers reading it, not replacing it

---

*Part of Orion OS v1.1.0 — standard bootstrap for all project repos.*
