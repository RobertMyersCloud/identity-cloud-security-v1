# SSO Protocol Pack — SAML vs OIDC vs SCIM (Operator Guide)

I built this pack the way I like to run IAM work in the real world: **clear decisions, repeatable checks, fast isolation, and proof that’s safe to share**.

> [!IMPORTANT]
> **Proof > claims.** When I say “I can troubleshoot SSO,” I also show *how I verify success* and *what evidence I capture* (sanitized).

---

## What this proves (in plain English)
- I can choose the right protocol for the requirement (**SSO vs provisioning**)
- I can isolate failure domains fast (IdP vs protocol config vs app authorization)
- I work like an operator: **verify → evidence → change discipline → rollback**
- I can apply patterns across **Entra ID** and **Okta** (same fundamentals, different UI)

---

## Quick decision guide (when to use what)

| Need | Use | Why |
|---|---|---|
| SSO for modern apps/APIs | **OIDC** | Modern, JSON/REST-friendly, OAuth2-based flows |
| SSO for enterprise SaaS (classic) | **SAML** | Common in enterprise SaaS, XML assertions |
| Automated user provisioning/deprovisioning | **SCIM** | Standard lifecycle API for identity data |

---

## Start here (60 seconds)
1. Open **[00_START_HERE.md](./00_START_HERE.md)**
2. Decide what’s broken: **SSO** (login/session) or **Provisioning** (create/update/deactivate)
3. Use the troubleshooting flow below to isolate the failure domain
4. Capture **sanitized proof** (minimal, safe-to-share)

---

## My “first look” notes (what breaks most often)

### SAML notes
SAML is usually where “it should work” turns into “why is the SP mad today?”

What I check first:
- **ACS URL / Entity ID mismatch**
- **Cert/signature requirements** (expired/wrong cert, response vs assertion signing)
- **NameID format mismatch**
- **Claim mapping mismatch** (email vs UPN vs immutable ID)
- **App authorization** (user authenticates but has no role/entitlement)

### OIDC notes
OIDC is cleaner, but failures are usually very precise:
- **Redirect URI mismatch**
- **Invalid client secret / wrong auth method**
- **Token validation errors** (iss/aud/exp, JWKS rotation)
- **Missing scopes/claims** for the app’s authorization layer

### SCIM notes
Provisioning is where “SSO works” but users still can’t do anything:
- **401/403** = auth/token/permissions
- **404** = wrong endpoint/base URL
- **409** = duplicate / identifier conflict
- **400** = schema or attribute mapping
- **429** = throttling/backoff needed

---

## Optional maturity upgrades (when I have tenant/app access)
I use my lab tenant to capture **sanitized proof** and show operational maturity:

- **IdP logs:** a sign-in event showing outcome + policy decision (sanitized)
- **Config proof:** a config summary (no IDs/secrets) showing endpoints + requirements
- **Change discipline:** a before/after summary + rollback note
- **SCIM proof:** provisioning run result summary (success/failure category only)

I keep proof **safe-to-share and minimal** (usually **1–3 items per scenario**).

> [!WARNING]
> I **do not** publish secrets, tenant/app/object IDs, real emails, internal domains, tokens, or raw assertions/responses.

---

## Troubleshooting flow (high level)

```mermaid
flowchart TD
  A[User cannot access app] --> B{SSO or provisioning?}

  B -->|SSO| C{Protocol?}

  C -->|SAML| S1[Confirm ACS URL + Entity ID]
  S1 --> S2{IdP sign-in success?}
  S2 -->|No| S3[Review IdP sign-in logs + policy outcomes]
  S2 -->|Yes| S4[Validate signing + cert expectations]
  S4 --> S5{App access granted?}
  S5 -->|No| S6[Check roles/entitlements + claim mapping]
  S5 -->|Yes| Z[Capture sanitized evidence + record change]

  C -->|OIDC| O1[Confirm redirect URI + client config]
  O1 --> O2{IdP sign-in success?}
  O2 -->|No| O3[Review sign-in logs + policy outcomes]
  O2 -->|Yes| O4[Validate token: iss/aud/exp + JWKS]
  O4 --> O5{App access granted?}
  O5 -->|No| O6[Check scopes/claims + authorization mapping]
  O5 -->|Yes| Z

  B -->|Provisioning| P1[SCIM: check provisioning logs]
  P1 --> P2{HTTP pattern?}
  P2 -->|401/403| P3[Auth/token/permissions issue]
  P2 -->|404| P4[Endpoint/base URL issue]
  P2 -->|409| P5[Duplicate/identifier conflict]
  P2 -->|429| P6[Throttling/backoff required]
  P2 -->|400| P7[Schema/attribute mapping issue]
  P3 --> Z
  P4 --> Z
  P5 --> Z
  P6 --> Z
  P7 --> Z
