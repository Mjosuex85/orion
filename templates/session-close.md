# Template: Session Close

> Orion uses this checklist at the end of every session.
> This is protocol, not optional.

---

## SESSION CLOSE CHECKLIST

### 1. Update project state
```
File: projects/<project>.md

- [ ] STATUS section updated with current date and session number
- [ ] Issues closed this session listed
- [ ] Issues created this session listed
- [ ] In Progress issues updated
- [ ] Next priorities updated
```

### 2. Log the session
```
File: logs/sessions.jsonl

Append one JSON line:
{
  "session": <number>,
  "date": "YYYY-MM-DD",
  "project": "<project-name>",
  "issues_closed": ["#XX", "#YY"],
  "issues_created": ["#ZZ"],
  "decisions": ["DXX"],
  "summary": "One-line summary of what happened",
  "orion_os_version": "X.X.X"
}
```

### 3. Update session log in ORION.md
```
File: ORION.md > SESSION LOG section

Add entry:
### Session XX — [Date]
- Key accomplishments
- Issues closed/created
- Decisions made
```

### 4. Update decisions (if applicable)
```
If new decisions were made:
- Universal decisions → DECISIONS.md
- Project-specific decisions → projects/<project>-decisions.md
```

### 5. Update metrics dashboard
```
File: metrics/METRICS.md

- [ ] System overview numbers updated
- [ ] Project issues count updated
- [ ] Session velocity table updated
- [ ] Agent performance updated (if relevant)
- [ ] Health check results from this session recorded
- [ ] Tech debt tracker updated (new debt added, resolved debt removed)
```

### 6. Confirm
```
Say: "Sesión cerrada. Próxima prioridad: [X]."
```

---

## NOTES

- This entire process should take < 3 minutes
- If Mario is in a hurry, compress to: update gameon.md + sessions.jsonl + confirm
- Never skip sessions.jsonl — it's the source of truth for velocity metrics
- METRICS.md can be updated less frequently (every 2-3 sessions) if sessions are short

---

*Part of Orion OS v1.2.0 — session close is protocol, not a question*
