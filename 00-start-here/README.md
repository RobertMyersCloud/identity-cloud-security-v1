# Start Here (2 minutes)

If you’re reviewing me for IAM / identity / cloud security roles, this repo is meant to feel like **internal documentation**:
**clear steps, clear proof, and no secret leakage**.

---

## Pick a review path

### 1) Hiring manager view (fast)
1. Read this page
2. Open the SSO pack: [01-sso-protocol-pack/README.md](../01-sso-protocol-pack/README.md)
3. Skim what’s next: [90-roadmap.md](../90-roadmap.md)

### 2) Technical reviewer view (how I troubleshoot)
1. SSO pack → runbooks → verification → evidence  
   [01-sso-protocol-pack/README.md](../01-sso-protocol-pack/README.md)

### 3) Governance / audit-minded view (how I stay safe + consistent)
1. Evidence rules (below)
2. Verification logs (what I capture and why)
3. Decisions + changelog discipline (why I chose the approach + what changed)

---

## How I work (my operator workflow)
When something breaks, I don’t guess. I isolate the failure domain and prove it.

1. **Decide what’s broken**
   - Is it **SSO** (login/session) or **Provisioning** (create/update/disable accounts)?

2. **Isolate the failure domain**
   - **IdP** (Entra/Okta): sign-in + policy outcome
   - **Protocol config** (SAML/OIDC): endpoints, signing, token/assertion expectations
   - **App authorization**: roles/entitlements mapping (auth success but no access)

3. **Verify success criteria**
   - “Good” has a definition (not vibes). I document what success looks like.

4. **Capture sanitized proof**
   - Minimal, safe-to-share evidence (usually 1–3 items)

5. **Record the change + rollback**
   - What I changed, why, how to undo it (change discipline matters)

> [!TIP]
> If you only have 60 seconds, open the SSO pack and follow the troubleshooting flow:
> [01-sso-protocol-pack/README.md](../01-sso-protocol-pack/README.md)

---

## Evidence rules (non-negotiable)
I use a lab environment to capture proof, but I keep it **safe-to-share**.

### What I *do* include
- Config summaries (endpoints + requirements) **without IDs/secrets**
- Sign-in/provisioning outcomes (success/failure category) **without identifiers**
- Before/after change notes + rollback plan
- Screenshots only when needed, **heavily redacted**

### What I *never* publish
- secrets/tokens/passwords/private keys/certs
- tenant/app/object IDs, subscription IDs
- real emails, internal domains, IPs tied to private systems
- raw SAML assertions / raw JWTs / raw logs with identifiers

> [!IMPORTANT]
> “Sanitized” means I remove or replace identifying values (IDs, emails, domains) and only keep what proves the technical point.

---

## Decisions vs Changelog (quick clarity)
- **Decisions** = “I chose this approach because…” (assumptions + tradeoffs)
- **Changelog** = “I changed this on this date…” (what changed + impact)

I keep both lightweight, but consistent.

---

## Next up
Go to the SSO Protocol Pack:
➡️ [01-sso-protocol-pack/README.md](../01-sso-protocol-pack/README.md)
