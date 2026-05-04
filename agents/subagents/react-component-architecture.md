# Subagent: React Component Architecture

> Invoked by Olga-React when a component exceeds size limits or a new feature has multiple sections.
> Read this before implementing. Apply the checklist. Fix issues before continuing.

---

## WHEN TO INVOKE

- Component JSX > 150 lines
- Component JS/logic > 200 lines
- New feature with 2+ distinct visual sections
- Uncertain how to split a component

---

## CORE PRINCIPLE

One component = one responsibility. If you need to scroll to understand a component, it needs to be split.

---

## SPLIT DECISION TREE

```
Is this section independently reusable across the app?
  YES → Extract as shared component in src/shared/ or src/components/
  NO  → Is it a distinct visual block within this page?
          YES → Extract as local sub-component in the same folder
          NO  → Keep it in the current component
```

---

## FILE ORGANIZATION PATTERN

```
src/
  components/          → shared across multiple pages
    Button/
      Button.jsx
      Button.css
    Card/
      Card.jsx
      Card.css

  pages/ (or sections/) → page-level components
    Contact/
      Contact.jsx
      Contact.css
      ContactForm.jsx    ← local sub-component if form is complex

  assets/
    projectsInfo/info.js
```

---

## PROPS DESIGN CHECKLIST

Before finalizing a component's interface:

```
□ Props are the minimum necessary — no over-passing
□ Callbacks are named onX (onClick, onSubmit, onSelect)
□ Boolean props default to false
□ No prop drilling more than 2 levels deep — lift state or use context
□ Arrays and objects have sensible defaults ([] or null)
```

---

## STATE PLACEMENT RULE

```
State used only in one component  → useState inside that component
State shared between siblings     → lift to closest common parent
State shared across many pages    → context (or Redux if already used)
Language toggle                   → already in Redux, use useSelector
```

---

## NAMING CONVENTIONS

```
Component files:   PascalCase    → ContactForm.jsx, ProjectCard.jsx
CSS files:         same name     → ContactForm.css
Component names:   PascalCase    → const ContactForm = () => {}
Props:             camelCase     → onSubmit, isLoading, projectList
Event handlers:    handle prefix → handleSubmit, handleClick
```

---

## ANTI-PATTERNS TO AVOID

```
❌ One massive component with 300+ lines doing everything
❌ Deeply nested JSX (more than 4 levels without extraction)
❌ Duplicate JSX blocks — extract to a component instead
❌ Logic mixed with rendering in a way that's hard to read
❌ Passing setState down as a prop (expose a callback instead)
```

---

## CHECKLIST BEFORE CONTINUING

```
□ No component exceeds 150 lines JSX / 200 lines JS
□ Each component has a single clear responsibility
□ Shared components live in src/components/
□ Local sub-components live in the same folder as their parent
□ Props interface is minimal and well-named
□ State lives at the correct level
□ No prop drilling deeper than 2 levels
```

---

*React subagent — part of Orion OS. Created Session 34.*
