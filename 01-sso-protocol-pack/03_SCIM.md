# SCIM (Operator Notes + Lifecycle Troubleshooting)

SCIM is where identity **lifecycle** breaks — not login.

When SSO works but users can’t access the app, SCIM is usually the reason:
- user never provisioned
- attributes are wrong
- deprovisioning didn’t happen
- identifier conflicts exist

My goal is to determine whether the failure is **auth**, **endpoint**, **schema**, or **rate/behavioral**.

---

## When I use SCIM
I expect SCIM when:
- the app supports automated provisioning
- user create/update/disable must be lifecycle-driven
- attributes (department, role, manager, etc.) matter
- access removal must be enforced automatically

If provisioning is manual or CSV-based, I document that risk explicitly.

---

## What breaks SCIM most often (real-world)

### 401 / 403 — Authentication or permissions
**Meaning:** SCIM token is invalid or lacks permissions  
**Common causes:**
- expired token
- wrong token type
- insufficient API permissions

### 404 — Endpoint / base URL wrong
**Meaning:** IdP can’t find the SCIM service  
**Common causes:**
- wrong base URL
- missing `/scim/v2` or vendor-specific path
- environment mismatch (prod vs test)

### 409 — Duplicate / identifier conflict
**Meaning:** user already exists under a different identifier  
**Common causes:**
- email vs username mismatch
- immutable ID mismatch
- legacy/manual accounts already present

### 400 — Schema or attribute mapping error
**Meaning:** payload doesn’t match what the app expects  
**Common causes:**
- required attribute missing
- wrong attribute format
- custom schema mismatch

### 429 — Throttling / rate limits
**Meaning:** too many requests too fast  
**Common causes:**
- large group sync
- aggressive retry behavior
- no backoff handling

---

## My validation checklist (the order I check)

### Step 1 — Confirm provisioning is enabled
- provisioning turned on in IdP
- correct provisioning mode selected (automatic)
- credentials/token present and valid

### Step 2 — Validate endpoint configuration
- base URL correct
- SCIM version supported
- test connection succeeds (if supported)

### Step 3 — Review provisioning logs
I look for:
- HTTP status patterns
- create vs update vs delete behavior
- retry behavior and backoff

### Step 4 — Validate attribute mappings
- required attributes mapped
- correct identifier selected (email, username, immutable ID)
- group/role mappings aligned with app expectations

### Step 5 — Confirm app-side behavior
- user exists in app after provisioning
- role/entitlement applied
- disable/deprovision behaves as expected

---

## Quick troubleshooting map (status → action)

| Status | Meaning | What I check first |
|---|---|---|
| 401 / 403 | Auth/permissions | token validity + API rights |
| 404 | Endpoint wrong | base URL + path |
| 409 | Duplicate | identifier strategy |
| 400 | Schema issue | attribute mapping |
| 429 | Throttling | backoff + sync scope |

---

## Evidence I capture (sanitized)

### Minimum evidence (recommended)
- **Provisioning run summary**:
  - success / failure
  - HTTP category only (no payloads)
- **Attribute mapping summary**:
  - attribute names only
- **Change record**:
  - what I changed
  - why
  - rollback note

### Only if necessary
- redacted screenshot showing:
  - provisioning enabled
  - mapping screen (names only)
  - error category (not payload)

> [!IMPORTANT]
> I never publish:
> - SCIM tokens
> - raw payloads
> - user identifiers
> - internal domain names

---

## Operator notes (what I watch first)
When time matters, I check:
1) provisioning enabled + credentials valid  
2) base URL correct  
3) identifier strategy (email vs immutable ID)  
4) required attributes mapped  
5) deprovision behavior actually enforced  

---

## Optional maturity upgrades (when I have tenant/app access)
Using my lab tenant, I can show:
- provisioning run result summary (sanitized)
- before/after attribute mapping diff
- identifier strategy decision documented
- deprovision test result (disabled user behavior)

If the problem is login-related instead of lifecycle, go back to:
- **SAML:** [01_SAML.md](./01_SAML.md)
- **OIDC:** [02_OIDC.md](./02_OIDC.md)
