# Decisions — NutriApp

> Project-specific architecture decisions.
> Universal decisions live in DECISIONS.md.

---

## 1. TOPOLOGY & STACK

**ND1. Monorepo — single repo `Mjosuex85/nutriapp`.**
Reason: no dedicated backend, single developer, simpler deploy.

**ND2. No dedicated backend — Supabase SDK direct from frontend.**
Reason: the data model is simple and personal. Adding NestJS would be over-engineering for Phase 1.
Review at Phase 3 if multi-tenant or complex server logic is needed.

**ND3. Frontend: React 19 + Vite + TypeScript.**
Reason: Mario learns React with a real project. Angular is GameOn's domain.

**ND4. State: Zustand over Context API.**
Reason: simpler API, less boilerplate, scales better than Context for this data model.

**ND5. Database: Supabase (PostgreSQL). Auth: Supabase Auth (Phase 2 only).**
Reason: PostgreSQL is the same paradigm as GameOn (familiar). SDK works directly from frontend.
Phase 1 has no auth — single user, local use only.

**ND6. Deploy: Vercel.**
Reason: consistent with GameOn, Mario already knows the platform.

---

## 2. DATA MODEL

**ND7. Stock is a single Supabase row with JSONB columns for proteins, carbs, condiments.**
Reason: stock is always read/written as a whole. Normalizing into rows would add unnecessary joins.

**ND8. `history` is append-only — never update or delete entries.**
Reason: audit trail of what was consumed. Corrections are new entries, not edits.

**ND9. `monthly_plans` row is unique per month+year — upserted, never inserted.**
Reason: deterministic fetch by month+year. `upsert({ onConflict: 'month,year' })` prevents duplicates.

**ND19. Done flags (`breakfast_done`, `lunch_dinner_done`) are stored inside the JSONB `meals` array — no schema change.**
Reason: optional booleans don't justify a schema migration. JSONB is flexible enough and the field is nullable by default.

---

## 3. FRONTEND

**ND10. CSS Modules for component styles — no Tailwind, no inline styles.**
Reason: clean separation from GameOn's Tailwind setup. Mario explores CSS Modules. All design tokens in `index.css` CSS variables.

**ND11. React Router v7 with page-based route organization.**
```
src/pages/
  Dashboard.tsx
  Recipes.tsx
  Plan.tsx
  Stock.tsx
```

**ND12. All Supabase calls live in `src/services/` — never in components or hooks directly.**
Reason: testability and separation of concerns. Hooks consume stores, components consume hooks.
The `lib/supabase.ts` file holds the client initialization — imported only by services.

**ND16. Fisher-Yates shuffle for monthly plan generation.**
Reason: deterministic rotation (`(d-1) % recipes.length`) always produced the same plan. Shuffle runs before the loop so each call to `generate()` produces a different result.

**ND17. `maybeSingle()` instead of `single()` for Supabase queries that may return 0 rows.**
Reason: `single()` throws PGRST116 when no row exists. `maybeSingle()` returns null cleanly — correct for first-time users with no plan yet.

**ND18. `structuredClone()` before mutating stock JSONB objects.**
Reason: Zustand state is read-only. Direct mutation of nested objects causes stale references. `structuredClone` ensures a deep copy before any ingredient deduction.

**ND20. Zustand selectors in hooks — never subscribe to the whole store.**
Reason: subscribing to the whole store (`useStore()`) causes re-renders on any state change. Selectors (`useStore(s => s.field)`) scope the subscription and prevent infinite loops.

**ND21. `applyRecipe` searches all three stock categories sequentially.**
Reason: ingredient names are keys in three separate JSONB objects (proteins, carbs, condiments). There is no category metadata on the ingredient — the lookup must try all three.

---

## 4. WORKFLOW

**ND13. Issues live in `Mjosuex85/nutriapp` — monorepo, single issue tracker.**

**ND14. Branch strategy: `develop` → `main`. No staging branch for Phase 1.**
Reason: personal tool, single developer. Staging adds overhead without benefit at this scale.
Add staging branch when Phase 3 (multi-user) begins.

**ND15. Mario is both Director and sole developer for NutriApp Phase 1.**
Nestor and Olga do not work on this project — different stack (React, no NestJS).
Phase 1 is built by Mario with Orion as architect.

---

*Last updated: April 16, 2026 — Session 24*
