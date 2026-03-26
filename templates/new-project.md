# Template: New Project

Use this template to start any new project with Orion OS in under 1 hour.

---

## 1. Define the project

```
Project name:
What problem does it solve?
One-line pitch:
User types:
First target user:
Backend stack:
Frontend stack:
Database:
Deploy:
```

---

## 2. Infrastructure checklist

- [ ] Backend repo: `owner/project-api`
- [ ] Frontend repo: `owner/project`
- [ ] GitHub Project (Kanban): Todo / In Progress / Done
- [ ] Create `projects/name.md` in Orion OS with project context
- [ ] Copy base `AGENTS.md` to the project repo
- [ ] Copy base `DECISIONS.md` to the project repo and adapt specific rules
- [ ] Document environment variables in `.env.example`

---

## 3. Configure agents

- [ ] Nestor receives: `agents/NESTOR.md` + `projects/name.md` + relevant skills
- [ ] Olga receives: `agents/OLGA.md` + `projects/name.md` + relevant skills
- [ ] Confirm both understand the stack and rules

---

## 4. First issue

Orion creates issue #1:

```markdown
## Context
Initial project setup — folder structure, base configuration, first endpoint/component.

## Prompt for [NESTOR/OLGA]
[Specific setup instructions]

## Test plan for the Director
1. Project starts without errors
2. First endpoint/component responds correctly
3. Environment variables documented in .env.example
```

---

## 5. Project-specific rules

Complete in `projects/name.md`:
- Full stack details
- Rules that differ from global ones
- Required environment variables
- Deploy flow
- Initial state

---

*Part of Orion OS — using this template ensures consistency across projects.*
