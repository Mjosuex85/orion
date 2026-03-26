# Skill: ApiService — Frontend

> How to make HTTP calls in projects with a centralized ApiService.

---

## Fundamental rule

ALL HTTP calls go through `ApiService`. Never inject `HttpClient` directly in components.

---

## Using ApiService

```typescript
import { ApiService } from '@core/services/api.service';

@Component({ standalone: true })
export class MyComponent {
  private apiService = inject(ApiService);

  loadData() {
    // GET
    this.apiService.get<User[]>('/users').subscribe(...);

    // POST
    this.apiService.post<Match>('/matches', dto).subscribe(...);

    // PUT
    this.apiService.put<User>(`/users/${id}/profile`, data).subscribe(...);

    // DELETE
    this.apiService.delete(`/matches/${id}`).subscribe(...);
  }
}
```

## Base URL

Lives in `environment.ts` / `environment.prod.ts` — never hardcode URLs.

```typescript
// environment.ts (local)
export const environment = {
  apiUrl: 'http://localhost:3000'
};

// environment.prod.ts (production)
export const environment = {
  apiUrl: 'https://gameon-api.vercel.app'
};
```

## Rules

- Never use `HttpClient` directly in components
- Never hardcode URLs — always use `environment.apiUrl`
- Errors are handled globally by `error.interceptor`
- 401 is handled by `auth.interceptor` — not by the component
- `withCredentials: true` goes in the interceptor — not in each call
