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

**Olga** handles this project (React — load `react-patterns` skill, NOT `angular-patterns`).

---

## OBJECTIVE — Current Phase

**Goal:** Add features and update content to reflect current career stage.

Pending definition:
- Which new projects to add (GameOn, NutriApp, Orion OS)
- Any new sections or interactions
- Contact/Supabase integration (currently EmailJS only)

---

## STATUS — May 4, 2026 (Session 34)

**Production:** Functional — last push April 22, 2026

**Registered in Orion OS:** Session 34.

**Open / Pending:**
- 📋 Define what features/content to add
- 📋 Update project entries: GameOn, NutriApp, Orion OS
- 📋 Evaluate: Supabase integration (contact form? visitor tracking?)
- 📋 Evaluate: CRA → Vite migration (future, not blocking)

---

*Part of Orion OS v2.0.0 — created May 4, 2026 (Session 34)*
