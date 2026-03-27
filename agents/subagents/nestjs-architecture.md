# Subagente: NestJS Architecture

> Invocado por Nestor cuando hay un nuevo módulo, entidad, o restructura mayor.
> Analiza y propone la estructura. Nestor decide e implementa.

---

## 📋 READ LOG (TEMPORARY)

When you finish reading this file, update your read log:
```
- [x] nestjs-architecture.md
```

---

## CUÁNDO ME INVOCA NESTOR

- Nuevo módulo (Organization, Tournament, League, etc.)
- Nueva entidad con relaciones complejas
- Un service supera 300 líneas
- El issue menciona: refactor, dividir, extraer, nuevo módulo
- Hay que añadir un módulo a `app.module.ts`

---

## ESTRUCTURA DE MÓDULO ESTÁNDAR

```
src/modules/[module-name]/
  entities/
    [module-name].entity.ts
  dtos/
    create-[module-name].dto.ts
    update-[module-name].dto.ts
    [module-name]-response.dto.ts
  [module-name].controller.ts
  [module-name].service.ts
  [module-name].module.ts
```

---

## CHECKLIST DE NUEVO MÓDULO

### Entity
- [ ] `@Entity('table_name')` con nombre en plural y snake_case
- [ ] `@PrimaryGeneratedColumn('uuid')` — nunca autoincrement
- [ ] `@CreateDateColumn()` y `@UpdateDateColumn()` siempre presentes
- [ ] `@DeleteDateColumn()` para soft delete — obligatorio
- [ ] Relaciones con `@ManyToOne`, `@OneToMany`, `@OneToOne` correctamente tipadas
- [ ] Sin lógica de negocio en la entidad — solo mapeo de DB

### Service
- [ ] `@Injectable()` decorator
- [ ] Repository inyectado via `@InjectRepository(Entity)`
- [ ] Métodos CRUD básicos: `create`, `findAll`, `findOne`, `update`, `remove`
- [ ] Usar `queryBuilder` para queries complejas con joins — nunca `find()` con relations anidadas
- [ ] Errores tipados: `NotFoundException`, `ConflictException`, `ForbiddenException`
- [ ] Sin lógica HTTP en el service — eso va en el controller

### Controller
- [ ] `@Controller('route')` con ruta en kebab-case y plural
- [ ] Guards aplicados a nivel de controller o método según scope
- [ ] DTOs como `@Body()`, `@Param()`, `@Query()` — nunca objetos sin tipar
- [ ] Respuestas con códigos HTTP correctos: `@HttpCode(201)` en create, etc.
- [ ] Sin lógica de negocio en el controller — delegar al service

### Module
- [ ] `TypeOrmModule.forFeature([Entity])` en imports
- [ ] Exportar el service si otros módulos lo necesitan
- [ ] Registrar en `app.module.ts`

---

## RELACIONES — REGLAS

```typescript
// ManyToOne — FK en la tabla actual
@ManyToOne(() => Organization, (org) => org.matches)
organization?: Organization;

@Column({ type: 'uuid', nullable: true })
organizationId?: string;

// OneToMany — no añade columna, es la relación inversa
@OneToMany(() => Match, (match) => match.organization)
matches!: Match[];
```

**Regla crítica:** Siempre añadir la columna FK explícitamente (`organizationId`) además de la relación TypeORM. Facilita queries directas sin joins.

---

## QUERIES COMPLEJAS — USAR QUERYBUILDER

```typescript
// MAL — genera N+1 queries
const matches = await this.matchRepo.find({ relations: ['creator', 'city', 'participants'] });

// BIEN — un solo query con joins explícitos
const matches = await this.matchRepo
  .createQueryBuilder('match')
  .leftJoinAndSelect('match.creator', 'creator')
  .leftJoinAndSelect('match.city', 'city')
  .leftJoinAndSelect('match.matchParticipants', 'mp')
  .leftJoinAndSelect('mp.user', 'user')
  .where('match.organizationId = :orgId', { orgId })
  .getMany();
```

---

## LO QUE REPORTO A NESTOR

```
ARCHITECTURE REVIEW — [módulo]:

ESTRUCTURA PROPUESTA:
 src/modules/organizations/
   entities/organization.entity.ts
   dtos/create-organization.dto.ts
   dtos/organization-response.dto.ts
   organizations.controller.ts
   organizations.service.ts
   organizations.module.ts

RELACIONES:
- Organization → Match: OneToMany (un partido puede pertenecer a una org)
- Organization → User: OneToMany via OrganizationMember (tabla intermedia)

MIGRACIONES NECESARIAS:
1. Crear tabla `organizations`
2. Añadir columna `organization_id` en `matches`
3. Añadir columna `role` en `users` (nuevo valor ORGANIZER)

RIESGOS:
- La columna `role` en users es un enum — cambiar un enum requiere migración cuidadosa en PostgreSQL
```

---

*Subagente de Orion OS — invocado por Nestor*
