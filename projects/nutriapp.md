# NutriApp — Project Context

> Technical context for Orion.
> Architecture decisions → `projects/nutriapp-decisions.md`.

---

## WHAT IS NUTRIAPP

NutriApp is a **personal food stock and meal planning tool**.
It lets Mario manage his ingredient stock, select daily meals from a recipe list, and automatically deduct ingredients when a meal is prepared — across a 30-day planning cycle.

**The pitch:** "Replaces manual tracking of food stock and weekly meal improvisation."

**Future potential:** Multi-user household tool or nutrition SaaS for coaches and athletes.

---

## TOPOLOGY

**Monorepo** — `Mjosuex85/nutriapp`
```
nutriapp/
  app/   → React 18 + Vite frontend
```
No dedicated backend — Supabase SDK used directly from frontend.

---

## STACK

```
Frontend:   React 18 + Vite
Styles:     CSS Modules + SCSS
State:      Zustand
Router:     React Router v6
Database:   Supabase (PostgreSQL)
Auth:       Supabase Auth (Phase 2 — not needed in Phase 1)
Deploy:     Vercel
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

### `stock` (single row per user)
- id, user_id (Phase 2), proteins JSONB, carbohydrates JSONB, condiments JSONB, updated_at

### `recipes` (15 rows)
- id, name, category (breakfast / lunch_dinner)
- ingredients JSONB: { ingredient_id: { amount, unit } }
- instructions, servings, calories, protein_g, carbs_g

### `monthly_plan` (one row per month)
- id, month, year
- meals JSONB: [{ day, date, breakfast_lunch, dinner_snack, status }]
- status: pending | completed | skipped

### `history` (append-only)
- id, date, recipe_id, ingredients_used JSONB, created_at

---

## FEATURES

### Dashboard
- Stock cards: proteins / carbs / condiments
- Alert if ingredient < 100g
- Weekly consumption chart
- CTA: "Ver Plan" | "Cocinar Hoy"

### Recipes
- 15 recipe cards
- Detail modal: ingredients, calories, macros, instructions
- "Usar esta receta" → adds to plan

### Monthly Plan
- 30-day calendar view
- Click day → select 2 recipes (breakfast + dinner)
- Confirm → deducts stock + logs to history
- Drag & drop between days

### Stock
- Detailed table/cards per ingredient
- kg + g dual display
- % consumed of total

---

## BUSINESS RULES

- Marking a meal as "completed" deducts ingredients from stock automatically
- Alert threshold: < 100g protein or carb triggers visual warning
- Monthly reset: option to reset plan on day 31 (keep remaining stock)
- Offline fallback: localStorage if Supabase unavailable

---

## INITIAL STOCK DATA

Two purchases pre-loaded (98.43€ total):
- Proteins: pechuga 3.711kg, burgers 2kg, jamoncitos 0.896kg, huevos 12u, quesos ~835g
- Carbs: arroz 2kg, pasta 1kg, harina 2kg, pan (hamburguesa 4u, sandwich 27u, rallado 1kg)
- Condiments: aceite 1L, salsas, mantequilla, verduras frescas, especias

Full stock spec in `projects/nutriapp-stock.md` (pending).

---

## STATUS — April 15, 2026

```
Repo created:        Mjosuex85/nutriapp ✅
Orion OS bootstrap:  in progress
Supabase project:    pending
App scaffold:        pending
First working route: pending
```

**Active priorities:**
- Bootstrap Orion OS files in nutriapp repo
- Create Supabase project + schema
- Initialize React + Vite scaffold
- Issue #1: project scaffold + Supabase connection + health check

---

## TARGET USERS

**Phase 1 — Personal:** Mario. Single user, no auth needed.
**Phase 2 — Household:** Multi-user with Supabase Auth.
**Phase 3 — Product:** Nutrition coaches managing client meal plans.

---

*Part of Orion OS v1.5.0 — initialized April 15, 2026*
