# Skill: JWT Auth — Backend

> Authentication patterns with JWT + Refresh Token.

---

## The full flow

```
Login → access token (short-lived) + refresh token in httpOnly cookie
       ↓
Requests with access token in Authorization: Bearer header
       ↓
Access token expires → interceptor calls /auth/refresh
       ↓
Backend reads cookie → validates refresh token → issues new tokens
       ↓
If refresh fails → logout → redirect to login
```

## Cookie configuration

```typescript
const cookieOptions = (isProduction: boolean) => ({
  httpOnly: true,
  path: '/',              // CRITICAL: '/' not '/auth/refresh'
  maxAge: 7 * 24 * 60 * 60 * 1000,
  sameSite: (isProduction ? 'none' : 'lax') as 'none' | 'lax',
  secure: isProduction,
});
```

## Frontend interceptor (Angular)

```typescript
// 401 handling lives ONLY in auth.interceptor — never in error.interceptor
// Exclude /auth/refresh from retry to avoid infinite loop
if (error.status === 401 && token && !req.url.includes('/auth/refresh')) {
  // attempt refresh
}
```

## Critical rules

- `cookie-parser` must be installed and configured in `main.ts`
- Cookie path must be `'/'` — not `'/auth/refresh'`
- Only ONE interceptor handles 401 — the auth interceptor
- Exclude `/auth/refresh` from retry to avoid infinite loops
- Refresh tokens are stored hashed in DB — never plain text
