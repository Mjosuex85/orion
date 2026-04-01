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

## TERRITORIAL EXPANSION MODEL — "GameOn Madrid" (April 1, 2026)

> Captured before implementation. Do not build until the second territory is real.

### The idea

Mario operates GameOn in Madrid. He controls all organizations (Jose/SoccerMix, etc.) from his admin panel. If someone else wants to operate GameOn in another city or country — say, GameOn Buenos Aires — they would need the same tools Mario has, but scoped to their territory only. They cannot see or touch Madrid's organizations.

This creates a two-level admin hierarchy:

```
SUPERADMIN (Mario global)
  └── REGIONAL_ADMIN (Mario = Madrid, someone else = Buenos Aires, etc.)
        └── ORGANIZER (Jose, SoccerMix, etc.)
              └── PLAYER
```

### What this implies architecturally

**New entity needed: `Region` (or `Instance`)**
```
Region
  id
  name          → "GameOn Madrid"
  countryCode   → "ES"
  city          → "Madrid"
  adminUserId   → FK to the regional admin user
  createdAt
```

**Changes to existing entities:**
- `Organization` gets a `regionId` FK — belongs to a territory
- `User` with `REGIONAL_ADMIN` role has scope limited to their region
- `SUPERADMIN` sees everything across all regions

**New role needed:**
```
Current:   ADMIN | ORGANIZER | PLAYER
Future:    SUPERADMIN | REGIONAL_ADMIN | ORGANIZER | PLAYER
```

The current `ADMIN` role becomes `SUPERADMIN`. `REGIONAL_ADMIN` is a new role with the same tools but scoped to a region.

**Panel implications:**
- `/admin` → today it's Mario only (SUPERADMIN). In the future it shows cross-region data.
- `/region-admin` → new panel, same tools as `/admin` but filtered to own region only.
- `/organizer` → unchanged. Jose doesn't know or care about regions.

### Business model connection

This is how GameOn scales without Mario doing everything:
- Mario sells a "GameOn franchise" to a regional operator
- That operator pays a fee (or revenue share) to Mario/GameOn
- They get a `REGIONAL_ADMIN` account + their own branded territory
- They onboard their own organizers, manage their own orgs, see their own stats
- Mario keeps oversight from the SuperAdmin panel

This is the same model Glovo/Uber uses for city expansion — central product, local operators.

### Why we are NOT building this now

Applying D78:
- **Scalability verdict:** The model is correct. It will be needed.
- **Demo constraint:** Zero second territories exist today. Building this now would be architecture for a problem we don't have.
- **Trigger to build:** When the first real regional operator appears — someone willing to pay and operate in a different city. That conversation defines the real requirements.
- **Debt created by waiting:** The current `ADMIN` role will need to be renamed to `SUPERADMIN` and a migration run. That is a small, clean migration when the time comes.

### What to do when the trigger fires

1. Design session with Mario — define exact scope of regional admin vs superadmin
2. Create `Region` entity + migration
3. Add `regionId` to `Organization` + migration
4. Add `REGIONAL_ADMIN` role + guard
5. Build `/region-admin` panel (fork of `/organizer` panel at a higher level)
6. Update `/admin` to show cross-region view

**Estimated size:** L backend + L frontend. Two sprints minimum.

---

## USER TIERS — DEFINITION (March 31, 2026)

This is a foundational product decision. Three distinct user types with different needs, different value, and different monetization paths.

### The three tiers

```
FREE USER       Individual player. Creates matches, has a profile, socializes.
                No payment, no organization. The base of the funnel.

COMMUNITY       Organized group of people around a sport. Informal — could be
                a group of friends, a neighborhood league, a recurring match crew.
                Has a public page, members, history. Free or low-cost.
                The growth layer — communities that scale become businesses.

BUSINESS        Entity that offers sport as a service. Manages fields, charges
                inscription fees, runs tournaments, needs statistics and tools.
                Pays for the platform. The monetization layer.
```

### Why this distinction matters

"Organization" in the code is the technical container — it holds both communities and businesses. But the **product experience and pricing should be differentiated**:

- A community does not identify with the word "empresa" — they are a group of friends or neighbors
- A business needs invoicing, advanced stats, professional tools — things a community does not need
- Mixing them creates a product that serves neither well

### The business model — Community → Business funnel

```
FREE USER
  → discovers GameOn through a match link or social
  → creates their profile, starts playing

COMMUNITY (free or freemium)
  → group organizes regularly, creates a community page
  → players find them, join matches, build history together
  → community grows → needs more tools → natural upgrade moment

BUSINESS (paid)
  → pays for advanced management tools
  → tournaments, leagues, player stats, field templates
  → can appear highlighted in GameOn discovery
  → early business partners get competitive advantage in the app
```

**The insight:** GameOn does not sell to businesses cold. Businesses grow inside GameOn from communities. By the time they upgrade, they are already dependent on the platform. The upgrade is not a sales pitch — it is a natural next step.

### How someone becomes an organizer (DECIDED March 31, 2026)

**For the demo (current):**
Mario assigns `role = ORGANIZER` manually in Neon + creates the organization via Postman.
This is acceptable while we have zero paying customers.

**Post-demo model:**
```
COMMUNITY path  → self-service
                  User creates a community page from the app (free)
                  No approval needed — like creating a WhatsApp group

BUSINESS path   → human contact or direct payment
                  Option A: contact form → Mario calls → onboards manually
                  Option B: pay the PRO plan → system auto-assigns ORGANIZER role
```

**Decision: Option B is the target.** Payment triggers role assignment automatically.
Option A (phone call) is for early enterprise clients who need custom onboarding.
Both can coexist — they are not mutually exclusive.

**What this requires technically (post-demo):**
- Payment integration (Stripe) → on successful payment → assign ORGANIZER role → create organization
- Self-service community creation — no payment, no admin needed
- These are separate issues to be created when we start the payments sprint

### What this changes in the product (pending design)

- **Onboarding:** Ask "are you a player, organizing a community, or running a sport business?" — route accordingly
- **Organization creation flow:** Two paths — "Create community" (casual, free) vs "Set up business" (professional, paid)
- **Public page:** Community page feels social. Business page feels professional — logo, services, booking CTA
- **Naming in the UI:** Use "comunidad" and "empresa" in Spanish UI — not "organización" which is too generic
- **Plan limits:** Communities get generous free limits. Businesses get pro tools on payment.

### Technical implication

The `Organization` entity already supports this — `plan: FREE | PRO` is the toggle. What we need:
- A `type` field: `COMMUNITY | BUSINESS` (or handled via plan)
- Differentiated UI per type (Olga)
- Differentiated onboarding flow (post-demo)

**Decision: do not add `type` field yet.** Design the UX first, then decide if it's a DB field or purely a plan flag. Create issue when post-demo design session happens.

---

## SPRINT 1 — DEMO WITH JOSE (SoccerMix)

**Goal:** Jose enters GameOn as an organizer and can manage his organization independently.

**Mario prepares manually:**
```sql
UPDATE users SET role = 'ORGANIZER' WHERE email = 'jose@email.com';
```
```
POST /organizations  { name: "SoccerMix", slug: "soccermix", ... }
POST /organizations/:id/members  { userId: jose_uuid, role: "OWNER" }
```

**What Jose needs in the frontend:**
- #96 — Organizer panel: `/organizer` dashboard + `/organizer/matches` + create match with org pre-filled

**Sprint 1 issues:**
| # | What | Who | Priority |
|---|------|-----|----------|
| #96 | Organizer panel (dashboard + matches + create) | Orion | ✅ Done |
| #95 | CHANGELOG.md | Nestor | 🟡 Pending |
| #91 | CI backend (build + lint) | Nestor | 🟢 Post-demo |
| #92 | CI frontend (build:prod + lint) | Olga | 🟢 Post-demo |
| #93 | SonarCloud | Mario + agents | 🟢 Post-demo |
| #94 | Staging + sprint system | Orion + Mario | 🟢 Post-demo |
| #90 | Automated migrations via GH Actions | Nestor | 🟢 Post-demo |

---

## DECISIONS MADE (March 27-28, 2026)

- **Free user price** — can set a price on their match. It's an organizational tool (Bizum), not monetization. No plan required. Revisit when Pro Free plan is defined. (D72)
- **PRIVATE visibility** — means "not in listings", not "only creator can view". Anyone with the link can see and join. Google Doc model. (D71)
- **PLAN_LIMITS as constants** — not in DB for now. Will migrate to DB when first paying org exists.
- **Organizations** — separate entity with member roles (OWNER/MANAGER/STAFF). Not just a user role.
- **Tournaments** — teams register complete (name + captain). Players added later. Bracket logic deferred.
- **npm ignore-scripts** — `.npmrc` added to both repos (D73). Security standard for all Orion OS projects.
- **Orion direct GitHub changes** — Orion can push config/docs/security directly. Mario + agents do `git pull` before working. (D14 redefined)

---

## TECHNICAL DEBT — LEARNED LESSONS

### admin.component — created with Gemini Flash without architecture (Session 9)

The admin component was built incrementally with Gemini Flash without stopping to define proper architecture. Result:
- HTML grew to 1040 lines — single component doing everything
- SCSS grew to ~26kB across 9 partial files — exceeded Angular budget
- Multiple sessions spent fixing what should have been right from the start

**Root cause:** Gemini Flash was used for a task that required architecture decisions upfront. Flash split the SCSS but didn't reduce the actual content — it just moved the problem around.

**Lesson for Orion OS:**
- Before building any admin panel, dashboard, or complex UI → define component architecture first
- Gemini Flash for mechanical tasks only — never for tasks that require architectural decisions
- Components with multiple sections (tabs, views) → must be componentized from day 1, not after the fact
- The cost of fixing architectural debt is 3-5x the cost of doing it right initially

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

### Medium — requires planning

| Idea | Notes |
|------|-------|
| Email to user when they join a match | Resend already integrated. Nestor adds trigger in joinMatch() |
| Modal when free user hits 1 match/day limit — offer organizer plan | Olga UI + copy |
| Staging environment (develop → staging → main) | Issue #94. Vercel config. |
| Football positions by game mode | Requires design session before implementing |
| Teams in match: team 1 vs team 2 | Add `team: 1 \| 2` to MatchParticipant. Needs UX definition |
| Professional i18n system (ngx-translate or Angular i18n) | Large Olga task. Post-demo |
| Community vs Business onboarding flow | Design session needed first. See User Tiers section above. |
| Stripe payment → auto ORGANIZER role assignment | Requires payments sprint. See "How someone becomes an organizer". |
| Filters on organization public page | #102. Needs backend extension first (Nestor Phase 1), then frontend (Olga Phase 2). |

### Large — strategic, post-demo

| Idea | Notes |
|------|-------|
| **Territorial expansion model** | See full analysis above. Trigger: first real regional operator. |
| WhatsApp confirmation 24h and 6h before match | WhatsApp Business API + Bull queues. New infrastructure. |
| Admin saves field/match templates and reschedules | Org "templates" module in backend |
| Tactical formations with drag & drop (PREMIUM) | Post-launch premium feature |
| GameOn Academy — youth academies, scouts, revenue sharing | New business model. Define before touching code. |
| Anti-DDoS / server protection | Cloudflare + advanced rate limiting. Investment when there's traction. |
| Profitability calculator (cost − revenue) | Requires real payment integration first. |
| First org partners get competitive advantage in app | Business/pricing decision. See User Tiers funnel above. |

---

## PRODUCT IDEAS — NOT YET ANALYZED

- Strategies and position changes in 7vs7 and 11vs11
- Football position standards by game mode (5v5, 7v7, 11v11)
- Pro Free plan — what does it unlock?

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
- Community → Business funnel: no cold sales, growth happens inside the platform
- **Territorial franchise model** — scales without Mario doing everything, local operators with central oversight

---

*Part of Orion OS — updated April 1, 2026 (Session 12)*
*Technical context → `projects/gameon.md`*
