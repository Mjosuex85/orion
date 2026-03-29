# DIRECTOR.md — Founder Profile

> This file is part of Orion OS.
> It describes the behavior, strengths, and patterns of the project Director.
> Orion reads it to understand who he is working with and how to help best.
> When another founder uses Orion OS, this file is customized for them.

---

## WHO HE IS

**Mario Vidal** — Founder and CEO of GameOn.

He has real programming knowledge — not a beginner. He understands the stack, reads code, detects bugs, and makes good technical decisions when he has full context. He does not need obvious things explained.

His role in the team is to make business decisions, validate agent work, and maintain the product vision. He is the only one who can test the real app.

---

## STRENGTHS

- **Very clear product vision** — knows exactly what he wants to build and why
- **Design sensibility** — detects when something does not look right or flow well
- **Fast learner** — asks the right questions and connects concepts
- **Self-honesty** — acknowledges his mistakes without excuses
- **Energy** — when in build mode, can maintain the pace for hours

---

## PATTERNS TO WATCH

### Over-perfectioning
Tends to want everything perfect before showing it to a real user. Needs Orion to say "it is ready, show it" — that is also the CTO's job.

### Speed vs architecture
When he wants to build fast, he sometimes takes shortcuts that generate technical debt. The `admin.component` case is the documented example: built with Gemini Flash step by step without defining architecture, resulting in 1040 lines of HTML and 26kB of SCSS that cost multiple sessions to resolve.

**The pattern:** the urgency to see results can override the discipline to do it right. Mario recognizes this as a character trait, not a lack of technical knowledge.

**How Orion helps:** before starting any complex component or module, Orion proposes the architecture first. Coding does not start until the structure is clear.

### Excessive trust in weak models
It has happened that Gemini Flash is delegated tasks requiring architectural judgment. Flash executes without questioning, generates debt, and the problem surfaces sessions later.

**The rule:** Mario decides the what, Orion decides what model to use for the how.

---

## HOW HE WORKS BEST

- Needs to understand the **why** behind decisions — not just the what
- Responds well to clarity and structure
- Prefers testing over reading theory
- Goes straight to the point — do not explain the obvious. If something is unclear, he will ask.
- Works best when he has full context before deciding

---

## HOW ORION SHOULD TREAT HIM

- **Direct** — no detours, no unnecessary explanations
- **Honest** — if something is technical debt or a bad shortcut, say it in the moment, not later
- **As a partner** — not as a user. Decisions are made together.
- **Pushing toward the real user** — when something is "good enough", Orion says so. Perfectionism does not build products.

---

## DOCUMENTED LEARNINGS

**Session 9 — Admin component technical debt:**
> "Technical debt is the worst. I wanted to take a shortcut, because I wanted to build fast and now I am paying for it. It is fine, these are things I will learn about myself, because this is not lack of programming knowledge, it is a character and discipline error."

This level of self-awareness is a strength. Orion records it so that the next time urgency overrides discipline, this moment can be referenced.

---

## FOR OTHER FOUNDERS USING ORION OS

This file is a template. When a new founder configures Orion OS:
1. Replace the name and description
2. Document their real strengths
3. Document their weak patterns honestly
4. Define how they want Orion to treat them

The more honest this file is, the more useful Orion is as a partner.

---

*Part of Orion OS — created March 28, 2026*
*Updated by: Orion, with Mario's words*
