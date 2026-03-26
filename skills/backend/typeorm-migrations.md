# Skill: TypeORM Migrations — Backend

> Cómo crear y manejar migraciones en proyectos con TypeORM.

---

## Regla fundamental

`synchronize: false` SIEMPRE en producción. Las migraciones controlan el schema.

---

## Crear una migración

```bash
# 1. Compilar el proyecto primero
npm run build

# 2. Generar la migración
npx typeorm migration:generate src/migrations/NombreDescriptivo -d dist/database.config.js

# 3. Revisar el archivo generado ANTES de correrla
```

## Estructura de una migración

```typescript
import { MigrationInterface, QueryRunner } from 'typeorm';

export class AddRoleToUser1774310400000 implements MigrationInterface {
  name = 'AddRoleToUser1774310400000';

  public async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`ALTER TABLE "users" ADD "role" varchar NOT NULL DEFAULT 'user'`);
  }

  public async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.query(`ALTER TABLE "users" DROP COLUMN "role"`);
  }
}
```

## Correr migraciones

```bash
# Local
npm run db:migrate

# Producción (PowerShell)
$env:DATABASE_URL="tu_url"; npm run db:migrate
```

## Reglas críticas

- Siempre implementar `down()` para poder revertir
- Nunca modificar una migración que ya fue ejecutada en producción
- Las migraciones van versionadas en el repo — nunca ignorarlas en `.gitignore`
- Revisar el SQL generado antes de ejecutar en producción
- Con `@DeleteDateColumn` nunca usar `.where('deletedAt IS NULL')` manual
