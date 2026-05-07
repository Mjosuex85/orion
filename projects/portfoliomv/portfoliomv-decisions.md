# PortfolioMV — Project Decisions

> Project-specific decisions for portfolioMV.
> Universal decisions live in `DECISIONS.md`.

---

## STACK

**PM-D1. Stack is React 18 + CRA. No migration to Vite in current phase.**
Revisit when CRA causes real friction.

**PM-D2. Redux is used only for language toggle. No new global state via Redux.**
New features use local React state (useState).

**PM-D3. Translations live in JSX inline, not in JSON files.**
Until volume justifies a migration, keep translations inline.

---

## AGENT

**PM-D4. Olga is the assigned agent for portfolioMV.**
Olga operates with react-patterns skill (not angular-patterns).

**PM-D5. Issues for portfolioMV live in `Mjosuex85/portfolioMV` repo.**

---

## GIT

**PM-D6. GitFlow: develop → main. No staging environment in current phase.**
When Supabase integration grows, revisit.

---

*PortfolioMV Decisions v1.0.0 — updated Session 36 (modular folder structure)*
