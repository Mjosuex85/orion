# PortfolioMV — Project Context

> Technical context for Orion and Olga.
> Project-specific decisions live in `projects/portfoliomv/portfoliomv-decisions.md`.

---

## WHAT IS PORTFOLIOMV

Mario's personal developer portfolio. Single-page React app with bilingual support (ES/EN).
The differentiator is that it reflects Mario's real career — not a template. It shows who he is as a technical founder.

**Current state:** Functional but needs content + features updated to reflect the current stage of Mario's career (GameOn, NutriApp, Orion OS).

---

## ISSUES REPO: Mjosuex85/portfolioMV
## KANBAN:      GitHub Projects > PortfolioMV (account level)

Columns: Backlog → Ready → In progress → In review → Done

---

## STACK

```
Framework:    React 18 (Create React App)
State:        Redux + redux-thunk (language toggle only)
Router:       React Router v6
Styling:      CSS per component + Unicons + Boxicons (CDN)
Contact:      EmailJS
DB:           Supabase (SDK installed, supabase/ dir exists)
Deploy:       Vercel
Analytics:    @vercel/analytics + @vercel/speed-insights
Node:         22.x
```

⚠️ CRA is deprecated. Migration to Vite is a future consideration — not current priority.

---

## REPO

```
Mjosuex85/portfolioMV  → private, single repo
```

Branch structure:
```
main     → production (Vercel)
develop  → active work (Olga commits here)
```

---

## AGENT

**Olga-React** handles this project (load `OLGA-REACT.md`, NOT `OLGA.md`).

---

## STATUS — May 7, 2026 (Session 36)

**Production:** Functional — last push April 22, 2026

**Open / Pending:**
- ✅ INC-01 RESOLVED: portfolioMV repo linked to GitHub Project board (Session 36)
- ✅ INC-02 UNBLOCKED: Olga can retry session after INC-01 resolved
- 📋 Issue #18 open: replace StockWise → Orion OS card + OrionOS page
- 📋 Update project entries: GameOn, NutriApp, Orion OS
- 📋 Evaluate: Supabase integration (contact form? visitor tracking?)
- 📋 Evaluate: CRA → Vite migration (future, not blocking)

**Next session priority:**
1. Fresh Olga-React session → issue #18

---

*Part of Orion OS v2.0.0 — updated Session 36 (modular folder structure + INC-01 resolved)*
