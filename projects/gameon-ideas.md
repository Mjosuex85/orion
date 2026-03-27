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

## DECISIONS MADE THIS SESSION (March 27, 2026)

- **Free user price** — can set a price on their match. It's an organizational tool (Bizum), not monetization. No plan required. Revisit when Pro Free plan is defined. (D72)
- **PRIVATE visibility** — means "not in listings", not "only creator can view". Anyone with the link can see and join. Google Doc model. (D71)
- **PLAN_LIMITS as constants** — not in DB for now. Will migrate to DB when first paying org exists and limits need to vary per client.
- **Organizations** — separate entity with member roles (OWNER/MANAGER/STAFF). Not just a user role.
- **Tournaments** — teams register complete (name + captain). Players added later. Bracket logic deferred.

---

## BACKLOG IDEAS — ANALYZED

### Ready to implement (small, no blocking dependency)

| Idea | Type | Who |
|------|------|-----|
| Click on player modal in field → go to profile | Frontend | Olga |
| Show "Available to register" / hide when already joined | Frontend | Olga |
| Reset filters button | Frontend | Olga |
| Match duration field | Backend + Frontend | Nestor + Olga |
| "Location reference" field in create match | Backend + Frontend | Nestor + Olga |
| Show price × players = total when creating match | Frontend | Olga |
| Generic avatar (replace DiceBear figurines) | Frontend | Olga |
| GameOn brand — prevent browser translation of the name | Frontend | Olga |
| `translate="no"` on brand elements | Frontend | Olga |

### Medium — requires planning

| Idea | Notes |
|------|-------|
| Email to user when they join a match (with match info + rules) | Resend already integrated. Nestor adds trigger in joinMatch() |
| Modal when free user hits 1 match/day limit — offer organizer plan | Olga UI + copy |
| Vercel pre-production environment (develop → staging) | Vercel config only, no code change |
| Two match creation flows: free (simple) vs organizer (full) | Issue #79 created. Blocked on GET /organizations/my |
| Football positions by game mode (goalkeeper, defender, forward) | Requires design session before implementing |
| Teams in match: team 1 vs team 2 | Add `team: 1 | 2` to MatchParticipant. Needs UX definition |
| Professional i18n system (ngx-translate or Angular i18n) | Large Olga task. Post-demo |

### Large — strategic, post-demo

| Idea | Notes |
|------|-------|
| WhatsApp confirmation 24h and 6h before match | WhatsApp Business API + Bull queues. New infrastructure. |
| Admin saves field/match templates and reschedules | Org "templates" module in backend |
| Tactical formations with drag & drop (PREMIUM) | Post-launch premium feature |
| GameOn Academy — youth academies, scouts, revenue sharing | New business model. Define before touching code. |
| Bizum integration — centralized payment flow | Show organizer's Bizum number. No payment gateway needed. Simple. |
| Anti-DDoS / server protection | Cloudflare + advanced rate limiting. Investment when there's traction. |
| Profitability calculator (cost − revenue) | Requires real payment integration first. |
| First org partners get competitive advantage in app | Business/pricing decision. Define before implementing. |

---

## UPCOMING ISSUES TO CREATE

When Mario returns from vacation, create these in order:

1. **Backend** — `GET /organizations/my` (Nestor) — unblocks #79
2. **Frontend** — #79 two match creation flows (Olga) — needs #1 first
3. **Backend** — Leagues module (Nestor) — same model as Tournaments
4. **Frontend** — Organizations page (Olga) — public listing + detail
5. **Frontend** — Tournaments page (Olga) — public view
6. **Deploy** — migrate develop → main, production deploy

---

## PRODUCT IDEAS — NOT YET ANALYZED

Raw ideas captured, not yet discussed with Orion:

- Strategies and position changes in 7vs7 and 11vs11 (button for substitutions)
- Football position standards by game mode (research best practices for 5v5, 7v7, 11v11)
- First organizations get visibility advantages in the app — define fair competition model
- Pro Free plan — what does it unlock? Price on matches? Multiple matches/day? Define

---

## COMPETITIVE POSITIONING

GameOn vs WhatsApp groups:
- Identity: players have a profile, stats, history
- Organization: structured match creation, not chaotic group chats
- Discovery: find organizations and matches in your area

GameOn vs existing platforms:
- Mobile-first, simple UX
- Free entry point with real value
- FIFA card differentiator — nobody else does player identity at amateur level

---

*Part of Orion OS — created March 27, 2026*
*Technical context → `projects/gameon.md`*
