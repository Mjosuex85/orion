# Skill: Angular Signals — Frontend

> Patrones de state management con Signals en Angular 17+.

---

## Signals básicos

```typescript
import { signal, computed, effect } from '@angular/core';

// Signal básico
const count = signal(0);
const doubled = computed(() => count() * 2);

// Actualizar
count.set(5);
count.update(v => v + 1);

// Leer
console.log(count()); // siempre con ()
```

## En componentes standalone

```typescript
@Component({
  selector: 'app-profile',
  standalone: true,
  imports: [CommonModule],
  template: `<p>{{ user().name }}</p>`
})
export class ProfileComponent {
  user = signal<User | null>(null);
  isLoading = signal(false);

  loadUser() {
    this.isLoading.set(true);
    this.userService.getUser().subscribe({
      next: (u) => {
        this.user.set(u);
        this.isLoading.set(false);
      }
    });
  }
}
```

## Reglas

- Preferir `signal()` sobre propiedades normales para estado reactivo
- Usar `computed()` para valores derivados — nunca recalcular en template
- Los signals en template siempre con `()`: `{{ user() }}` no `{{ user }}`
- Para evitar `ExpressionChangedAfterChecked` con signals: no necesitas `ChangeDetectorRef`
- Proyecto es 100% standalone — nunca usar NgModules
