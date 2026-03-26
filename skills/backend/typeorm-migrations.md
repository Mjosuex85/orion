# Skill: TypeORM Migrations — Backend

> How to create and manage migrations in TypeORM projects.

---

## Fundamental rule

`synchronize: false` ALWAYS in production. Migrations control the schema.

---

## Create a migration

```bash
# 1. Build the project first
npm run build

# 2. Generate the migration
npx typeorm migration:generate src/migrations/DescriptiveName -d dist/database.config.js

# 3. Review the generated file BEFORE running it
```

## Migration structure

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

## Run migrations

```bash
# Local
npm run db:migrate

# Production (PowerShell)
$env:DATABASE_URL="your_url"; npm run db:migrate
```

## Critical rules

- Always implement `down()` to allow rollback
- Never modify a migration that already ran in production
- Migrations are version-controlled in the repo — never gitignore them
- Review generated SQL before running in production
- With `@DeleteDateColumn` never use `.where('deletedAt IS NULL')` manually
