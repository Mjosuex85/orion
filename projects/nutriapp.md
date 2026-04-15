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
- `breakfast_done` / `lunch_dinner_done`: boolean flags set when user cooks a meal (persisted)

### `history` (not yet built)
- id, date, recipe_id, ingredients_used JSONB, created_at

---

## FEATURES (as of April 15, 2026)

### Dashboard ✅
- Today's meal proposals (breakfast + lunch/dinner from current plan)
- Stock summary cards with low-stock alerts (amber highlight)
- MealActionSheet accessible from today's meals
- "Crear menú" flow with ConfirmModal — deducts stock + marks meal done

### Recipes ✅
- Cards with category badges (amber = Desayuno, green = Almuerzo & Cena)
- RecipeModal: colored sticky header (amber/green per category), ingredients, macros, instructions
- "Crear menú" button inside modal only when opened from Plan/Dashboard (not from Recipes page)

### Monthly Plan ✅
- Three views: Hoy / Semana / Mes
- Week view with prev/next navigation (week offset)
- Month grid with amber (breakfast) / green (lunch) chips
- Click chip → MealActionSheet (Ver receta / Crear menú / Cambiar menú del día)
- Click day cell → DayEditModal to change recipes
- Auto-generate: Fisher-Yates shuffle, different result each call
- ConfirmModal for Generate and Save actions
- Saved to Supabase via upsert — persists across sessions
- Done meals show strikethrough chip + ✓ prefix

### Stock ✅
- Sections: Proteínas / Carbohidratos / Condimentos
- StockCard with kg/g dual display, low-stock detection
- Deducted automatically when a meal is cooked

---

## KEY ARCHITECTURE RULES

- No inline styles. No Tailwind. CSS Modules only.
- Supabase calls only in `services/` layer.
- Hooks consume Zustand stores via selectors (avoid infinite re-renders).
- `maybeSingle()` for queries that may return 0 or 1 rows.
- `structuredClone()` before mutating stock objects.
- CSS variables design system defined in `index.css` (`--color-primary`, `--color-surface`, etc.).
- Mobile: bottom nav `position: fixed`, `z-index: 9999`, `env(safe-area-inset-bottom)` for notch.
- Desktop: top nav shown via media query `≥768px`.

---

## PENDING / DEFERRED

- **Historial page**: Log of cooked meals (recipe, date, ingredients used). Not yet designed with Mario.
- **Stock camera feature**: Open camera, photo of shopping list, auto-updates stock. Future.
- **Auth / sign out**: Planned for end of Phase 1.
- **Recipe add/edit from UI**: Currently recipes are read-only from the app.
- **Vercel deploy**: Not yet done.
- **Multi-user (Phase 2)**: Supabase Auth, user_id on all tables.

---

## MIGRATIONS RUN

```
001_stock.sql         ✅ run
002_recipes.sql       ✅ run (15 recipes seeded)
003_monthly_plans.sql ✅ run (Mario confirmed April 15, 2026)
```

---

## STATUS — April 15, 2026

```
Repo:                   Mjosuex85/nutriapp ✅
Branch develop:         ✅ (active branch)
Supabase project:       ✅ hvvcqxdwbbgdxewbgrij.supabase.co
All migrations run:     ✅
Dashboard:              ✅ with today's meals + cook flow
Recipes page:           ✅ with modal + cook button
Monthly Plan page:      ✅ Hoy/Semana/Mes + generate + save + cook
Stock page:             ✅
UI redesign:            ✅ design system, vibrant colors, fixed bottom nav
MealActionSheet:        ✅ redesigned (gradient cook button, handle pill)
ConfirmModal:           ✅ used for generate/save/cook actions
RecipeModal:            ✅ colored header, macros, cook button
Vercel deploy:          ⬜ pending
Auth / sign out:        ⬜ deferred
Historial page:         ⬜ deferred
```

---

## TARGET USERS

**Phase 1 — Personal:** Mario. Single user, no auth needed.
**Phase 2 — Household:** Multi-user with Supabase Auth.
**Phase 3 — Product:** Nutrition coaches managing client meal plans.

---

*Part of Orion OS v1.5.0 — last updated April 15, 2026*
