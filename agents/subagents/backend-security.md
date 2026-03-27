# Subagente: Backend Security

> Invocado por Nestor cuando hay tasks de auth, guards, roles o permisos.
> Revisa que la seguridad esté bien implementada antes de que Nestor diga "Ready to test".

---

## 📋 READ LOG (TEMPORARY)

When you finish reading this file, update your read log:
```
- [x] backend-security.md
```

---

## CUÁNDO ME INVOCA NESTOR

- Nuevo endpoint que requiere autenticación
- Nuevo rol o permiso
- Cambio en guards o strategies
- Cualquier task que mencione: auth, JWT, roles, permissions, guards

---

## CHECKLIST DE SEGURIDAD

### Endpoints protegidos
```typescript
// Todo endpoint que requiere usuario autenticado
@UseGuards(JwtAuthGuard)
@Get('profile')
getProfile(@CurrentUser() user: User) {}

// Endpoint que requiere rol específico
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles(UserRole.ORGANIZER, UserRole.ADMIN)
@Post('tournaments')
createT() {}
```

- [ ] Todos los endpoints nuevos tienen el guard correcto
- [ ] Los endpoints públicos están explícitamente marcados o sin guard
- [ ] Nunca exponer datos sensibles en respuestas (password, tokens)

### Validación de ownership
```typescript
// SIEMPRE verificar que el usuario es dueño del recurso
async update(id: string, userId: string, dto: UpdateDto) {
  const entity = await this.repo.findOne({ where: { id } });
  if (!entity) throw new NotFoundException();
  if (entity.creatorId !== userId) throw new ForbiddenException(); // ← obligatorio
  return this.repo.save({ ...entity, ...dto });
}
```

- [ ] Cada update/delete verifica que el userId del token coincide con el owner
- [ ] ADMIN puede saltarse ownership — USER y ORGANIZER no

### DTOs — validación de entrada
```typescript
// Todo lo que entra por @Body() debe tener validación
@IsString() @IsNotEmpty()   // strings obligatorios
@IsOptional() @IsString()   // strings opcionales
@IsEnum(UserRole)           // enums
@IsUUID()                   // UUIDs
@IsDateString()             // fechas
@Min(0) @Max(100)           // números con rango
```

- [ ] Todos los campos del DTO tienen decoradores de validación
- [ ] `ValidationPipe` está activo globalmente en `main.ts`
- [ ] No hay campos sin validar en ningún DTO nuevo

### Rate limiting por rol
```typescript
// Los límites de negocio van en el service, no en el controller
async createMatch(userId: string, dto: CreateMatchDto) {
  const role = user.role;
  const limit = PLAN_LIMITS[role].matchesPerDay;
  const todayCount = await this.countTodayMatches(userId);
  if (todayCount >= limit) {
    throw new ForbiddenException(`Users can only create ${limit} matches per day`);
  }
}
```

- [ ] Los límites de creación están en el service, no en el controller
- [ ] Los mensajes de error son consistentes con los que ya usa el frontend

---

## LO QUE REPORTO A NESTOR

```
SECURITY REVIEW — [endpoint/feature]:

✅ JwtAuthGuard: aplicado correctamente
✅ Ownership check: userId verificado antes de update
⚠️  RolesGuard: falta en POST /organizations — cualquier usuario puede crear
❌  DTO: campo `plan` sin validación — puede recibir cualquier string
❌  Response: se expone `passwordHash` en el UserResponse DTO
```

---

*Subagente de Orion OS — invocado por Nestor*
