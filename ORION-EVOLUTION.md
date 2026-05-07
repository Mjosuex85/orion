# ORION-EVOLUTION.md

> This file is read by Orion in MODO CTO.
> It is the source of truth for how Orion OS grows as a system.
> It contains: pending evolution, learned patterns, and open decisions about the system itself.
> Updated every session where system-level work happens.

---

## PENDING EVOLUTION

### P-01 — Fix stale paths in context-switch.md and health-check.md
**What:** Both files still reference flat paths (`projects/<project>.md`).
**Should be:** `projects/<X>/<X>.md` (modular structure from v2.1.0)
**Priority:** Medium — affects correctness of context switch protocol
**Session detected:** 36

### P-02 — Evaluate removing old flat redirect files in projects/
**What:** After modular migration, old files like `projects/gameon.md` were left as redirects.
**Decision needed:** Delete them cleanly or keep as permanent redirects?
**Priority:** Low — cosmetic, not blocking
**Session detected:** 36

### P-03 — Renew agent tokens
**What:** `nestor-gameon-agent` + `olga-gameon-agent` expired May 9, 2026.
**Action:** Mario renews tokens in GitHub → Settings → Developer settings → Personal access tokens
**Priority:** CRITICAL — blocks all agent sessions
**Session detected:** 36

### P-04 — gameon-state.md per project
**What:** Each project's `.md` mixes operational state with history and architecture.
**Proposal:** Extract a `<project>-state.md` — small, dense, always current.
  Orion reads this first in MODO PROYECTO. The larger files become reference-only.
**Priority:** High — directly improves bootstrap quality
**Session detected:** 36

### P-05 — Few-shot examples for Orion decision-making
**What:** Orion has no access to concrete examples of past good/bad decisions during bootstrap.
**Proposal:** Add a `ORION-PATTERNS.md` with 5-10 real examples from our sessions.
  Format: situation → what Orion did → outcome → lesson.
**Priority:** Medium — improves judgment in ambiguous situations
**Session detected:** 36

### P-06 — RFC-001 resolution
**What:** Instance Contract RFC open since Session 33. No movement.
**Decision needed:** How does a private instance relate to orion-os public layer?
**Priority:** Low — needed before v3.0.0, not urgent now
**Session detected:** 33

### P-07 — Fill in NutriApp and PortfolioMV vision.md stubs
**What:** Both files were created as stubs in Session 37 with seed ideas only.
**Action:** Mario writes the full original vision in his own voice, in low-pressure moments.
  Guide questions are already in each TODO block.
**Priority:** Low for PortfolioMV (seed may be enough), Medium for NutriApp (real product).
**Session detected:** 37

---

## LEARNED PATTERNS

> Real situations we encountered and how we resolved them.
> These are few-shot examples for Orion's judgment.

### Pattern 01 — Kanban not linked to repo
**Situation:** Issues created via MCP appeared in repo but not in board.
**Root cause:** GitHub Projects v2 requires manual repo linking — not automatic.
**Resolution:** Mario links repo in GitHub → Projects → Settings → Default repository.
**Lesson:** Every new project needs this step. Document in workflows/config.md immediately.
**Session:** 36

### Pattern 02 — Flat file structure doesn't scale
**Situation:** With 3 projects, `projects/` root was a flat list of 8+ files with no organization.
**Resolution:** Modular folder per project — 4 standard files each.
**Lesson:** Structure should anticipate growth. When adding the 3rd project, reorganize.
**Session:** 36

### Pattern 03 — Bootstrap loading too much, too early
**Situation:** Every session loaded all project files + skills + health check regardless of purpose.
**Resolution:** Two modes — MODO CTO (identity only) / MODO PROYECTO (full context).
**Lesson:** Context has a cost. Load only what the session needs.
**Session:** 36

### Pattern 04 — Orion lives in GitHub, not in Claude
**Situation:** Mario worried that switching Claude Projects would lose "36 sessions of accumulated memory."
**Root cause:** Conflating *runtime* (where Orion executes) with *identity* (where Orion lives).
**Resolution:** Clarified that Orion's brain is `Mjosuex85/orion` — ORION.md, DECISIONS.md, ORION-EVOLUTION.md, logs, projects. Claude.ai is just one possible runtime. Any client with GitHub MCP can wake Orion.
**Lesson:** Claude Projects are conversation containers, not identity containers. They need no system files — just GitHub MCP connected. The `Orion OS` Claude Project is empty by design. Reinforces D90 (no terminal assumed) and prepares the ground for v3.0.0 web interface.
**Session:** 37

### Pattern 05 — Frozen vision protects against drift
**Situation:** GameOn's project files (`gameon.md`, `gameon-decisions.md`) had grown technical and operational. The original product idea ("FIFA card appears when you touch the field") was implicit, scattered, never written as a single artifact.
**Resolution:** Created `<name>-vision.md` as a fifth standard project file (D100). Frozen, dated, in Mario's original voice. Anchor to compare against in any strategic session.
**Lesson:** Operational files drift. Frozen vision files don't. Both are needed — never merge them.
**Session:** 37

---

## OPEN SYSTEM DECISIONS

### OSD-01 — Should ORION-EVOLUTION.md be read in MODO PROYECTO too?
**Question:** Today it's only read in MODO CTO. But some patterns are relevant during project work.
**Options:** A) CTO only (current) B) Always C) Only when session touches system files
**Status:** 🟡 Open

### OSD-02 — gameon-state.md format
**Question:** What is the minimum viable state file? What fields are essential?
**Candidate fields:** current version, open blockers, next priority, last session date
**Status:** 🟡 Open — design before implementing P-04

---

## EVOLUTION LOG

| Session | Change | Impact |
|---------|--------|--------|
| 37 | v2.2.0 — vision.md as 5th standard file (D100). Pattern 04 (Orion lives in GitHub) | High |
| 36 | Bootstrap v2.1.0 — MODO CTO / MODO PROYECTO | High |
| 36 | Modular project folder structure | High |
| 36 | workflows/config.md — Kanban convention | Medium |
| 35 | LICENSE added for recruiter visibility | Low |
| 34 | PortfolioMV registered, OLGA-REACT.md created | Medium |
| 33 | Orion OS v2.0.0 — public layer extracted | High |
| 26 | Security incident protocol (D93) | High |
| 21 | v1.1.0 → v1.4.0 framework evolution | High |

---

*Orion OS v2.2.0 — last updated Session 37, May 7, 2026*
*Read by Orion in MODO CTO*
