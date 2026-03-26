# Skill: Migrations in GameOn — Project Specific

> How to create and run migrations specifically in GameOn.

---

## Flow to create a migration

```bash
# 1. Build
npm run build

# 2. Generate
npx typeorm migration:generate src/migrations/DescriptiveName -d dist/database.config.js

# 3. Review the file in src/migrations/
# 4. Build again to include the migration
npm run build

# 5. Run locally
npm run db:migrate
```

## Run in production (MANDATORY before each deploy with migrations)

```powershell
# PowerShell — with Neon URL
$env:DATABASE_URL="postgresql://..."; npm run db:migrate
```

## Existing migrations in order

1. `InitialSchema` — base schema
2. `AddIsFirstLoginToUser` — onboarding
3. `AddRoleToUser` — USER/ADMIN roles
4. `AddPriceToMatch` — match pricing
5. `CreateCitiesAndDistricts` — cities and districts (Madrid as default)
6. `AddCityDistrictToMatch` — replaces `location` column with `cityId` + `districtId` + `address`

## Critical rules in GameOn

- `migrationsRun: true` in production but NOT reliable in Vercel serverless
- Always run migrations manually before deploying
- NEVER modify migrations that already ran in production
- Migrations path in `app.module.ts`: `__dirname + '/../migrations/*.js'`
- See issue #65 for the full documented flow
