# Skill: Angular Patterns — Frontend

> Core patterns for Angular 17+ projects under Orion OS.
> Stack: Angular 21 + TailwindCSS + Angular Signals.

---

## Project structure

```
src/app/
  core/           → interceptors, guards, services
  shared/
    ui/
      atoms/      → button, input, modal, loader, toast
      molecules/  → stat-card, match-row, empty-state
  features/       → one folder per feature
  app.routes.ts
```

## Component rules

- 100% standalone components — never NgModules
- `ChangeDetectionStrategy.OnPush` always (use the enum, never numeric value 0)
- HTML max 150 lines / TS max 200 lines / SCSS max 20kB
- SCSS partials in `styles/` folder inside the component
- Output signals: `output()` not `@Output() + EventEmitter`

## State — Angular Signals

```typescript
const count = signal(0);
const doubled = computed(() => count() * 2);
count.set(5);
console.log(count()); // always call as function
```

## Tailwind rules

- Use `[class.class-name]` bindings — NOT ternary `[class]="cond ? 'a' : 'b'"`
- Tailwind purges dynamic ternary strings in build
- All CSS uses design tokens from styles.scss — never hardcoded values

## HTTP

- All calls go through `ApiService`
- Backend URL in `environment.ts` / `environment.prod.ts`
- 401 handling in `auth.interceptor.ts` only
- Error handling: interceptor for global, component for business logic (D79)

## Icons

- Material Symbols via CDN: `<span class="material-symbols-outlined">icon_name</span>`
