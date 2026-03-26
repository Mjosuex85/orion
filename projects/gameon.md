# GameOn — Project Context

> This file gives Orion and agents the specific context for GameOn.
> GameOn is the lab project where Orion OS is tested and refined.

---

## WHAT IS GAMEON

GameOn is a **player identity platform** for amateur sports. The differentiator is not managing matches — it's that when someone touches your spot on the field, your FIFA card appears with your photo, country, history, and stats.

**The right pitch:** "Replaces WhatsApp and paper for organizing matches."

### The two business layers
```
Free layer (social hook)
  └ Create matches, profile, tactical field, FIFA card

Paid layer (monetization)
  └ Organizers/companies manage leagues and tournaments
  └ Players unlock real stats
```

---

## STACK

### Backend — `Mjosuex85/gameon-api`
```
Runtime:    Node.js + NestJS 10
ORM:        TypeORM + PostgreSQL (Neon)
Auth:       JWT (access 15min, refresh 7d) + Google OAuth
Deploy:     Vercel (serverless)
DB:         Neon PostgreSQL (production)
DB local:   Docker port 5434
```

### Frontend — `Mjosuex85/gameon`
```
Framework:  Angular 17+ (100% standalone components)
Styles:     TailwindCSS + SCSS
State:      Angular Signals
Deploy:     Vercel
```

---

## GAMEON-SPECIFIC RULES

- All issues live in `gameon-api` — including frontend ones
- GitHub Project: "GameOn" — Kanban with Todo / In Progress / Done
- Migrations must be run manually before each deploy (see issue #65)
- `migrationsRun: false` locally, `true` in production (not reliable in serverless — run manually)
- Migration command in production (PowerShell):
  ```powershell
  $env:DATABASE_URL="your_neon_url"; npm run db:migrate
  ```

---

## STATUS — March 26, 2026

- ✅ Backend in production (Vercel)
- ✅ Frontend in production (Vercel)
- ✅ Migrations executed (roles, cities, price)
- ✅ Refresh token working with email/password
- 🔴 Google OAuth refresh token fails in production (issue #66)
- 🔴 admin.component.scss oversized (issue #64)

---

## FIRST TARGET USER

Football community organizer in Madrid.
Currently uses WhatsApp + paper. GameOn replaces that.

---

*Part of Orion OS — updated March 26, 2026*
