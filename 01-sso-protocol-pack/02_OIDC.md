# OIDC (Operator Notes + Troubleshooting)

OIDC is cleaner than SAML, but failures are usually **precise**.  
My goal is to isolate whether the break is: **redirect/config**, **token issuance**, **token validation**, or **app authorization**.

---

## When I choose OIDC
I expect OIDC when:
- the app is modern and API-first
- the app uses OAuth2 flows (Authorization Code most common)
- tokens (ID token / access token) are validated with JWKS keys
- scopes/claims drive authorization logic

If the app is older enterprise SaaS and expects XML assertions, I use **SAML** instead.

---

## What breaks OIDC most often (real-world)

### 1) Redirect URI mismatch
**Symptom:** error immediately after login or callback fails.  
**Cause:** redirect URI must match **exactly** (scheme/host/path, sometimes trailing slash).

### 2) Wrong client authentication (secret/cert/method)
**Symptom:** token exchange fails at `/token`.  
**Cause:** invalid client secret, wrong cert, wrong auth method (basic vs post).

### 3) Issuer / Audience mismatch
**Symptom:** token validation fails.  
**Cause:** app expects different `iss` (issuer) or `aud` (audience/client ID).

### 4) JWKS / key rotation issues
**Symptom:** signature validation fails “randomly” after a key changes.  
**Cause:** app caches keys too long or doesn’t refresh JWKS correctly.

### 5) Missing scopes/claims (authorization layer)
**Symptom:** login succeeds but user can’t do anything.  
**Cause:** scopes/claims don’t satisfy the app’s role/permission checks.

> Auth success doesn’t mean the API/app authorizes the user. I always validate the app’s required scopes/claims.

---

## My validation checklist (the order I check)

### Step 1 — Confirm app registration basics
- tenant/authority being used (correct environment)
- **client ID** correct in the app
- **redirect URI** exact match
- response type / flow (Authorization Code w/ PKCE is typical)
- logout URL (optional, but I validate if SLO matters)

### Step 2 — Confirm token issuance works
- authorization request returns code successfully
- token exchange works at `/token`
- confirm expected tokens are returned:
  - ID token (identity)
  - access token (API authorization), if applicable

### Step 3 — Validate token claims (what the app should check)
Minimum checks I expect:
- `iss` (issuer)
- `aud` (audience)
- `exp` / `nbf` (time)
- signature using JWKS
- nonce/state handling (anti-replay / anti-CSRF)

### Step 4 — Confirm scopes/claims for authorization
- scopes granted (for access token)
- roles/groups/claims provided (if app requires them)
- app role mapping logic (app-side) confirmed

---

## Quick troubleshooting map (symptom → likely cause)

| Symptom | Likely cause | First place I look |
|---|---|---|
| Callback error after login | redirect URI mismatch | app config + registered redirect URIs |
| `/token` fails | bad secret / wrong auth method | client auth method + secret/cert |
| “Invalid issuer” | wrong authority/tenant | `iss` expected vs actual |
| “Invalid audience” | wrong client/app ID | `aud` in token vs app config |
| Signature validation fails | JWKS caching/rotation | JWKS refresh + cache behavior |
| Login ok, no access | missing scopes/claims | scopes/roles/claims mapping |

---

## Evidence I capture (sanitized)
I keep it minimal and safe-to-share.

### Minimum evidence (recommended)
- **Config summary** (no IDs): authority pattern, redirect URI pattern, auth method, required scopes
- **Token validation summary**: pass/fail by category (issuer/audience/time/signature) — no raw tokens
- **Change record**: before/after summary + rollback note

### If needed (only when necessary)
- redacted screenshot showing:
  - redirect URI entries
  - auth method type (secret/cert)
  - scope list (names only)

> [!IMPORTANT]
> I do **not** publish raw tokens, secrets, client IDs, tenant IDs, or screenshots that include those values.

---

## Operator notes (what I watch first)
If I need speed, I check:
1) **redirect URI** exact match  
2) **client auth** (secret/cert + method)  
3) token validation: `iss` / `aud` / `exp` + signature  
4) scopes/claims for the app’s authorization layer  
5) app-side role mapping (if applicable)

---

## Optional maturity upgrades (when I have tenant/app access)
Using my lab tenant, I can capture sanitized proof that I operate with discipline:
- sign-in event outcome + policy decision (sanitized)
- config export/summary (redacted, no IDs)
- before/after change summary + rollback plan
- verification result recorded (what “good” looks like)

Next: if your issue is lifecycle automation rather than login, go to **SCIM**:
- [03_SCIM.md](./03_SCIM.md)
