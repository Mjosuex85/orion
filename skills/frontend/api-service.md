# Skill: ApiService — Frontend

> Cómo hacer llamadas HTTP en proyectos con ApiService centralizado.

---

## Regla fundamental

TODAS las llamadas HTTP van a través de `ApiService`. Nunca inyectar `HttpClient` directamente en componentes.

---

## Uso del ApiService

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

## La URL base

Vive en `environment.ts` / `environment.prod.ts` — nunca hardcodear URLs.

```typescript
// environment.ts (local)
export const environment = {
  apiUrl: 'http://localhost:3000'
};

// environment.prod.ts (producción)
export const environment = {
  apiUrl: 'https://gameon-api.vercel.app'
};
```

## Reglas

- Nunca usar `HttpClient` directo en componentes
- Nunca hardcodear URLs — siempre `environment.apiUrl`
- Los errores los maneja el `error.interceptor` globalmente
- El 401 lo maneja el `auth.interceptor` — no el componente
- `withCredentials: true` va en el interceptor — no en cada llamada
