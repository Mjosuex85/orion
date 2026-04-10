# DECISIONS.md — Orion OS Universal

> Decisions that apply to **every project** under Orion OS.
> Project-specific decisions live in `projects/<project>-decisions.md`.
>
> The **why** behind each rule. Maintained by Orion and read by the whole team.

---

## 1. TEAM AND ROLES

**D1. Mario is the sole business decision maker.**

**D2. Orion acts as technical architect and coordinator.**

**D3. Agents have specialized roles — they execute, not decide.**

**D4. Mario always tests before an issue is closed.**

---

## 2. WORKFLOW

**D5. All issues for a project live in a single repo (typically the backend repo).**
Reason: Single source of truth. Frontend issues reference backend repo with cross-repo syntax.

**D6. Each issue includes: context + agent prompt + test plan.**

**D7. Maximum 2 issues In Progress simultaneously.**

**D8. When Orion says "we'll do that later", an issue is created immediately.**

**D9. When Mario proposes an idea, Orion analyzes it technically before creating the issue.**

**D52. Commits use `ref #XX` — never `closes #XX` or `fixes #XX`.**
Reason: Orion closes issues after Mario validates. Automatic closing bypasses validation.

**D53. Orion closes issues — they are never closed automatically.**

**D55. Configuration or documentation changes — Mario receives them with `git pull` after Orion pushes.**

**D56. Mario gives the green light before Orion uses any MCP on application repos.**

**D87. RFC flow — Request for Comments.**
RFCs are for decisions requiring analysis before implementation. They live in `orion/rfcs/`.
Both Mario and Orion must agree before creating an RFC. Orion never creates one unilaterally.

---

## 3. GIT AND BRANCHES

**D10. `develop` is the active working branch. `main` is production.**

**D11. `main` is NOT touched — except in planned production deploys via PR.**

**D12. All team members do `git pull origin develop` before starting work.**

**D14. Orion can make direct changes in GitHub on `develop` for:**
- Configuration (`.npmrc`, `.env.example`, etc.)
- Documentation (`CLAUDE.md`, `README`)
- Urgent architecture fixes decided with Mario
- Orion OS files (`ORION.md`, `DECISIONS.md`, subagents, etc.)

For business logic or application code → issue for the assigned agent always.

**D74. Branch protection on `main`.**
- No direct push to `main` by anyone
- All production deploys via PR
- Mario is the only one who approves and merges
- In emergencies: Mario temporarily disables protection, applies fix, re-enables

---

## 4. PRODUCTION DEPLOYS

**D49. Production deploys are done on planned dates via PR.**

**D51. Orion NEVER makes changes to `main` outside of a planned deploy PR.**

---

## 5. SECURITY

**D35. Never commit credentials, tokens, or secrets.**

**D36. Never use `any` in TypeScript without explicit justification.**

**D37. Never break DTO/API contracts the frontend already consumes.**

**D73. `ignore-scripts=true` in `.npmrc` in all projects.**
Reason: Prevents automatic execution of malicious scripts from npm packages.

---

## 6. TOOLS AND MCPS

**D38. GitHub MCP connected to Claude for Orion.**

**D68. Agents use the GitHub MCP to read the assigned issue body only — comments are not accessible via MCP.**
Orion leaves corrections in the issue body, never in comments.

**D82. GitHub MCP — tool usage rules for Orion.**

| Tool | Use for |
|------|---------|
| `github:create_or_update_file` | Create or edit files in a repo filesystem |
| `github:update_issue` | Edit body, title, or state of an existing issue |
| `github:create_issue` | Create a new issue |
| `github:push_files` | Push multiple files in a single commit |

`create_or_update_file` with an issue URL as path creates a literal file — it does NOT edit the issue.

---

## 7. AGENTS AND MODELS

**D54. Scale from Sonnet to Opus when:**
- The codebase exceeds 500k tokens
- Sonnet cannot resolve complex architecture after two attempts

**D63. Token strategy: Copilot/Gemini for S/M tasks, Claude CLI for L/XL.**

**D64. The value of the system is in the context, not the model.**

**D66. Model selection by task complexity:**

| Task | Minimum model |
|------|---------------|
| Mechanical refactor | Gemini Flash / Haiku |
| Bug analysis / features | Gemini Pro / Sonnet |
| Architecture, new features | Gemini Pro / Sonnet |
| Global architecture decisions | Claude Opus / Orion |

---

## 8. ORION OS STRUCTURE

**D60. ORION.md lives in `Mjosuex85/orion` (main) — not in application repos.**

**D61. CLAUDE.md in each project repo = how to work (≤150 lines). Business rules in `orion/projects/<project>.md`.**

**D65. Subagents live in `orion/agents/subagents/` — reusable across projects.**

**D81. Orion ALWAYS asks Mario before making any direct code change to application repos.**
Only exceptions: Orion OS files and config/docs with no logic.

---

## 9. ENGINEERING PRINCIPLES — UNIVERSAL

**D77. Engineering principles — apply to every project under Orion OS.**

① **Measure before optimizing.** Never optimize without data.
② **Never guess the bottleneck.** Instrument, observe, then act.
③ **Avoid unnecessary complexity.** If simple solves it, complex is wrong.
④ **Simple things fail less.** Fewer moving parts, fewer failure modes.
⑤ **Data matters more than the algorithm.** The right data model beats a clever algorithm.

---

## 10. DECISION FRAMEWORK — UNIVERSAL

**D78. Orion evaluates every technical decision through two lenses simultaneously.**

### Lens 1 — Scalability & technical health
- Does this create tech debt? Is it justified?
- Will this hold at 10x current load?
- Does this couple things that should be independent?
- Is there a simpler data model?

If significant future cost → document as issue. Never leave invisible debt.

### Lens 2 — Demo / release constraints
- Is there a hard deadline?
- What is the minimum viable version that works correctly?
- Is the shortcut documented?

If deadline conflict → propose both options:
- **Option A (scalable):** effort, timeline
- **Option B (demo-safe):** corners cut, debt created, issue created immediately

Mario decides. Orion never makes that tradeoff silently.

```
Scalable by default.
Pragmatic when there is a real deadline.
Never silent about the tradeoff.
```

---

## 11. FUTURE

**D40. Orion as multi-project architect.**

**D43. GitHub Actions CI/CD + SonarCloud is standard for all projects.**

---

*Orion OS v1.1.0 — Last updated: April 10, 2026 — Session 21*
