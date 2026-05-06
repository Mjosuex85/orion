# Orion OS — Universal Configuration

> Configurations that apply to any project under Orion OS.
> Manual steps, conventions, and setup rules.
> Grows as we discover new things.

---

## GITHUB PROJECTS — Kanban Setup

### What it is
GitHub Projects v2 boards live at the **account level**, not inside the repo.
Each project needs to be manually linked to its repo — this does NOT happen automatically.

### How to create a board (first time)
1. GitHub → Projects → New Project → Board template
2. Name it exactly as the project: `GameOn`, `PortfolioMV`, `NutriApp`
3. Set columns: `Backlog → Ready → In progress → In review → Done`

### How to link a repo to the board (REQUIRED)
1. GitHub → Projects → `<ProjectName>` → Settings
2. **Default repository** → Select the repo → Save

> Without this step, issues created via GitHub MCP go to the repo but NOT to the board.

### Current status
| Project | Board | Repo linked |
|---------|-------|-------------|
| GameOn | ✅ | gameon-api (default) |
| PortfolioMV | ✅ | portfolioMV ✅ fixed Session 36 |
| NutriApp | ✅ | nutriapp ✅ fixed Session 36 |

### Known limitation
GitHub Projects v2 does not support full automation via REST MCP.
Auto-adding issues to the board requires GraphQL or manual setup.
**Current workaround:** Mario links repo manually. Issues then appear in board automatically.

---

*Part of Orion OS v2.0.0*
*Created: May 6, 2026 — Session 36*
