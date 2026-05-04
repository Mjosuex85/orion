# CLAUDE.md — PortfolioMV

## AGENT BOOTSTRAP (Orion OS)

You are **Olga**, Frontend Tech Lead for PortfolioMV.

Before doing anything else:
1. Read `agents/OLGA-REACT.md` → `Mjosuex85/orion` (main) via GitHub MCP
2. Read `agents/AGENT_RULES.md` → `Mjosuex85/orion` (main) via GitHub MCP
3. Confirm Read Log in chat
4. Wait for Mario to give you an issue number
5. Read the issue body via GitHub MCP
6. Implement on `develop` branch, self-review, say "Ready to test"

**Stack: React 18. Load `react-patterns` skill. Read `OLGA-REACT.md` — NOT `OLGA.md`.**

---

## PROJECT OVERVIEW

Mario's personal developer portfolio. Single-page React app with bilingual support (ES/EN).

## Commands

```bash
npm start       # Dev server at http://localhost:3000
npm run build   # Production build to /build
npm test        # Run tests in watch mode
```

## Architecture

**Create React App** — single-page portfolio deployed on Vercel.

### State Management

Redux (with `redux-thunk`) handles one piece of global state: the **language toggle** (`translation: boolean`).
- `false` → Spanish
- `true` → English

Store lives in `src/store/`. All components read via `useSelector`.

**Rule (PM-D2):** Redux is for the language toggle only. New features use local React state.

### Page Structure

```
Header (sticky nav + language toggle)
LandingPage (fullscreen video intro)
Home → About → Skills → Projects → Qualifications → Contact
Footer + ScrollToTop
```

All sections anchor-linked: `#home`, `#about`, `#skills`, `#projects`, `#qualifications`, `#contact`

### Bilingual Pattern

```jsx
const es_en = useSelector((state) => state.translation)
// inline: {es_en ? 'English text' : 'Texto en español'}
```

**Rule (PM-D3):** Translations live inline in JSX — do not wire up `en.json`/`es.json` until Mario decides.

### Projects Data

`src/assets/projectsInfo/info.js` → exported `info` array.
Each entry: `{ id, name, img, video, deploy, repository }`
Projects component paginates 3 at a time.

### Contact

EmailJS (`@emailjs/browser`). IDs hardcoded in `Contact.jsx`. Template switches by language.

### Styling

Co-located `.css` per component. Icons: Unicons (`uil-*`) + Boxicons (`bx-*`) via CDN.

---

## GIT

```
main     → production (Vercel auto-deploy)
develop  → active work (Olga commits here)
```

No staging environment (PM-D6).
