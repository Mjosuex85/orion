# Skill: Angular Signals — Frontend

> State management patterns with Signals in Angular 17+.

---

## Basic signals

```typescript
import { signal, computed, effect } from '@angular/core';

// Basic signal
const count = signal(0);
const doubled = computed(() => count() * 2);

// Update
count.set(5);
count.update(v => v + 1);

// Read — always with ()
console.log(count());
```

## In standalone components

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

## Rules

- Prefer `signal()` over plain properties for reactive state
- Use `computed()` for derived values — never recalculate in template
- Signals in templates always with `()`: `{{ user() }}` not `{{ user }}`
- To avoid `ExpressionChangedAfterChecked` with signals: no need for `ChangeDetectorRef`
- Project is 100% standalone — never use NgModules
