# identity-cloud-security-ops-finops

Operator-grade, proof-backed identity-first cloud security operations with governance and cost discipline.

## Scope
This repository demonstrates:
- **Identity as the control plane** (Microsoft Entra ID + Azure authorization)
- **Operational security execution** with audit-ready evidence (expected vs observed)
- **Governance and boundary discipline** (RBAC scope, change intent, rollback)
- **FinOps guardrails** that support secure operations (budgets, tagging, cost visibility)

## Relationship to the portfolio ladder
- **Foundation:** `security-cloud-foundations` (baseline controls + safe operation)
- **This repo (Ops):** `identity-cloud-security-ops-finops` (operator-grade execution + runbooks)
- **Capstone:** `zero-trust-architecture-capstone` (program-level architecture and strategy)

## How to review (fast path)
1. Start with `security-cloud-foundations/00-START-HERE.md`
2. Then review one lab + one runbook here to confirm consistency
3. Use evidence indexes to validate outcomes without guesswork

## Evidence rules
- Sanitized by default (no tenant/sub IDs, domains, emails, hostnames)
- Prefer written validation; screenshots only when they prove state
- Every lab includes rollback and interview-ready explanation
