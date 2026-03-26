# Skill: Git Flow — Universal

> This skill applies to ALL projects and ALL agents.

---

## Absolute rules

- `main` is NEVER touched — except on planned deploys
- ALL work goes to `develop`
- Always `git pull origin develop` BEFORE starting
- NEVER use `closes #XX` — only `ref #XX`
- NEVER commit without Director approval

## Mandatory commit format

```
[AGENT] type(scope): description ref #XX | size: S/M/L/XL
```

Valid examples:
```
[NESTOR] fix(auth): cookie path corrected ref #42 | size: S
[OLGA] feat(profile): add avatar upload component ref #53 | size: M
[NESTOR] refactor(matches): optimize N+1 queries ref #55 | size: L
```

## Commit types

| Type | Use |
|------|-----|
| `feat` | New functionality |
| `fix` | Bug fix |
| `refactor` | Improvement without behavior change |
| `style` | Style/format changes |
| `docs` | Documentation |
| `chore` | Config, dependencies |

## Sizes

| Size | Tokens | When |
|------|--------|------|
| S | ~1,000 | 1-5 lines, config |
| M | ~3,000 | 1 file, small feature |
| L | ~8,000 | Multiple files |
| XL | ~15,000 | Architecture, new system |
