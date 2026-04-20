# workflows/security-incident.md
# Security Incident Response Protocol

> Orion OS — applies to any third-party provider incident (Vercel, Supabase, GitHub, etc.)
> Triggered when: a provider reports unauthorized access, data breach, or credential exposure.

---

## TRIGGER CONDITIONS

Execute this protocol when:
- A provider publishes a security bulletin affecting your account
- Suspicious activity detected in deployments, logs, or access history
- Third-party tool used by a team member is compromised (supply-chain attack)
- Mario reports a possible credential leak

---

## PHASE 1 — ASSESS (first 30 minutes)

1. Read the provider's official bulletin — identify: attack vector, affected systems, data at risk
2. Inventory all projects deployed on the affected provider
3. For each project, list all env vars by name and classify:
   - 🔴 HIGH: DB credentials, JWT secrets, OAuth secrets, API keys with billing
   - 🟡 MEDIUM: API keys without billing, public-facing tokens
   - 🟢 LOW: build config, non-secret env vars
4. Check activity logs / deployment history for anomalies
5. Check provider's OAuth app list for unknown authorized apps

**Output:** risk matrix per project (HIGH / MEDIUM / LOW)

---

## PHASE 2 — CONTAIN (immediate)

For any 🔴 HIGH credential:

| Credential type | Where to rotate |
|---|---|
| JWT secrets | Generate new random 256-bit strings, update in Vercel env vars |
| Database URL / password | DB provider dashboard → rotate password → update connection string |
| Google OAuth Client Secret | Google Cloud Console → Credentials → Regenerate |
| GitHub Token | GitHub → Settings → Developer Settings → Tokens → Revoke + new |
| Supabase anon/service key | Supabase → Project Settings → API → Reset key |
| Gemini / OpenAI API key | AI provider dashboard → revoke + generate new |
| Resend API key | Resend dashboard → API Keys → revoke + new |

**Rule: rotate the credential at the SOURCE first, then update Vercel (or wherever it's used).**
Never update Vercel first — the old credential stays active until revoked at source.

---

## PHASE 3 — ROTATE (ordered by risk)

Execute in this order:
1. Any GitHub tokens (can access repos)
2. OAuth Client Secrets (Google, GitHub OAuth apps)
3. JWT secrets (active sessions may be forged)
4. Database credentials (data integrity risk)
5. Email sending keys (Resend, SendGrid — spam/phishing risk)
6. AI API keys (billing risk)
7. All other encrypted env vars

For each rotation:
- [ ] Revoke at source
- [ ] Generate new credential
- [ ] Update in Vercel (Settings → Environment Variables)
- [ ] Trigger a redeploy to pick up new values
- [ ] Verify the service is working post-rotation

---

## PHASE 4 — VERIFY

1. Test each affected service in production after rotation
2. Review deployment logs for errors post-rotation
3. Confirm no unauthorized deployments in the last 48h
4. Verify Google Workspace OAuth apps — remove unknown apps
5. Check: `110671459871-30f1spbu0hptbs60cb4vsmv79i7bbvqj.apps.googleusercontent.com` (Vercel April 2026 incident specific)

---

## PHASE 5 — DOCUMENT

1. Log the incident in `logs/post-mortems.jsonl`
2. Note: provider, date, affected projects, credentials rotated, time to full rotation
3. Identify: was there a gap in our protocol? Update this file if yes.
4. Update project-level decisions if any credential management rule changes

---

## KNOWN INCIDENTS

### Vercel CDN Breach — April 20, 2026
- **Vector:** Context.ai (AI platform) compromised → employee Google Workspace account → Vercel internal systems
- **Attributed to:** ShinyHunters (claimed, sold data for $2M)
- **Affected projects:** portfoliomv, gameon, gameon-api, nutriapp, wanderlust-menu-api, wanderlust-menu, e-commerce-api
- **Risk level per project:**
  - 🔴 gameon-api: JWT secrets, DATABASE_URL, GOOGLE_CLIENT_SECRET, RESEND_API_KEY
  - 🔴 portfoliomv: GITHUB_TOKEN_PORTFOLIOMV, GEMINI_API_KEY, SUPABASE keys
  - 🔴 wanderlust-menu-api: DB credentials, JWT secrets, GOOGLE OAuth, RESEND
  - 🟡 e-commerce-api: DB credentials, API_KEY, ACCESS_TOKEN (project inactive)
  - 🟢 gameon, wanderlust-menu: only build config vars
  - 🟢 nutriapp, appcountries, weather-app: no env vars or inactive
- **Actions taken:** full credential rotation ordered (Session 26)
- **Reference:** https://vercel.com/kb/bulletin/vercel-april-2026-security-incident

---

## NOTES FOR v3.0.0

When Orion OS runs as a web product:
- Secret scanning must be automated (GitHub Advanced Security or similar)
- Env vars should be marked as `sensitive` by default in the onboarding wizard
- Rotation reminders should be part of the dashboard (scheduled alerts)
- This protocol becomes an automated runbook triggered by provider webhooks

---

*Orion OS — workflows/security-incident.md*
*Created: April 20, 2026 — Session 26 — D93*
