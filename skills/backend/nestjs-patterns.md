# Skill: NestJS Patterns — Backend

> Reusable patterns for any NestJS project.

---

## Standard module structure

```
src/modules/name/
  ├── name.module.ts
  ├── name.controller.ts
  ├── name.service.ts
  ├── entities/
  │   └── name.entity.ts
  ├── dtos/
  │   ├── create-name.dto.ts
  │   └── update-name.dto.ts
  └── types/
      └── name.type.ts
```

## Dependency injection

```typescript
constructor(
  @InjectRepository(Entity)
  private readonly repo: Repository<Entity>,
  private readonly otherService: OtherService
) {}
```

## DTOs with validation

```typescript
import { IsString, IsNotEmpty, IsOptional } from 'class-validator';

export class CreateMatchDto {
  @IsString()
  @IsNotEmpty()
  title: string;

  @IsOptional()
  @IsString()
  description?: string;
}
```

## Guards

```typescript
// Always protect routes with JwtAuthGuard
@UseGuards(JwtAuthGuard)
@Get('profile')
async getProfile(@Req() req: AuthenticatedRequest) {
  return req.user;
}
```

## Rules

- Never use `any` without explicit justification
- Always type service return values
- Controllers only call services — never direct business logic
- Services never call other controllers
