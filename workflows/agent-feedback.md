# Agent Feedback Loop — Orion OS

> After every completed issue, Orion evaluates how the agent performed.
> This feeds improvements to system prompts, skills, and issue quality.

---

## WHEN TO RUN

After Mario approves and the agent commits:

1. Orion evaluates the agent's work
2. Records the evaluation in `logs/agent-feedback.jsonl`
3. If patterns emerge → updates agent system prompt or creates new skill

---

## EVALUATION CRITERIA

| # | Criterion | Score |
|---|-----------|-------|
| 1 | **Protocol compliance** | ✅ Followed / ⚠️ Minor deviation / ❌ Violation |
| 2 | **Code quality** | ✅ Clean / ⚠️ Needed minor fixes / ❌ Required significant rework |
| 3 | **Test coverage** | ✅ Tests included and passing / ⚠️ Tests weak / ❌ No tests |
| 4 | **First-attempt success** | ✅ Ready to test was final / ⚠️ 1 correction / ❌ 2+ corrections |
| 5 | **Scope respect** | ✅ Did exactly what was asked / ⚠️ Minor scope creep / ❌ Changed things outside scope |

---

## LOG FORMAT

```
logs/agent-feedback.jsonl
```

```json
{
  "date": "2026-04-10",
  "session": 21,
  "project": "gameon",
  "issue": "#129",
  "agent": "olga",
  "size": "S",
  "protocol": "green",
  "code_quality": "green",
  "tests": "green",
  "first_attempt": "green",
  "scope": "green",
  "overall": "excellent",
  "notes": ""
}
```

Overall ratings: `excellent` (all green) | `good` (1 yellow) | `needs_improvement` (2+ yellow) | `problematic` (any red)

---

## PATTERN DETECTION

Orion reviews feedback data periodically (every 5-10 issues) to detect patterns:

| Pattern | Action |
|---------|--------|
| Agent consistently scores `excellent` | No changes needed — the system works |
| Agent has 2+ `yellow` on protocol | Review AGENT_RULES.md — maybe a rule is unclear |
| Agent has 2+ `yellow` on code_quality | Review the relevant skill files — add more examples |
| Agent has 2+ `yellow` on first_attempt | Issues may need more detail in the prompt section |
| Agent has 2+ `yellow` on scope | Add explicit "DO NOT touch" section in issues |
| Agent has any `red` | Discuss with Mario — might need model upgrade or prompt rewrite |

---

## FEEDBACK → ACTION

Feedback is only valuable if it creates change. The loop:

```
Agent completes issue
  → Orion evaluates
    → Logs feedback
      → Detects pattern (if any)
        → Updates system prompt / skill / issue template
          → Next issue benefits from the improvement
```

This is not a performance review — it's a system calibration mechanism. The agents don't see their scores. The scores improve the instructions they receive.

---

## WHEN NOT TO LOG

- Orion OS internal work (no agent involved)
- Issues closed without agent execution (e.g., obsolete issues)
- Sessions focused on planning/architecture only

---

*Part of Orion OS v1.3.0 — every issue makes the system smarter*
