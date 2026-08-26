# SECURITY

Mora Core is proprietary software and is being designed for real retail operations involving inventory, sales, employees, commissions, cash, customer data, integrations and eventually fiscal/marketplace workflows. Security is therefore a product property, not a final audit phase.

## Reporting a vulnerability
Do not open a public issue containing credentials, exploit details, customer data or a proof-of-concept that could endanger a live deployment. Report security concerns privately to the project owner through an authorized private channel.

A useful report should include:
- affected component/version/SHA;
- impact and preconditions;
- reproducible steps;
- whether tenant isolation, money, inventory, authentication, authorization or secrets are affected;
- logs/screenshots only after removing sensitive data.

## Security principles
- Secure-by-design and secure-by-default.
- Least privilege and fail closed for security-critical decisions.
- Defense in depth.
- Strong separation of authentication, authorization, object-level authorization and tenant isolation.
- No client-provided tenant identity is trusted without server-side resolution.
- No secrets in source code, frontend/mobile bundles, documentation, logs or error messages.
- Parameterized database access and schema validation at trust boundaries.
- Resource budgets for uploads, API responses, pagination, external calls, AI usage and background work.
- Idempotency for retried writes, webhooks, imports, payments and jobs where duplication would be harmful.
- Audit trails for sensitive business actions.
- Data minimization and explicit lifecycle/retention rules.

## High-risk areas
The following require threat modeling and stronger validation:
- login/session/MFA and account recovery;
- tenant/store membership and role changes;
- cross-tenant resource access;
- inventory movements and reconciliation;
- discounts, cancellations, returns and exchanges;
- cash sessions and closing differences;
- commission rules and settlement;
- payment/TEF/PSP adapters;
- fiscal adapters and document issuance;
- marketplace credentials and webhook handling;
- product/media uploads and AI processing;
- bulk import/export;
- billing and entitlements;
- database migrations and backup/restore.

## Security verification baseline
As implementation appears, CI should progressively cover lint/type checks, dependency auditing, secret scanning, SAST/CodeQL, test suites, migration verification and supply-chain checks. High/Critical real findings are addressed early; scanner output is triaged rather than blindly accepted.

See `docs/security/security-architecture.md` for the threat model and controls, `docs/data/data-lifecycle.md` for lifecycle/privacy concerns and `docs/devops/devsecops.md` for pipeline controls.
