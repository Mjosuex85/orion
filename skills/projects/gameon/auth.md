# Skill: Auth en GameOn — Específico de proyecto

> Cómo funciona la autenticación específicamente en GameOn.

---

## Stack de auth

- JWT access token: 15 minutos, en header Authorization
- JWT refresh token: 7 días, en cookie httpOnly
- Google OAuth: passport-google-oauth20
- Tokens hasheados en DB con bcrypt

## Entidades relevantes

```
User → tiene refreshTokens (OneToMany)
UserRefreshToken → hashedToken, sessionId, expiresAt, device, ipAddress
PasswordResetToken → token (hasheado), userId, expiresAt, used
```

## Variables de entorno necesarias

```
JWT_ACCESS_SECRET
JWT_REFRESH_SECRET
JWT_ACCESS_EXPIRES_IN=900       (15 minutos en segundos)
JWT_REFRESH_EXPIRES_IN=604800   (7 días en segundos)
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
GOOGLE_CALLBACK_URL=https://backend/auth/google/callback
FRONTEND_URL=https://frontend.vercel.app
```

## Problema conocido

Google OAuth en producción falla con `Refresh token is missing` — issue #66.
El refresh token con email/password funciona correctamente.

## Archivos clave

- `src/modules/auth/auth.controller.ts`
- `src/modules/auth/auth.service.ts`
- `src/modules/auth/strategies/google.strategy.ts`
- `src/modules/users/entities/user-refreshtoken.entity.ts`
