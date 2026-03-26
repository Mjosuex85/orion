# Skill: Migraciones en GameOn — Específico de proyecto

> Cómo crear y correr migraciones específicamente en GameOn.

---

## Flujo para crear una migración

```bash
# 1. Compilar
npm run build

# 2. Generar
npx typeorm migration:generate src/migrations/NombreDescriptivo -d dist/database.config.js

# 3. Revisar el archivo en src/migrations/
# 4. Compilar de nuevo para incluir la migración
npm run build

# 5. Correr en local
npm run db:migrate
```

## Correr en producción (OBLIGATORIO antes de cada deploy con migraciones)

```powershell
# PowerShell — con URL de Neon
$env:DATABASE_URL="postgresql://..."; npm run db:migrate
```

## Migraciones existentes en orden

1. `InitialSchema` — schema base
2. `AddIsFirstLoginToUser` — onboarding
3. `AddRoleToUser` — roles USER/ADMIN
4. `AddPriceToMatch` — precio de partidos
5. `CreateCitiesAndDistricts` — ciudades y distritos (Madrid por defecto)
6. `AddCityDistrictToMatch` — reemplaza columna `location` por `cityId` + `districtId` + `address`

## Reglas críticas en GameOn

- `migrationsRun: true` en producción pero NO es confiable en Vercel serverless
- Siempre correr migraciones manualmente antes del deploy
- NUNCA modificar migraciones que ya corrieron en producción
- El path de migraciones en `app.module.ts`: `__dirname + '/../migrations/*.js'`
- Ver issue #65 para el flujo completo documentado
