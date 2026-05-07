# DECISIONS.md — Orion OS Universal

> Decisions that apply to **every project** under Orion OS.
> Project-specific decisions live in `projects/<project>-decisions.md`.

---

## 1. TEAM AND ROLES

**D1. Mario is the sole business decision maker.**

**D2. Orion acts as technical architect and coordinator.**

**D3. Agents have specialized roles — they execute, not decide.**

**D4. Mario always tests before an issue is closed.**

---

## 2. WORKFLOW

**D5. All issues for a project live in a single repo (typically the backend repo).**

**D6. Each issue includes: context + agent prompt + test plan.**

**D7. Maximum 2 issues In Progress simultaneously per project.**

**D8. When Orion says "we'll do that later", an issue is created immediately.**

**D9. When Mario proposes an idea, Orion analyzes it technically before creating the issue.**

**D52. Commits use `ref #XX` — never `closes #XX` or `fixes #XX`.**

**D53. Orion closes issues — they are never closed automatically.**

**D55. Configuration or documentation changes — Mario receives them with `git pull`.**

**D56. Mario gives the green light before Orion uses any MCP on application repos.**

**D87. Session close protocol — Orion updates `projects/<project>.md` at the end of every session.**
Shows diff to Mario before push. Source of truth for project state.

---

## 3. GIT AND BRANCHES

**D10. `develop` is the active working branch. `main` is production.**

**D11. `main` is NOT touched — except via planned PR.**

**D12. All team members do `git pull origin develop` before starting work.**

**D14. Orion can push to `develop` for:** config, docs, urgent fixes decided with Mario, Orion OS files.

**D74. Branch protection on `main`.** No direct push. Mario sole merger.

---

## 4. PRODUCTION DEPLOYS

**D49. Production deploys via planned PR.**

**D51. Orion NEVER changes `main` outside of a deploy PR.**

---

## 5. SECURITY

**D35. Never commit credentials, tokens, or secrets.**

**D36. Never use `any` in TypeScript without justification.**

**D37. Never break DTO/API contracts the frontend consumes.**

**D73. `ignore-scripts=true` in `.npmrc` in all projects.**

---

## 6. TOOLS AND MCPS

**D38. GitHub MCP connected to Claude for Orion.**

**D68. Agents read issue body via MCP — corrections currently go in body, not comments.**

*Current state (May 2026):* GitHub MCP reads PR comments but does not read issue
comments reliably. Until that's fixed, Mario edits the issue body directly when
correcting an agent. The natural flow — body + comments, comments authoritative —
is the target once GitHub MCP supports it. This is a temporary tooling constraint,
not a permanent design choice.

**D82. GitHub MCP tool rules:**

| Tool | Use for |
|------|---------|
| `create_or_update_file` | Files in repo filesystem |
| `update_issue` | Issue body, title, state |
| `create_issue` | New issue |
| `push_files` | Multiple files, single commit |

---

## 7. AGENTS AND MODELS

**D54. Scale from Sonnet to Opus when:** codebase > 500k tokens or Sonnet fails twice.

**D63. Copilot/Gemini for S/M tasks, Claude CLI for L/XL.**

**D64. The value is in the context, not the model.**

**D66. Model by complexity:**

| Task | Model |
|------|-------|
| Mechanical refactor | Gemini Flash / Haiku |
| Bug analysis / features | Gemini Pro / Sonnet |
| Architecture | Gemini Pro / Sonnet |
| Global decisions | Claude Opus / Orion |

---

## 8. ORION OS STRUCTURE

**D60. ORION.md lives in `Mjosuex85/orion` — not in app repos.**

**D61. CLAUDE.md in each project repo = how to work (≤150 lines).**

**D65. Subagents live in `orion/agents/subagents/` — reusable across projects.**

**D81. Orion asks Mario before any direct code change to application repos.**

---

## 9. ENGINEERING PRINCIPLES

**D77.** ① Measure before optimizing. ② Never guess the bottleneck. ③ Avoid unnecessary complexity. ④ Simple fails less. ⑤ Data > algorithm.

---

## 10. DECISION FRAMEWORK

**D78.** Scalable by default. Pragmatic when there is a real deadline. Never silent about the tradeoff.

**D89. Every technical decision must be reversible or explicitly documented as irreversible.**
Reason: Orion OS is evolving toward a web product (v3.0.0). Decisions that block that evolution
must be flagged and justified. When two options exist, always prefer the one that doesn't
close doors for the future interface.

---

## 11. MULTI-PROJECT

**D88. Multi-project rules (v1.4.0):**

1. **Each project is fully isolated:** own project.md, own decisions file, own repos, own issues.
2. **D7 applies per project:** max 2 in-progress per project, not global.
3. **Context switch is explicit:** Orion saves state before switching (see `workflows/context-switch.md`).
4. **Decisions go to the right file:** universal → DECISIONS.md, project-specific → projects/<n>-decisions.md.
5. **Agents re-bootstrap on project switch:** they must re-read CLAUDE.md from the new repo.
6. **Cross-project decisions are rare:** if one is needed, document in DECISIONS.md and note in both project files.
7. **New projects use the init workflow:** `workflows/project-init.md` + templates. No ad-hoc setup.
8. **Primary project:** the project Orion loads by default at session start. Currently GameOn.

---

## 12. SCAFFOLD AND EXECUTION

**D90. Orion creates project scaffolds via GitHub MCP — never assumes local terminal access.**
Reason: the workflow must scale to the v3.0.0 web interface where no terminal exists.
Orion pushes all source files to the repo. Mario clones and runs `npm install` locally.
This is the only reversible flow — it maps 1:1 to a future "Setup" button in the web UI.

Claude Code can be used by Mario to accelerate local work, but it is never
the required path for a workflow to function. If a workflow only works with
Claude Code, it is not a valid Orion OS workflow.

---

## 13. DOCUMENTATION

**D91. HOW-TO-USE.md is a living document — updated by Orion at every version bump.**
Triggered by: new Orion OS version, new/removed project, command changes, workflow changes.
Orion owns this file. Mario never edits it manually.
Every update includes a Changelog entry + footer version bump, pushed in the same commit as the version change.

---

## 14. SONARCLOUD QUALITY STRATEGY

**D92. SonarCloud quality enforcement — three-layer strategy:**

### Layer 1 — Agents (prevention, before commit)
- **Nestor (VSCode):** SonarQube for IDE extension installed + connected to SonarQube Cloud. Detects issues inline while writing code. Required in bootstrap.
- **Olga (Antigravity):** SonarQube MCP Server installed from Antigravity MCP Store. Detects issues via natural language before saying "Ready to test". Required in bootstrap.
- Goal: zero Sonar issues reach CI.

### Layer 2 — CI (enforcement, on PR)
- Bruno runs SonarCloud scan on every PR to `staging` and `main`.
- Quality Gate must pass before Mario can merge.
- If Quality Gate fails → fix in `develop`, never skip or bypass.

### Layer 3 — Orion (deploy-only coordination)
- **Orion does NOT use SonarCloud MCP during normal development.**
- Orion's SonarCloud role is limited to **critical deploy moments**: when Quality Gate blocks a release and Orion must triage which issues to fix vs. temporarily exclude with justification.
- Orion never excludes files from coverage without creating a follow-up issue with tests.
- Orion never marks a Hotspot as "Safe" without confirming with Mario.

### Sonar issue assignment rule
- Angular/frontend issues → Olga
- NestJS/backend issues → Nestor
- Orion triages ambiguous cases

---

## 15. SECURITY INCIDENTS

**D93. Security incident response — structured protocol for third-party provider breaches.**
Full protocol: `workflows/security-incident.md`

Key rules:
- Rotate credentials at SOURCE first, then update Vercel/provider env vars.
- Rotation order: GitHub tokens → OAuth secrets → JWT secrets → DB credentials → email keys → AI API keys.
- Every incident is logged in `logs/post-mortems.jsonl`.
- All env vars containing secrets must be marked as `sensitive` in Vercel.
- Env vars NOT marked sensitive are treated as potentially exposed in any breach.

First incident documented: Vercel CDN breach, April 20, 2026 (ShinyHunters, via Context.ai).

---

## 16. SYSTEM / INSTANCE SEPARATION (v2.0.0)

**D94. Public system layer lives in `Mjosuex85/orion-os` — separate repo from this instance.**
Reason: the system layer (agents, skills, workflows, templates, universal decisions) is
reusable. The instance layer (projects, logs, metrics, personal context) is not. v3.0.0
requires both to be cleanly separable, so v2.0.0 makes the cut now while it is still cheap.

Key rules:
- `Mjosuex85/orion-os` is public, MIT, the product.
- `Mjosuex85/orion` (this repo) is private, the instance, the diary.
- Public repo was created from scratch with no shared git history. Reason: the private
  repo's history mixes project work and learning sessions — not appropriate for a public
  product. The private repo retains full history.
- Sync mechanism (cherry-pick / Action / subtree) is **deferred** until 2-3 manual
  migrations show the real pattern of changes. Premature automation hides the data.

**D95. System layer is generic; `/examples` carries real anonymized projects.**
Reason: a generic-only public repo looks dead. Real users need to see what an actual
working instance looks like, not just empty templates. `/examples` solves this without
leaking real project names into the system code itself.

Key rules:
- System code (agents, skills, workflows, templates) uses generic terms: "the founder",
  "the project", "the instance".
- `/examples` contains anonymized real projects (ex: example-polyrepo, example-monorepo)
  that show full Orion OS instances in action.
- No real project names (GameOn, NutriApp, etc.) appear in the system code in the public repo.
- `/examples` is migrated case by case during v2.x, not all at once.

---

## 17. KANBAN AND ISSUE TRACKING

**D96. One GitHub Project (Kanban) per active project in Orion OS.**
Reason: each project has isolated context. A shared board across projects generates noise and
doesn't scale. Consistent with the multi-project isolation principle (D88).

Rules:
- GitHub Project lives at account level (`Mjosuex85/Projects/<ProjectName>`) — not inside any repo.
- Project name matches the project name in Orion OS exactly (GameOn, NutriApp, PortfolioMV).
- Standard columns — always the same across all projects:
  `Backlog → Ready → In progress → In review → Done`
- Orion references the project's issues by repo (D97) — not by board URL.
- Board creation is manual via GitHub UI until GitHub Projects v2 GraphQL is available via MCP (D98).

**D97. The issues repo for each project is declared in `projects/<project>.md`.**
Orion reads and creates issues by pointing to that repo via GitHub MCP.
No board URL needed — the repo name is the reference.

Format in project files:
```
ISSUES REPO: Mjosuex85/<repo-name>
KANBAN:      GitHub Projects > <ProjectName> (account level)
```

**D98. GitHub Projects v2 GraphQL — deferred tooling.**
The GitHub MCP currently uses REST API — Projects v2 requires GraphQL.
Until MCP support lands, board creation is a manual step in `workflows/project-init.md`.
When GraphQL becomes available: Orion automates board creation on `"Orion iniciar proyecto"`.
This is a tooling constraint, not a permanent design choice.

---

## 18. CONVENTIONS

**D99. Decision prefix convention — all project-specific decisions use `<PREFIX>-D<N>` format.**

| Project | Prefix | Example |
|---------|--------|---------|
| Universal | `D` | `D96` |
| GameOn | `GN-D` | `GN-D1` |
| NutriApp | `N-D` | `N-D1` |
| PortfolioMV | `PM-D` | `PM-D1` |

Rule: new projects always get a prefix defined at registration time in Orion.
Prefix format: short project abbreviation + hyphen + D + number.

---

## 19. PROJECT VISION (v2.2.0)

**D100. Every project has a `vision.md` — frozen original pitch, anchor against drift.**

Reason: as projects evolve, technical decisions and operational context (`<name>.md`,
`<name>-decisions.md`) accumulate and slowly drift away from the original product idea.
The `vision.md` exists to catch that drift early — it is the immutable reference Mario
can compare against in any strategic session to ask "are we still building what we set
out to build?".

Key rules:
- File path: `projects/<name>/<name>-vision.md`.
- Content: Mario's **original pitch in his own voice**, unedited, dated.
- **Frozen by default.** Never overwritten — only appended with a new dated entry if the
  vision consciously changes. Old entries remain visible.
- Orion never rewrites the vision in its own tone. The voice belongs to the founder.
- If vision is not yet fully written, file is created as a **stub** with seed idea + TODO
  block — explicit, never hidden.
- Distinct from `<name>-ideas.md` (living backlog of what could be built next). Never merge
  the two — frozen vision and living backlog serve different purposes.
- New projects: `vision.md` is part of the standard scaffold (5 files per project).

---

## 20. FUTURE

**D40. Orion as multi-project architect.** ✅ Active since v1.4.0.

**D43. GitHub Actions CI/CD + SonarCloud is standard for all projects.**

---

*Orion OS v2.2.0 — Last updated: May 7, 2026 — Session 37*
*New: D100 (project vision.md as fifth standard file)*
