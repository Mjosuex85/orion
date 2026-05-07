# NutriApp — Project Context

> Technical context for Orion.
> Architecture decisions → `projects/nutriapp/nutriapp-decisions.md`.

---

## WHAT IS NUTRIAPP

NutriApp is a **personal food stock and meal planning tool**.
It lets Mario manage his ingredient stock, select daily meals from a recipe list, and automatically deduct ingredients when a meal is prepared — across a 30-day planning cycle.

**The pitch:** "Replaces manual tracking of food stock and weekly meal improvisation."

**Future potential:** Multi-user household tool or nutrition SaaS for coaches and athletes.

---

## ISSUES REPO: Mjosuex85/nutriapp
## KANBAN:      GitHub Projects > NutriApp (account level)

Columns: Backlog → Ready → In progress → In review → Done

---

## TOPOLOGY

**Monorepo** — `Mjosuex85/nutriapp`
```
nutriapp/
  app/   → React 19 + Vite frontend
  supabase/migrations/   → SQL migrations
```
No dedicated backend — Supabase SDK used directly from frontend.

---

## STACK

```
Frontend:   React 19 + Vite + TypeScript
Styles:     CSS Modules (no Tailwind, no inline styles)
State:      Zustand
Router:     React Router v7
Database:   Supabase (PostgreSQL + JSONB)
Auth:       Supabase Auth (Phase 2 — not needed in Phase 1)
Deploy:     Vercel (pending)
```

---

## SKILLS

```yaml
skills:
  - skills/universal/git-flow.md
  - skills/frontend/react-patterns.md
```

---

## DATA MODEL

### `stock` (single row)
- id, proteins JSONB, carbohydrates JSONB, condiments JSONB, updated_at
- JSONB shape: `{ ingredient_name: { amount: number, unit: string } }`
- Low stock threshold: <100 for g/ml units, ≤1 for bote/spray/unidad

### `recipes`
- id, name, category (`breakfast` | `lunch_dinner`)
- ingredients JSONB: `{ ingredient_name: { amount, unit } }`
- instructions, servings, calories, protein_g, carbs_g, created_at

### `monthly_plans`
- id, month, year, meals JSONB, created_at
- UNIQUE (month, year) — upserted on save
- meals shape: `[{ day, date, breakfast, lunch_dinner, status, breakfast_done?, lunch_dinner_done? }]`
- status: `pending` | `completed` | `skipped`

### `history` (not yet built)
- id, date, recipe_id, ingredients_used JSONB, created_at

---

## FEATURES (as of April 15, 2026)

### Dashboard ✅
### Recipes ✅
### Monthly Plan ✅
### Stock ✅

---

## KEY ARCHITECTURE RULES

- No inline styles. No Tailwind. CSS Modules only.
- Supabase calls only in `services/` layer.
- Hooks consume Zustand stores via selectors.
- `maybeSingle()` for queries that may return 0 or 1 rows.
- `structuredClone()` before mutating stock objects.
- CSS variables design system in `index.css`.

---

## PENDING / DEFERRED

- Historial page: not yet designed.
- Stock camera feature: future.
- Auth / sign out: end of Phase 1.
- Recipe add/edit from UI: read-only currently.
- Vercel deploy: pending.
- Multi-user (Phase 2): Supabase Auth.

---

## STATUS — April 15, 2026

```
Repo:                   Mjosuex85/nutriapp ✅
All migrations run:     ✅
Dashboard:              ✅
Recipes page:           ✅
Monthly Plan page:      ✅
Stock page:             ✅
Vercel deploy:          ⬜ pending
Auth / sign out:        ⬜ deferred
```

---

*Part of Orion OS v2.0.0 — updated Session 36 (modular folder structure)*
