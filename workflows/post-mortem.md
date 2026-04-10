# Post-Mortem Protocol — Orion OS

> When something goes wrong, we learn from it.
> Post-mortems are not blame — they are system improvements.

---

## WHEN TO CREATE A POST-MORTEM

| Trigger | Action |
|---------|--------|
| Bruno reports ❌ on a PR | Post-mortem entry |
| An issue is reopened after being closed | Post-mortem entry |
| A bug reaches staging or production | Post-mortem entry |
| Mario finds a problem during testing | Post-mortem entry |
| An agent reports "Blocked" due to issue quality | Post-mortem entry (for Orion, not the agent) |

---

## WHERE THEY LIVE

```
logs/post-mortems.jsonl
```

One JSON line per incident. Machine-parseable for future analysis.

---

## FORMAT

```json
{
  "id": "PM-001",
  "date": "2026-04-10",
  "session": 21,
  "project": "gameon",
  "trigger": "bruno_failure | issue_reopened | bug_in_staging | testing_failure | agent_blocked",
  "issue": "#XXX",
  "agent": "nestor | olga | orion",
  "what_happened": "One sentence: what went wrong",
  "root_cause": "One sentence: why it went wrong",
  "category": "issue_quality | missing_test | wrong_assumption | missing_skill | protocol_violation | technical_gap",
  "fix_applied": "What was done to fix it",
  "prevention": "What rule, skill, or check prevents this from recurring",
  "new_rule": "Dxx added to DECISIONS.md (if applicable)"
}
```

---

## CATEGORIES

| Category | Meaning | Typical prevention |
|----------|---------|--------------------|
| `issue_quality` | Issue was incomplete or ambiguous | Improve issue-quality.md checklist |
| `missing_test` | Bug not covered by existing tests | Add test requirement to issue template |
| `wrong_assumption` | Agent assumed something not in the issue | Add explicit constraint to prompt |
| `missing_skill` | Agent didn't know a project-specific pattern | Create new skill file |
| `protocol_violation` | Agent skipped a step in AGENT_RULES | Reinforce in AGENT_RULES.md |
| `technical_gap` | Agent's model didn't know how to do something | Escalate to higher model or add subagent |

---

## ORION'S RESPONSIBILITY

Orion creates post-mortem entries as part of session work:

1. **Detect:** Any of the triggers above occurs
2. **Log:** Append JSON line to `logs/post-mortems.jsonl`
3. **Act:** Apply the prevention measure immediately (new rule, updated skill, etc.)
4. **Track:** Add to `metrics/METRICS.md` trends section if it reveals a pattern

The goal is not documentation for its own sake — it's **closing the loop** so the same failure never happens twice.

---

## PATTERN DETECTION

When Orion sees 3+ post-mortems with the same category:

| Pattern | Action |
|---------|--------|
| 3+ `issue_quality` | Review and strengthen `issue-quality.md` |
| 3+ `missing_test` | Consider raising coverage threshold or adding test-specific skills |
| 3+ `wrong_assumption` | The issue template needs more explicit constraint fields |
| 3+ `missing_skill` | Create a new skill or expand existing ones |
| 3+ `protocol_violation` | Discuss with Mario — the rule may be unclear or impractical |
| 3+ `technical_gap` | Consider upgrading the model tier for that type of task |

---

*Part of Orion OS v1.3.0 — same mistake never happens twice*
