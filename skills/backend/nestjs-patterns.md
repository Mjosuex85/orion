# Skill: NestJS Patterns — Backend

> Patrones reutilizables para cualquier proyecto NestJS.

---

## Estructura de módulo estándar

```
src/modules/nombre/
  ├── nombre.module.ts
  ├── nombre.controller.ts
  ├── nombre.service.ts
  ├── entities/
  │   └── nombre.entity.ts
  ├── dtos/
  │   ├── create-nombre.dto.ts
  │   └── update-nombre.dto.ts
  └── types/
      └── nombre.type.ts
```

## Inyección de dependencias

```typescript
// Siempre usar inject() en constructores
constructor(
  @InjectRepository(Entity)
  private readonly repo: Repository<Entity>,
  private readonly otherService: OtherService
) {}
```

## DTOs con validación

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
// Siempre proteger rutas con JwtAuthGuard
@UseGuards(JwtAuthGuard)
@Get('profile')
async getProfile(@Req() req: AuthenticatedRequest) {
  return req.user;
}
```

## Reglas

- Nunca usar `any` sin justificación explícita
- Siempre tipar los retornos de los servicios
- Los controllers solo llaman servicios — nunca lógica de negocio directa
- Los servicios nunca llaman a otros controllers
