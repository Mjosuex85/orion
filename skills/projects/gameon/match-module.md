# Skill: Módulo de Partidos (Matches) en GameOn

> Cómo funciona el módulo central de GameOn.

---

## Entidades

```
Match
  ├── id, title, description
  ├── dateTime, price
  ├── maxPlayers, cancellationWindowHours
  ├── cityId (FK → cities), districtId (FK → districts), address
  ├── creator (FK → users)
  └── matchParticipants (OneToMany → MatchParticipant)

MatchParticipant (entidad intermedia — NO ManyToMany simple)
  ├── id
  ├── position (índice 0-based del spot en el campo)
  ├── registeredAt
  ├── match (FK → Match)
  └── user (FK → User)
```

## Reglas de negocio críticas

- Máximo 3 partidos por día por usuario
- No crear partidos en el pasado
- No unirse a un partido lleno
- Ventana de cancelación: `cancellationWindowHours` antes del partido
- Solo el creador puede editar/eliminar su partido
- `position` es el índice del spot — fallback por orden de array si es `null`

## Por qué MatchParticipant es entidad propia

Necesitamos `position` y `registeredAt` como campos propios.
ManyToMany simple no permite esto. No revertir a ManyToMany.

## Nombre del módulo

El módulo se llama `matchs` (con s) — no cambiar aunque parezca un typo.
Hay referencias en toda la codebase.

## Con @DeleteDateColumn

Nunca usar `.where('deletedAt IS NULL')` manual en createQueryBuilder.
TypeORM filtra los soft-deleted automáticamente.
