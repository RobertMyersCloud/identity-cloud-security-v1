# Start Here (2 minutes)

If you’re reading this, it’s probably because **a user can’t access an app** or **provisioning isn’t doing what it should**.  
This pack is how I approach the problem as an IAM operator: **simple first, fast isolation, proof-based validation.**

---

## What this pack is (in one sentence)
A practical guide to **SSO (SAML/OIDC)** and **provisioning (SCIM)** that shows how I troubleshoot issues and document results with **sanitized evidence**.

---

## My workflow (the way I actually run this)
### Step 1 — Decide what’s broken
- **SSO issue:** user can’t sign in / session isn’t established
- **Provisioning issue:** user can sign in, but account/roles aren’t created/updated/disabled correctly

### Step 2 — Isolate the failure domain
I don’t “guess.” I isolate the failure domain:
- **IdP side** (Entra/Okta sign-in + policy outcomes)
- **Protocol config** (SAML/OIDC settings that must match)
- **App authorization** (roles/entitlements mapping — authentication ≠ access)
- **Provisioning pipeline** (SCIM auth, endpoint, schema, throttling)

### Step 3 — Verify the fix (success criteria)
I confirm success in the same order every time:
- sign-in result is correct
- protocol validation checks pass
- app authorization is correct
- provisioning run behaves as expected (if SCIM)

### Step 4 — Record what changed (so it’s repeatable)
I log:
- what I changed
- why I changed it
- what “good” looks like (verification)
- how I would roll it back

---

## Pick your path (choose one)

### Path A — I just need the right “first checks”
Start with the protocol notes:
- **SAML:** [01_SAML.md](./01_SAML.md)
- **OIDC:** [02_OIDC.md](./02_OIDC.md)
- **SCIM:** [03_SCIM.md](./03_SCIM.md)

### Path B — I’m troubleshooting an outage / incident
Use the operator sequence:
1. Runbooks: [04_Troubleshooting_Runbooks.md](./04_Troubleshooting_Runbooks.md)  
2. Verify: [05_Verification_Checklist.md](./05_Verification_Checklist.md)  
3. Capture proof: [evidence/README.md](./evidence/README.md)

### Path C — I’m doing a clean implementation / go-live
Use the governance sequence:
1. Controls + policy references: [06_Controls_and_Policy_References.md](./06_Controls_and_Policy_References.md)  
2. Logging + telemetry plan: [08_Logging_and_Telemetry.md](./08_Logging_and_Telemetry.md)  
3. Change + rollback discipline: [09_Change_Management_and_Rollback.md](./09_Change_Management_and_Rollback.md)

---

## Evidence standard (what I capture)
I keep proof **minimal** and **safe-to-share** (usually 1–3 items per scenario):
- sign-in outcome summary (no tenant/app/object IDs)
- config summary (no secrets, no identifiers)
- before/after change summary + rollback note
- SCIM run result summary by category (no raw payloads)

> [!WARNING]
> I do **not** publish secrets, tenant/app/object IDs, real emails, internal domains, tokens, or raw assertions/responses.

---

## Definitions I use (so we’re speaking the same language)
- **IdP**: Identity Provider (Entra ID / Okta)
- **SP / Relying Party**: the app being accessed
- **Authentication**: “who you are”
- **Authorization**: “what you can do” (roles/entitlements)
- **Provisioning**: lifecycle automation (create/update/disable via SCIM)

---

## If you want the fastest win
If you only do one thing: **separate authentication from authorization.**  
Most “SSO is broken” tickets are actually “user authenticated but has no role/entitlement in the app.”
