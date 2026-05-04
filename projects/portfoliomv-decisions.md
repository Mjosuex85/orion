# PortfolioMV — Project Decisions

> Project-specific decisions for portfolioMV.
> Universal decisions live in `DECISIONS.md`.

---

## STACK

**PM-D1. Stack is React 18 + CRA. No migration to Vite in current phase.**
Reason: the objective is adding features, not infrastructure work. Revisit when CRA causes real friction.

**PM-D2. Redux is used only for language toggle. No new global state via Redux.**
New features use local React state (useState) unless there is a clear cross-component need.

**PM-D3. Translations live in JSX inline, not in JSON files.**
The `en.json` / `es.json` files exist but are not wired. Until volume justifies a migration, keep translations inline.

---

## AGENT

**PM-D4. Olga is the assigned agent for portfolioMV.**
Olga operates with react-patterns skill (not angular-patterns — different project, different stack).

**PM-D5. Issues for portfolioMV live in `Mjosuex85/portfolioMV` repo.**
Follows D5 (universal): issues in the project repo, not in orion.

---

## GIT

**PM-D6. GitFlow: develop → main. No staging environment in current phase.**
Reason: portfolio has no backend to migrate. Staging is unnecessary overhead.
When Supabase integration grows, revisit.

---

*PortfolioMV Decisions v1.0.0 — created May 4, 2026 (Session 34)*
