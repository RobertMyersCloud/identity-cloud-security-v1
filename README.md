# identity-cloud-security-v1
IAM • Identity-First Cloud Security • Zero Trust (Operator-grade portfolio)

I’m building an IAM-first cloud security portfolio that reads like internal security documentation:
**clear decisions → repeatable runbooks → verification steps → sanitized evidence**.

> [!IMPORTANT]
> Everything in this repo is **sanitized**.
> I do **not** publish secrets/tokens, raw SAML assertions/JWTs, tenant/app/object IDs, real emails, internal domains, or raw logs containing identifiers.

---

## Start here (90 seconds)
1. **Start Here (2 minutes)** → [00-start-here/README.md](./00-start-here/README.md)  
2. **SSO Protocol Pack (SAML • OIDC • SCIM)** → [01-sso-protocol-pack/README.md](./01-sso-protocol-pack/README.md)  
3. Roadmap / what’s next → [90-roadmap.md](./90-roadmap.md)

---

## What this repo proves
- I think **identity-first** (identity is the control plane)
- I can support real SSO + provisioning integrations (**SAML/OIDC/SCIM**)
- I troubleshoot like an operator: **isolate → verify → document**
- I use governance habits: **evidence discipline, change control, rollback**

---

## Review paths (pick one)
**Hiring manager view**
- Start Here → SSO Protocol Pack → Roadmap

**Technical reviewer view**
- SSO Protocol Pack → Runbooks → Verification → Evidence templates

**Governance / audit-minded view**
- Evidence policy → Verification approach → Decisions + changelog discipline

---

## Repository map (where things live)
- `00-start-here/` → quick tour, how I work, evidence rules
- `01-sso-protocol-pack/` → SAML/OIDC/SCIM deep dives + runbooks + verification + evidence templates
- `90-roadmap.md` → next modules I’m building (kept honest + lightweight)

---

## Evidence policy (non-negotiable)
I keep proof **safe-to-share** and minimal (usually 1–3 items per scenario):
- Config summaries (no IDs/secrets)
- Sign-in/provisioning outcome summaries (no identifiers)
- Before/after change notes + rollback plan

If something requires screenshots, I redact aggressively.
