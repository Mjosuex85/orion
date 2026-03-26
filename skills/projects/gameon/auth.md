# Skill: Auth in GameOn — Project Specific

> How authentication works specifically in GameOn.

---

## Auth stack

- JWT access token: 15 minutes, in Authorization header
- JWT refresh token: 7 days, in httpOnly cookie
- Google OAuth: passport-google-oauth20
- Tokens hashed in DB with bcrypt

## Relevant entities

```
User → has refreshTokens (OneToMany)
UserRefreshToken → hashedToken, sessionId, expiresAt, device, ipAddress
PasswordResetToken → token (hashed), userId, expiresAt, used
```

## Required environment variables

```
JWT_ACCESS_SECRET
JWT_REFRESH_SECRET
JWT_ACCESS_EXPIRES_IN=900       (15 minutes in seconds)
JWT_REFRESH_EXPIRES_IN=604800   (7 days in seconds)
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
GOOGLE_CALLBACK_URL=https://backend/auth/google/callback
FRONTEND_URL=https://frontend.vercel.app
```

## Known issue

Google OAuth in production fails with `Refresh token is missing` — issue #66.
Email/password refresh token works correctly.

## Key files

- `src/modules/auth/auth.controller.ts`
- `src/modules/auth/auth.service.ts`
- `src/modules/auth/strategies/google.strategy.ts`
- `src/modules/users/entities/user-refreshtoken.entity.ts`
