# Orion OS — Roadmap & Vision

> This file documents where Orion OS is, where it's going, and the principles
> that must guide every decision made to evolve it.
> It is the context for sessions focused on growing Orion OS as a product.

---

## WHAT IS ORION OS

Orion OS is a framework for orchestrating AI agent teams that build real software.
It is not a chatbot. It is a working methodology: roles, rules, memory, decisions, workflows.

Today it runs on top of Claude + GitHub MCP + markdown files.
Tomorrow it will be a product anyone can use without technical knowledge.

**The two layers:**
```
System layer   → workflows, skills, templates, decisions  → Mjosuex85/orion-os (public, MIT)
Instance layer → projects/, logs/, personal decisions    → Mjosuex85/orion (private)
```

---

## CURRENT STATE — v2.0.0 (May 1, 2026)

### What works
- Smart session init: Orion loads the right context + skills per project
- Session commands: "iniciar proyecto", "vamos con X", "cierra sesión"
- Multi-project: GameOn (Angular/NestJS) + NutriApp (React/Supabase) isolated
- Multi-framework skills: angular-patterns + react-patterns
- Monorepo + polyrepo support in project-init
- Scaffold via GitHub MCP (D90) — reversible toward web UI
- BOOT.md — provisional one-line session start for Claude Desktop
- **Public system layer extracted to `Mjosuex85/orion-os` (D94, D95)**

### What doesn't work yet
- No automatic session start — requires manual BOOT.md trigger
- No persistent memory between sessions — bootstrap cost every time
- No web interface — Mario is the only user, requires technical knowledge
- DECISIONS.md in GameOn repo is separate from universal DECISIONS.md — needs sync review
- project-init wizard exists in docs but not tested end-to-end as a command
- Public repo `orion-os` is a skeleton — actual content (agents, skills, workflows,
  templates, examples) needs to be migrated incrementally during v2.x

---

## ROADMAP

### v2.0.0 — Public System Layer  ✅ DONE (May 1, 2026)
```
Clean fork of the system layer to Mjosuex85/orion-os (public, MIT).
No shared git history with this private instance. Hybrid genericization
(generic system code + /examples for real anonymized projects).
Sync mechanism deferred until usage shows the real pattern.
Shipped: README, LICENSE, ROADMAP, DECISIONS, MIGRATION + placeholder dirs.
```

### v2.x — System Layer Maturation  (active)
```
Goal: migrate, generalize, and stabilize the system layer in the public repo
What changes:
  - Agents (Nestor, Olga, Bruno, DIRECTOR, AGENT_RULES) → generic versions in public repo
  - Skills (angular-patterns, react-patterns) → public, framework-agnostic structure
  - Workflows (project-init, context-switch, security-incident, etc.) → public, instance-agnostic
  - Templates (issue, project-context, project-decisions, ci, bruno) → public, generic
  - /examples populated with example-polyrepo + example-monorepo (anonymized GameOn + NutriApp)
  - Sync mechanism between this private instance and the public repo defined and tested
  - Order: agents → templates → workflows → skills → examples (least to most opinionated)
Done criteria: a new technical user can clone orion-os, copy structure, write their
own ORION.md, and have a working instance in < 30 minutes.
```

### v3.0.0 — Orion OS web interface
```
Goal: anyone can use Orion without technical knowledge
Core flow:
  1. User goes to orion-os.com
  2. GitHub OAuth → connects their account
  3. Onboarding wizard:
     - Project name
     - Topology (monorepo/polyrepo)
     - Frontend framework
     - Backend
     - Database
     - Auth
     - Deploy
  4. Orion creates repo + files + issue #1 automatically
  5. User gets a chat with Orion — context loaded, no bootstrap needed
  6. "Setup" button → automated git clone + npm install script

Tech stack (proposed, not decided):
  - Frontend: React (NutriApp is the proof of concept)
  - Backend: NestJS or serverless
  - LLM: Anthropic API (Claude)
  - GitHub integration: GitHub OAuth + GitHub API (replaces MCP)
  - Session memory: DB-backed (replaces markdown bootstrap)
```

---

## CORE PRINCIPLES FOR EVOLVING ORION OS

### D89 — Every decision must be reversible
When two options exist, always choose the one that doesn't block the web product.
If a decision is irreversible, document it explicitly and justify it.

### D90 — Scaffold via GitHub API/MCP, never terminal
Orion pushes files to repos. Users clone and install.
This maps 1:1 to the future web UI "Setup" button.
If a workflow only works with terminal access, it is not a valid Orion OS workflow.

### D94 — System and instance live in separate repos
The public repo is a product. The private repo is a diary. They never mix.
Migration from private to public is manual, careful, and one file at a time —
until the data tells us what to automate.

### The lab principle
GameOn = primary lab for testing Orion as CTO.
NutriApp = proof of concept for multi-framework + monorepo support.
Every decision made in these projects either improves Orion OS or validates v3.0.0 assumptions.

---

## OPEN QUESTIONS (to explore in this project)

1. How does Orion OS handle persistent memory without markdown bootstrap?
   → DB-backed state? Vector memory? Hybrid?

2. What is the minimum viable v3.0.0?
   → Just the wizard + repo creation, or also the full chat interface?

3. How does orion-os (public) stay in sync with Mario's private instance?
   → Deferred. First 2-3 v2.x migrations are manual cherry-pick. Then we decide.

4. Can the BOOT.md be replaced by a Claude.ai Project with instructions?
   → Yes for claude.ai users. But what about Claude Desktop / CLI users?

5. What does "skill" look like in the web product?
   → Marketplace? User-defined? Auto-detected from stack?

6. (NEW) What is the migration order for v2.x?
   → Current proposal: agents → templates → workflows → skills → examples.
     Reason: agents and templates are most generic; skills carry the most
     framework-specific opinions; examples carry the most personal context.
     Migrating least-opinionated first lets us discover the genericization
     pattern before tackling the hardest cases.

---

## SESSION FOCUS FOR THIS PROJECT

This project exists to:
- Improve Orion OS as a system (not to build GameOn or NutriApp features)
- Make architectural decisions about v2.x and v3.0.0
- Test and refine the bootstrap, commands, and workflow files
- Identify gaps in the system and document them as issues or RFCs
- Drive the v2.x migration from private to public, file by file

When something breaks in GameOn or NutriApp sessions → bring it here to fix the system.
When a new workflow or skill is needed → design it here first, then push to the repo.

---

*Orion OS v2.0.0 — May 1, 2026*
*This file is the north star for sessions focused on growing Orion OS.*
