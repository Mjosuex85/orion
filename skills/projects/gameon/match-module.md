# Skill: Match Module in GameOn — Project Specific

> How GameOn's core module works.

---

## Entities

```
Match
  ├── id, title, description
  ├── dateTime, price
  ├── maxPlayers, cancellationWindowHours
  ├── cityId (FK → cities), districtId (FK → districts), address
  ├── creator (FK → users)
  └── matchParticipants (OneToMany → MatchParticipant)

MatchParticipant (intermediate entity — NOT simple ManyToMany)
  ├── id
  ├── position (0-based index of spot on the field)
  ├── registeredAt
  ├── match (FK → Match)
  └── user (FK → User)
```

## Critical business rules

- Maximum 3 matches per day per user
- Cannot create matches in the past
- Cannot join a full match
- Cancellation window: `cancellationWindowHours` before the match
- Only the creator can edit/delete their match
- `position` is the spot index — fallback by array order if `null`

## Why MatchParticipant is its own entity

We need `position` and `registeredAt` as own fields.
Simple ManyToMany doesn't allow this. Do not revert to ManyToMany.

## Module name

The module is called `matchs` (with s) — do not rename even if it looks like a typo.
There are references throughout the codebase.

## With @DeleteDateColumn

Never use `.where('deletedAt IS NULL')` manually in createQueryBuilder.
TypeORM filters soft-deleted records automatically.
