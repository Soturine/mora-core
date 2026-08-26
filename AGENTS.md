# AGENTS.md

This file is the repository map for Codex, Copilot, Claude and other coding agents. It is intentionally concise. Detailed rules live in `docs/`.

## Mission
Build Mora Core as a professional, secure, multi-tenant retail operating system. The current repository is documentation-first; never claim a planned capability is implemented.

## Non-negotiables
- Business rules first; frontend validation is UX, not authority.
- Prefer modular monolith and clear ports/adapters over premature microservices.
- PostgreSQL constraints/transactions protect important invariants.
- Inventory is an append-only movement ledger plus derived balance; never arbitrary quantity CRUD.
- AuthN != AuthZ != object-level authorization != tenant isolation.
- Never trust `organizationId`/`storeId` supplied by a client without server-side membership checks.
- No secrets in Git, browser, mobile bundle, logs, fixtures, screenshots or docs.
- AI output is untrusted. AI may propose/transform; deterministic code authorizes, calculates, persists and enforces invariants.
- Original product media must be retained when AI-derived media is created; provenance and human approval are required for commercial AI assets.
- External integrations must be behind adapters and use timeouts, schema validation, bounded payloads, idempotency and retry/backoff where appropriate.
- No fake checkout, fake stock, fake prices, fake fiscal behavior or fake marketplace integrations.
- New dependencies require a real justification.
- Keep current docs truthful; history belongs in changelog/release notes.

## Canonical docs
- Product vision: `docs/product/vision.md`
- Modules: `docs/product/modules.md`
- Architecture: `docs/architecture/system-architecture.md`
- Domain model: `docs/domain/domain-model.md`
- Multi-tenancy: `docs/saas/multitenancy.md`
- Security: `docs/security/security-architecture.md`
- QA: `docs/qa/test-strategy.md`
- DevSecOps: `docs/devops/devsecops.md`
- Roadmap: `docs/roadmap/roadmap.md`
- Engineering Constitution: `docs/engineering/constitution.md`
- ADRs: `docs/adr/`

## Expected workflow
1. Explore the relevant module and read the closest instructions/docs.
2. State objective, acceptance criteria, invariants, risks and explicit out-of-scope.
3. Plan a small coherent change.
4. Implement with focused tests.
5. Run relevant lint/type/test/security checks.
6. Commit logically and push the real remote milestone.
7. Continue independent work while remote CI runs; check remote gates between milestones.
8. Before a release candidate, run the justified full qualification once.
9. Require the exact remote SHA to be green before tag/release.
10. Report `implemented`, `partial`, `experimental`, `deferred` or `not validated` honestly.

## Testing rule
Ask: “If the feature were fundamentally broken, would this test still pass?” If yes, strengthen the test. Mock external boundaries, not the behavior being proved.

## High-risk paths
Changes involving identity, authorization, tenant isolation, inventory movements, sales, cash, commissions, fiscal, payments, migrations, secrets, marketplace webhooks, AI publication or release workflows require stronger review and integration tests.

## Anti-overengineering
Do not introduce Kafka, Kubernetes, event sourcing, CQRS, Redis, GraphQL, distributed tracing or microservices merely to look sophisticated. Add complexity only when a measured operational/product requirement buys a property the system needs.
