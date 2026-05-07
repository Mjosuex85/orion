# Decisions — NutriApp

> Project-specific architecture decisions.
> Universal decisions live in DECISIONS.md.

---

## 1. TOPOLOGY & STACK

**N-D1. Monorepo — single repo `Mjosuex85/nutriapp`.**

**N-D2. No dedicated backend — Supabase SDK direct from frontend.**
Review at Phase 3 if multi-tenant or complex server logic is needed.

**N-D3. Frontend: React 19 + Vite + TypeScript.**
Mario learns React with a real project. Angular is GameOn's domain.

**N-D4. State: Zustand over Context API.**

**N-D5. Database: Supabase (PostgreSQL). Auth: Supabase Auth (Phase 2 only).**

**N-D6. Deploy: Vercel.**

---

## 2. DATA MODEL

**N-D7. Stock is a single Supabase row with JSONB columns.**

**N-D8. `history` is append-only — never update or delete entries.**

**N-D9. `monthly_plans` row is unique per month+year — upserted, never inserted.**

**N-D19. Done flags stored inside JSONB `meals` array — no schema change.**

---

## 3. FRONTEND

**N-D10. CSS Modules — no Tailwind, no inline styles.**

**N-D11. React Router v7 with page-based route organization.**

**N-D12. All Supabase calls live in `src/services/` — never in components directly.**

**N-D16. Fisher-Yates shuffle for monthly plan generation.**

**N-D17. `maybeSingle()` instead of `single()` for queries that may return 0 rows.**

**N-D18. `structuredClone()` before mutating stock JSONB objects.**

**N-D20. Zustand selectors in hooks — never subscribe to the whole store.**

**N-D21. `applyRecipe` searches all three stock categories sequentially.**

---

## 4. WORKFLOW

**N-D13. Issues live in `Mjosuex85/nutriapp` — monorepo, single issue tracker.**

**N-D14. Branch strategy: `develop` → `main`. No staging branch for Phase 1.**

**N-D15. Mario is both Director and sole developer for NutriApp Phase 1.**
Nestor and Olga do not work on this project.

---

*Last updated: Session 36 (modular folder structure)*
