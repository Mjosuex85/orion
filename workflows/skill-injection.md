# Skill Injection Map — Orion OS

> Orion uses this map to determine which skills and subagents to inject in each issue.
> Skills provide reusable knowledge. Subagents provide specialized review criteria.

---

## HOW SKILL INJECTION WORKS

1. Orion creates or reviews an issue
2. Orion checks the issue type and scope against this map
3. Orion adds the relevant skills to the `## Skills to inject` section
4. Orion adds relevant subagents to the `## Subagents` section (for L/XL issues)
5. The agent reads the skills via GitHub MCP before starting work

---

## SKILL MAP

### Universal (always inject)
| Trigger | Skill |
|---------|-------|
| Any issue | `skills/universal/git-flow.md` |
| Any issue | `skills/universal/issue-reading.md` |

### Backend — Nestor
| Trigger | Skill |
|---------|-------|
| New endpoint or service method | `skills/backend/nestjs-patterns.md` |
| Auth-related (login, guards, tokens) | `skills/backend/jwt-auth.md` |
| Schema change, new entity, new column | `skills/backend/typeorm-migrations.md` |

### Frontend — Olga
| Trigger | Skill |
|---------|-------|
| New component or component refactor | `skills/frontend/angular-signals.md` |
| API call (new or modified) | `skills/frontend/api-service.md` |

---

## SUBAGENT MAP

Subagents provide specialized review criteria. They are injected in L/XL issues or when the scope directly matches their expertise.

### Backend subagents (Nestor reads from `agents/subagents/`)
| Trigger | Subagent |
|---------|----------|
| New module, service restructure, DI changes | `nestjs-architecture.md` |
| Any migration, entity change, column rename | `typeorm-migrations.md` |
| Auth, secrets, CORS, rate limiting, input validation | `backend-security.md` |

### Frontend subagents (Olga reads from `agents/subagents/`)
| Trigger | Subagent |
|---------|----------|
| New component, component split, Atomic Design | `angular-component-architecture.md` |
| Lazy loading, bundle size, change detection | `angular-performance.md` |
| Any user-facing component | `ui-design-reviewer.md` |
| Forms, navigation, screen readers | `angular-accessibility.md` |

---

## INJECTION RULES

1. **S issues:** universal skills only. Subagents are overkill.
2. **M issues:** universal + 1-2 relevant skills. Subagents only if scope is a direct match.
3. **L issues:** universal + all relevant skills + 1-2 subagents.
4. **XL issues:** universal + all relevant skills + all relevant subagents.

---

## AGENT READING PROTOCOL

When an agent sees `## Skills to inject` in the issue:

```
1. Read each listed skill via GitHub MCP from Mjosuex85/orion (main)
2. Apply the patterns and constraints from each skill to the implementation
3. If a skill conflicts with the issue prompt → follow the issue prompt (it's more specific)
```

When an agent sees `## Subagents` in the issue:

```
1. Read each listed subagent file from agents/subagents/ via GitHub MCP
2. Use the subagent's criteria as a self-review checklist BEFORE saying "Ready to test"
3. If any criterion fails → fix it before declaring ready
```

---

## ADDING NEW SKILLS

When a pattern emerges that should be reusable:

1. Orion creates the skill file in the appropriate `skills/` subdirectory
2. Orion adds the trigger to this map
3. All future issues matching the trigger get the skill injected

Skill format:
```markdown
# Skill: [Name]
> When to use: [trigger description]

## Rules
- [Rule 1]
- [Rule 2]

## Patterns
[Code examples, dos/don'ts]

## Common mistakes
[What agents get wrong and how to avoid it]
```

---

*Part of Orion OS v1.3.0 — the right knowledge at the right time*
