# Decisions — NutriApp

> Project-specific architecture decisions.
> Universal decisions live in DECISIONS.md.

---

## 1. TOPOLOGY & STACK

**ND1. Monorepo — single repo `Mjosuex85/nutriapp`.**
Reason: no dedicated backend, single developer, simpler deploy.

**ND2. No dedicated backend — Firebase SDK direct from frontend.**
Reason: the data model is simple and personal. Adding NestJS would be over-engineering for Phase 1.
Review at Phase 3 if multi-tenant features are needed.

**ND3. Frontend: React 18 + Vite.**
Reason: Mario learns React with a real project. Angular is GameOn's domain.

**ND4. State: Zustand over Context API.**
Reason: simpler API, less boilerplate, scales better than Context for this data model.

**ND5. Firebase Firestore for data, Firebase Auth for authentication.**
Reason: fast to bootstrap, real-time sync built-in, free tier sufficient for Phase 1-2.

**ND6. Deploy: Vercel.**
Reason: consistent with GameOn, Mario already knows the platform.

---

## 2. DATA MODEL

**ND7. Stock is a single Firestore document, not a collection.**
Reason: stock is always read/written as a whole. A collection would add unnecessary reads.

**ND8. `history` is append-only — never update or delete entries.**
Reason: audit trail of what was consumed. Corrections are new entries, not edits.

**ND9. `monthlyPlan` document ID format: `plan_YYYY_MM`.**
Reason: deterministic ID enables direct doc fetch without queries.

---

## 3. FRONTEND

**ND10. CSS Modules for component styles — no Tailwind in this project.**
Reason: React project, clean separation from GameOn's Tailwind setup. Mario explores CSS Modules.

**ND11. React Router v6 with file-based route organization.**
```
src/pages/
  Dashboard.tsx
  Recipes.tsx
  Plan.tsx
  Stock.tsx
```

**ND12. All Firestore calls live in `src/services/` — never in components or hooks directly.**
Reason: testability and separation of concerns. Hooks consume services, components consume hooks.

---

## 4. WORKFLOW

**ND13. Issues live in `Mjosuex85/nutriapp` — monorepo, single issue tracker.**

**ND14. Branch strategy: `develop` → `main`. No staging branch for Phase 1.**
Reason: personal tool, single developer. Staging adds overhead without benefit at this scale.
Add staging branch when Phase 3 (multi-user) begins.

**ND15. Mario is both Director and sole tester for NutriApp.**
Nestor and Olga do not work on this project — stack is different (React, no NestJS).
Phase 1 is built by Mario with Orion as architect.

---

*Last updated: April 15, 2026 — project initialization*
