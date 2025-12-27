# SAML (Operator Notes + Troubleshooting)

SAML is usually where “it should work” turns into **“why is the SP mad today?”**  
My approach is consistent: confirm the hard mismatches first, then prove signing/claims, then confirm app authorization.

---

## When I choose SAML
I expect SAML when:
- enterprise SaaS app supports SAML “classic” integrations
- the app wants **XML assertions** and relies on IdP claims
- the org needs mature controls (conditional access, MFA, device posture) enforced at the IdP

If the app is a modern API-first integration, I usually prefer **OIDC** instead.

---

## What breaks SAML most often (in the real world)

### 1) ACS URL mismatch
**Symptom:** user signs in at IdP, then app errors or loops.  
**Cause:** the **Reply URL / ACS URL** in the IdP doesn’t match what the SP expects.

### 2) Entity ID / Audience mismatch
**Symptom:** “invalid audience” / “audience restriction failed.”  
**Cause:** Entity ID (SP identifier) doesn’t match the app’s configured audience.

### 3) Signing expectations mismatch (assertion vs response)
**Symptom:** app rejects message even though login “worked.”  
**Cause:** SP expects **Response signed** vs **Assertion signed** (or both).  
Also common: cert changed/expired and SP still trusts the old one.

### 4) NameID format mismatch
**Symptom:** app can’t identify the user (or maps to the wrong account).  
**Cause:** app expects a specific NameID format:
- emailAddress
- persistent
- unspecified
…and you’re sending something else.

### 5) Claim mapping mismatch (UPN vs email vs immutable ID)
**Symptom:** authentication succeeds, but app treats user as unknown or unauthorized.  
**Cause:** claims don’t match the app’s user matching logic (email vs UPN vs employeeID).

### 6) App authorization (the silent killer)
**Symptom:** user authenticates, but access is denied inside the app.  
**Cause:** roles/groups/entitlements aren’t mapped correctly.

> Authentication ≠ Authorization. I always confirm app role mapping after SAML validation.

---

## My validation checklist (the order I check)

### Step 1 — Confirm “must match” values
- **ACS / Reply URL**
- **Entity ID / Audience**
- **IdP Issuer**
- **Binding** (HTTP-Redirect vs HTTP-POST)
- **Clock skew** tolerance (if the app is strict)

### Step 2 — Confirm signing + certificate
- what the SP requires:
  - Response signed?
  - Assertion signed?
  - both?
- confirm cert:
  - correct cert in metadata
  - not expired
  - SP trusts the same cert

### Step 3 — Confirm NameID + claims
- NameID format expected by the app
- claim values:
  - email / UPN / immutable identifier
  - group/role claims (if used)

### Step 4 — Confirm app authorization mapping
- does the user land in the correct app role?
- are groups mapped to roles properly?
- is there a “default role” requirement?

---

## Quick troubleshooting map (symptom → likely cause)

| Symptom | Likely cause | First place I look |
|---|---|---|
| Login loop back to app | ACS/Reply URL mismatch | IdP app config + SP SSO settings |
| “Invalid audience” | Entity ID / Audience mismatch | SAML audience restriction / Entity ID |
| “Signature invalid” | wrong cert or wrong signing setting | signing settings + cert metadata |
| “User not found” | NameID/claim mismatch | NameID format + claim mapping |
| Auth success but denied | roles/entitlements missing | app authorization mapping |

---

## Evidence I capture (sanitized)
I capture **just enough proof** to show maturity without exposing identifiers.

### Minimum evidence (recommended)
- **Config summary** (no IDs): ACS pattern, Entity ID pattern, signing expectation, NameID format
- **Sign-in outcome**: success/failure category + policy decision outcome (no identifiers)
- **Change record**: before/after summary + rollback note

### If needed (only when necessary)
- redacted screenshot showing:
  - signing setting toggles (assertion/response)
  - NameID format
  - claim mapping keys (not values)

> [!IMPORTANT]
> I do **not** publish raw SAML assertions, full metadata with IDs, or screenshots that include tenant/app/object IDs.

---

## Operator notes (what I watch first)
If I’m dropped into a ticket and need speed, I check in this order:
1) **ACS URL + Entity ID**  
2) **Signing requirement + cert** (expired? wrong? response vs assertion?)  
3) **NameID format**  
4) **Claim mapping** (email vs UPN vs immutable ID)  
5) **App authorization** (user authenticates but has no role)

---

## Optional maturity upgrades (when I have tenant/app access)
Using my lab tenant, I can capture sanitized proof that I operate with discipline:
- IdP sign-in event showing outcome + policy decision (sanitized)
- config summary export (redacted, no IDs/secrets)
- before/after change summary + rollback plan
- verification result recorded (what “good” looks like)

Next: if you want the modern equivalent, go to **OIDC**:
- [02_OIDC.md](./02_OIDC.md)
