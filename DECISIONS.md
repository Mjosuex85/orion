# DECISIONS.md — Orion OS

Global rules that apply to ALL projects orchestrated by Orion OS.
These rules are the foundation of the framework — they are inherited, not overridden.

---

## 1. HIERARCHY & ROLES

**G1. The Director is the sole decision-maker for business and vision.**
Reason: Product direction belongs to them. Orion analyzes and proposes — the Director decides.

**G2. Orion is the permanent CTO — not tied to a single project.**
Reason: Orion accumulates experience across projects. Its value grows with each new project.

**G3. Tech Leads (Nestor, Olga) evaluate within their domain — they don't decide global architecture.**
Reason: Tech Leads have technical judgment within their area, but global architecture is decided by Orion with the Director.

**G4. Specialized agents (Dev, Tester, Security) only execute what their Tech Lead defines.**
Reason: Execution without misinterpretation. The Tech Lead owns the quality of the prompt.

**G5. Each project has its own context in `projects/name.md`.**
Reason: Orion needs project-specific context to make correct decisions.

---

## 2. WORKFLOW

**G6. The flow is always: Orion creates issue → agent executes → Director validates → Orion closes.**
Reason: Human validation is mandatory. No issue is closed without Director approval.

**G7. All issues for a project live in the project's main repo.**
Reason: One single place per project to manage work.

**G8. Standard commit format for metrics:**
```
[AGENT] type(scope): description ref #XX | size: S/M/L/XL
```
Example:
```
[NESTOR] fix(auth): cookie path corrected ref #42 | size: S
```
Sizes: S=~1k tokens, M=~3k, L=~8k, XL=~15k
Reason: Enables automatic consumption tracking via GitHub Actions at zero token cost.

**G9. Maximum 2 issues In Progress simultaneously per project.**
Reason: Limited WIP avoids fragmented context and improves quality.

**G10. Every issue includes: context + agent prompt + test plan.**
Reason: Without the prompt, the agent lacks context. Without the test plan, the Director doesn't know what to verify.

---

## 3. GIT

**G11. `main` is only touched on planned deploys.**
Reason: Autodeploy is active in production. All work goes to `develop`.

**G12. Agents always do `git pull origin develop` before working.**
Reason: Prevents merge conflicts.

**G13. Commits use `ref #XX` — never `closes #XX` or `fixes #XX`.**
Reason: Issue closing is done manually by Orion after Director validation.

---

## 4. QUALITY & SECURITY

**G14. Never commit credentials, tokens, or secrets.**
Reason: Critical and irreversible security risk.

**G15. The Director approves before Orion uses any MCP.**
Reason: MCPs consume resources. The Director controls the token budget.

**G16. Orion asks permission before making direct changes to GitHub.**
Reason: The Director owns the code. Exception: active production deploy emergencies.

---

## 5. TEAM SCALING

**G17. Adding a new agent = create their file in `agents/` + define their role in the project.**
Reason: The system scales by hierarchy. Each new agent inherits global rules.

**G18. Specialized agents (Tester, Security, DevOps) report to their Tech Lead.**
Reason: Nestor manages his backend team. Olga manages her frontend team. Orion coordinates Tech Leads.

---

## 6. METRICS

**G19. Every agent commit follows the format with `size:` for automatic tracking.**
Reason: No data, no optimization. Tracking costs 0 additional tokens.

**G20. Logs live in `orion/logs/usage.jsonl` — one entry per line.**
Reason: JSONL allows atomic append without corruption risk.

---

*Last updated: March 26, 2026 — Orion*
*This file is the foundation of the Orion OS framework.*
