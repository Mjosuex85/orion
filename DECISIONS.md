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

**D68. Agents read issue body only via MCP — corrections go in body, not comments.**

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

**D81. Orion asks Mario before any direct code change to app repos.**

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

## 14. FUTURE

**D40. Orion as multi-project architect.** ✅ Active since v1.4.0.

**D43. GitHub Actions CI/CD + SonarCloud is standard for all projects.**

---

*Orion OS v1.5.0 — Last updated: April 15, 2026 — Session 24*
*New: D91 (HOW-TO-USE versioning protocol)*
