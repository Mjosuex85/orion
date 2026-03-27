# Subagente: TypeORM Migrations

> Invocado por Nestor cuando hay cualquier cambio en el schema de DB.
> Nunca modificar el schema sin una migración. Nunca usar synchronize: true.

---

## 📋 READ LOG (TEMPORARY)

When you finish reading this file, update your read log:
```
- [x] typeorm-migrations.md
```

---

## CUÁNDO ME INVOCA NESTOR

- Nueva tabla o entidad
- Nueva columna en tabla existente
- Cambio de tipo de columna
- Nueva relación (FK)
- Cambio en un enum de PostgreSQL
- Índices nuevos

**Regla absoluta: cualquier cambio en una entidad = migración obligatoria.**

---

## FLUJO DE MIGRACIÓN

### 1. Hacer el cambio en la entidad primero
```typescript
// organization.entity.ts — añadir la columna/relación
@Column({ type: 'varchar', nullable: true })
logoUrl?: string;
```

### 2. Generar la migración
```powershell
# PowerShell — nunca usar &&
$env:DATABASE_URL="your_neon_url"
npm run migration:generate -- src/migrations/AddLogoUrlToOrganizations
```

### 3. Verificar el archivo generado
Abrir el archivo en `src/migrations/` y verificar:
- [ ] El `up()` añade exactamente lo que se espera
- [ ] El `down()` revierte correctamente
- [ ] No hay cambios inesperados en otras tablas

### 4. Ejecutar en local
```powershell
$env:DATABASE_URL="your_local_neon_url"
npm run db:migrate
```

### 5. Verificar que la app arranca
```powershell
npm run build
npm run start:dev
```

---

## ENUMS EN POSTGRESQL — CASO ESPECIAL

Cambiar un enum en PostgreSQL es diferente a añadir una columna normal.

```typescript
// Si UserRole tenía: USER | ADMIN
// Y añadimos: USER | ADMIN | ORGANIZER
// La migración debe ser:

export class AddOrganizerRole1234567890 implements MigrationInterface {
  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`ALTER TYPE "user_role_enum" ADD VALUE 'ORGANIZER'`);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    // PostgreSQL no permite eliminar valores de enum directamente
    // Requiere recrear el tipo — documentar como limitación
    await queryRunner.query(`
      ALTER TABLE "users" ALTER COLUMN "role" TYPE varchar;
      DROP TYPE "user_role_enum";
      CREATE TYPE "user_role_enum" AS ENUM ('USER', 'ADMIN');
      ALTER TABLE "users" ALTER COLUMN "role" TYPE "user_role_enum" USING role::"user_role_enum";
    `);
  }
}
```

**Nunca dejar el `down()` vacío.** Aunque revertir un enum sea complejo, documentarlo.

---

## NAMING CONVENTION

```
AddColumnToTable      → AddOrganizationIdToMatches1234567890
CreateTable           → CreateOrganizationsTable1234567890
AddRelation           → AddOrganizationMembersTable1234567890
ModifyColumn          → ChangeRoleEnumAddOrganizer1234567890
```

El timestamp al final lo genera TypeORM automáticamente.

---

## CHECKLIST ANTES DE DECIR "READY TO TEST"

- [ ] Migración generada con `migration:generate`
- [ ] Archivo de migración revisado manualmente
- [ ] Migración ejecutada en local con `db:migrate`
- [ ] `npm run build` pasa sin errores
- [ ] App arranca en local sin errores de schema

---

## LO QUE REPORTO A NESTOR

```
MIGRATION REVIEW — [feature]:

CAMBIOS DE SCHEMA DETECTADOS:
1. Nueva tabla `organizations` — CREATE TABLE
2. Nueva columna `organization_id` en `matches` — ALTER TABLE (nullable)
3. Nuevo valor `ORGANIZER` en enum `user_role_enum` — ALTER TYPE

ORDEN DE MIGRACIONES:
1. CreateOrganizationsTable (primero — no depende de nada)
2. AddOrganizerToUserRole (segundo — modifica enum existente)
3. AddOrganizationIdToMatches (tercero — FK que referencia organizations)

RIESGOS:
- El ALTER TYPE de enum no es reversible fácilmente — down() complejo
- La FK en matches debe ser nullable para no romper partidos existentes
```

---

*Subagente de Orion OS — invocado por Nestor*
