# Template: New Project

Use this template to start any new project with Orion OS.

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
- [ ] Create `projects/<name>.md` in Orion OS with project context
- [ ] Create `projects/<name>-decisions.md` in Orion OS for project-specific decisions
- [ ] Copy `templates/claude-md.md` to both repos as `CLAUDE.md` and adapt
- [ ] Document environment variables in `.env.example`
- [ ] Set up `.npmrc` with `ignore-scripts=true` (D73)
- [ ] Set up `.gitattributes` with `* text=auto eol=lf`

---

## 3. Configure agents

### Bootstrap chain (applies to all agents)
```
1. IDE reads CLAUDE.md natively from the project repo (develop branch)
2. CLAUDE.md instructs: read agents/<AGENT>.md + AGENT_RULES.md via GitHub MCP from orion
3. Agent confirms Read Log and waits for issue number
4. Mario gives issue number → agent reads issue body via GitHub MCP
```

### Agent setup
- [ ] Nestor: verify GITHUB_PAT has Read+Write on new repos + orion
- [ ] Olga: update mcp_config.json token if needed
- [ ] Bruno: copy `bruno.yml` to `.github/workflows/` in both repos, configure secrets

---

## 4. CI/CD setup

- [ ] Copy CI workflow from existing project and adapt
- [ ] Set up SonarCloud project + add SONAR_TOKEN to GitHub Secrets
- [ ] Configure `sonar-project.properties` in repo root
- [ ] Set initial coverage threshold (50% recommended for new projects)

---

## 5. First issue

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

## 6. Project-specific decisions

Complete in `projects/<name>-decisions.md`:
- Architecture decisions specific to this project
- Stack-specific rules
- Deploy-specific configuration
- Business logic constraints

---

*Part of Orion OS v1.1.0 — using this template ensures consistency across projects.*
