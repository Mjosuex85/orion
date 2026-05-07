# GameOn — Ideas & Product Memory

> This file is for Orion and Mario. Not for Nestor or Olga.
> It captures product vision, pending ideas, backlog concepts and business decisions.
> Updated every session.

---

## PRODUCT VISION

GameOn is not just a match organizer — it's a player identity platform for amateur sports.
The long-term bet: when someone touches your spot on the field, your FIFA card appears.
The short-term hook: replace WhatsApp and paper for organizing matches.

**Two future directions:**
1. **Social network** — players make matches public, anyone can discover and join without a link
2. **Player identity** — stats, history, FIFA card unlocked through org tournaments/leagues

---

## MATCH CALENDAR VIEW (April 8, 2026)

> Captured during Session 20. Do not build until post-demo.

Three view modes: DIARIO (default) / SEMANAL / MENSUAL
Backend: `GET /organizations/:id/matches` already supports `dateFrom` + `dateTo` — no changes needed.
Trigger: when Jose has enough matches that the daily view is insufficient.
Estimation: M frontend + XS backend.

---

## THE IMMERSIVE FIELD — La joya de la corona (April 1, 2026)

> Mario's vision. The single most differentiating feature of GameOn.

Evolution layers:
- Layer 1: Visual polish (post-demo) — Olga, no backend needed
- Layer 2: Organizer control — drag & drop, attendance, substitutions
- Layer 3: Live match mode — QR check-in, WebSockets, referee role
- Layer 4: Stats engine — requires tournament data

Trigger Layer 1: right after demo with Jose.

---

## TERRITORIAL EXPANSION MODEL (April 1, 2026)

> Do not build until the second territory is real.

SUPERADMIN → REGIONAL_ADMIN → ORGANIZER → PLAYER
New entity: `Region`. New role: `REGIONAL_ADMIN`.
Trigger: first real regional operator in a different city.

---

## USER TIERS (March 31, 2026)

```
FREE USER    → individual player
COMMUNITY   → informal group, free or low-cost
BUSINESS    → sport as a service, pays for platform
```

Funnel: FREE USER → COMMUNITY → BUSINESS (grows inside GameOn, upgrade is natural).
Post-demo: payment (Stripe) triggers ORGANIZER role automatically.

---

## BACKLOG IDEAS — READY TO IMPLEMENT

| Idea | Who |
|------|-----|
| Click on player modal → go to profile | Olga |
| Show available spots / hide when already joined | Olga |
| Reset filters button | Olga |
| Match duration field | Nestor + Olga |
| Location reference field | Nestor + Olga |
| Show price × players = total | Olga |
| Generic avatar (replace DiceBear) | Olga |
| GameOn brand — prevent browser translation | Olga |

---

## TECHNICAL DEBT

- `admin.component` built without architecture (Session 9) — HTML 1040 lines, SCSS 26kB.
  Lesson: define component architecture before building admin panels. Flash for mechanical tasks only.

---

*Part of Orion OS — updated Session 36 (modular folder structure)*
*Technical context → `projects/gameon/gameon.md`*
