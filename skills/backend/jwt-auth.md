# Skill: JWT Auth — Backend

> Patrones de autenticación con JWT + Refresh Token.

---

## El flujo completo

```
Login → access token (corto) + refresh token en cookie httpOnly
       ↓
Requests con access token en header Authorization: Bearer
       ↓
Access token expira → interceptor llama /auth/refresh
       ↓
Backend lee cookie → valida refresh token → emite nuevos tokens
       ↓
Si refresh falla → logout → redirigir al login
```

## Configuración de cookie

```typescript
const cookieOptions = (isProduction: boolean) => ({
  httpOnly: true,
  path: '/',              // CRÍTICO: '/' no '/auth/refresh'
  maxAge: 7 * 24 * 60 * 60 * 1000,
  sameSite: (isProduction ? 'none' : 'lax') as 'none' | 'lax',
  secure: isProduction,
});
```

## Interceptor frontend (Angular)

```typescript
// El manejo del 401 vive SOLO en auth.interceptor — nunca en error.interceptor
// Excluir /auth/refresh del retry para evitar loop infinito
if (error.status === 401 && token && !req.url.includes('/auth/refresh')) {
  // intentar refresh
}
```

## Reglas críticas

- `cookie-parser` debe estar instalado y configurado en `main.ts`
- El path de la cookie debe ser `'/'` — no `/auth/refresh`
- Solo UN interceptor maneja el 401 — el auth interceptor
- Excluir `/auth/refresh` del retry para evitar loops infinitos
- Refresh tokens se guardan hasheados en DB — nunca en texto plano
