# PortfolioMV — Project Context

> Technical context for Orion and Olga.
> Project-specific decisions live in `projects/portfoliomv-decisions.md`.

---

## WHAT IS PORTFOLIOMV

Mario's personal developer portfolio. Single-page React app with bilingual support (ES/EN).
The differentiator is that it reflects Mario's real career — not a template. It shows who he is as a technical founder.

**Current state:** Functional but needs content + features updated to reflect the current stage of Mario's career (GameOn, NutriApp, Orion OS).

---

## ISSUES REPO: Mjosuex85/portfolioMV
## KANBAN:      GitHub Projects > PortfolioMV (account level)

Columns: Backlog → Ready → In progress → In review → Done

---

## STACK

```
Framework:    React 18 (Create React App)
State:        Redux + redux-thunk (language toggle only)
Router:       React Router v6
Styling:      CSS per component + Unicons + Boxicons (CDN)
Contact:      EmailJS
DB:           Supabase (SDK installed, supabase/ dir exists)
Deploy:       Vercel
Analytics:    @vercel/analytics + @vercel/speed-insights
Node:         22.x
```

⚠️ CRA is deprecated. Migration to Vite is a future consideration — not current priority.

---

## REPO

```
Mjosuex85/portfolioMV  → private, single repo
```

Branch structure:
```
main     → production (Vercel)
develop  → active work (Olga commits here)
```

---

## STRUCTURE

```
src/
  assets/
    projectsInfo/info.js   → project entries array (id, name, img, video, deploy, repository)
  store/                   → Redux (translation boolean: false=ES, true=EN)
api/                       → Vercel serverless functions
public/                    → static assets
supabase/                  → Supabase config/migrations
```

### Bilingual pattern
Every component that renders text:
```jsx
const es_en = useSelector((state) => state.translation)
// inline: {es_en ? "English" : "Español"}
```
`src/static/en.json` and `es.json` exist but NOT wired — translations live in JSX.

### Projects data
`src/assets/projectsInfo/info.js` → exported `info` array.
Each entry: `{ id, name, img, video, deploy, repository }`
Projects component paginates 3 at a time with prev/next.

### Sections (top to bottom)
```
Header (sticky nav + language toggle)
LandingPage (fullscreen video intro)
Home → About → Skills → Projects → Qualifications → Contact
Footer + ScrollToTop
```
All sections are anchor-linked: `#home`, `#about`, `#skills`, `#projects`, `#qualifications`, `#contact`

---

## AGENT

**Olga-React** handles this project (load `OLGA-REACT.md`, NOT `OLGA.md`).

---

## KNOWN INCIDENTS — Session 34

### ⚠️ INC-01: GitHub Project token not configured for portfolioMV
**What happened:** Issue #18 was created as a normal GitHub issue — it did NOT appear in the portfolioMV Kanban board automatically.
**Root cause:** The GitHub Project (Kanban) for portfolioMV is not linked to the repo. GitHub Projects v2 requires either a REST token with `project` scope or manual repo linking from the UI.
**Impact:** Issues created via GitHub MCP go to the repo issue tracker but not to the board.
**Fix:** Mario must manually link `portfolioMV` repo to the GitHub Project board from the UI:
  GitHub → Projects → PortfolioMV → Settings → Linked repositories → add portfolioMV
**Status:** ⬜ Pending — Mario action required.

### ⚠️ INC-02: Olga could not read issue #18 correctly
**What happened:** Olga failed to read the issue body via GitHub MCP during the first attempt with portfolioMV.
**Root cause:** Suspected — same token scope issue as INC-01, or Antigravity session context. Not confirmed.
**Impact:** First Olga session with portfolioMV was blocked before implementation started.
**Fix:** Resolve INC-01 first. Then retry with a fresh Antigravity session, clean bootstrap.
**Status:** ⬜ Pending — blocked by INC-01.

---

## STATUS — May 4, 2026 (Session 34 close)

**Production:** Functional — last push April 22, 2026

**Registered in Orion OS:** Session 34.

**Open / Pending:**
- 🔴 Fix: Link portfolioMV repo to GitHub Project board (INC-01) — Mario
- 🔴 Retry: Olga session after INC-01 is resolved (INC-02)
- 📋 Issue #18 open: replace StockWise → Orion OS card + OrionOS page
- 📋 Update project entries: GameOn, NutriApp, Orion OS
- 📋 Evaluate: Supabase integration (contact form? visitor tracking?)
- 📋 Evaluate: CRA → Vite migration (future, not blocking)

**Next session priority:**
1. Mario links portfolioMV to GitHub Project board
2. Fresh Olga-React session → issue #18

---

*Part of Orion OS v2.0.0 — updated May 4, 2026 (Session 34 close)*
