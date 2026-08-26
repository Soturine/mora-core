# Engineering Constitution

Mora Core follows a permanent professional engineering standard. The guiding principle is: **do not optimize to look sophisticated; optimize to withstand a serious audit.**

## Business rules and architecture
- Identify actors, goals, states, transitions, invariants, permissions, errors, edge cases, concurrency, lifecycle and acceptance criteria before implementation.
- Keep critical rules centralized in the domain/application layer; frontend validation is UX, not authority.
- Prefer the simplest architecture with strong boundaries. The default is a modular monolith with high cohesion, low coupling, explicit contracts, dependency inversion and ports/adapters.
- Avoid giant entry files, controllers, components, circular dependencies and premature distributed architecture.
- Record significant decisions in ADRs.

## Code quality
- Use clear names, explicit types/contracts, deliberate error handling and comments that explain why.
- Avoid broad silent catches, indiscriminate dynamic typing, magic values, placeholders, dead code, duplicate business rules and abstractions without a demonstrated benefit.
- AI-generated code is an untrusted draft until reviewed and tested. Test count, coverage, line count and commit count are not proof of quality.

## Data, concurrency and lifecycle
- Define identity, ownership, state, relationships, uniqueness, retention, deletion, audit, concurrency, version and provenance for important entities.
- Use database constraints and transactions to protect important invariants. Migrations are schema authority.
- Consider simultaneous operations and apply appropriate transaction, uniqueness, locking, versioning and idempotency mechanisms.
- Classify data as authoritative persistent, persistent derived, regenerable cache or ephemeral. Deletion must account for database records, files, derived media, indexes, analytics and backups according to policy.

## Security and privacy
- Security is designed in, not added at the end.
- Authentication, authorization, object authorization and tenant isolation are separate concerns.
- Apply least privilege, fail-closed behavior for critical decisions, input validation, output safety, secure session handling, resource limits, upload controls and audit where appropriate.
- Secrets must never be committed, shipped to public clients, logged or placed in documentation/fixtures. Agents receive only the capabilities required for the task.

## APIs and contracts
- Prefer pragmatic resource-oriented HTTP semantics when suitable: correct methods/status codes, pagination, filtering, sorting, limits, authorization, concurrency controls, cache semantics and consistent errors.
- OpenAPI and other schemas should be real contracts rather than decorative documentation.
- Do not model APIs as a direct mirror of database tables.

## Testing and QA
- Tests must prove behavior. Use unit/component tests heavily, real integration tests at important boundaries and a small number of end-to-end critical journeys.
- Add contract, database, property, fuzz, mutation, security, migration, recovery and performance tests according to risk.
- Mock external dependencies, not the behavior being proved.
- Smoke tests must verify a meaningful effect/state, not only process startup.
- Flaky tests are defects; prefer deterministic clocks, isolated fixtures and state-based waits.
- Important milestones require verification, validation and independent audit.

## Frontend and accessibility
- UI should reflect the domain and distinguish server, UI, form, URL and persistent-preference state.
- Avoid giant pages/components.
- Accessibility is part of Definition of Done: semantic HTML, keyboard/focus behavior, labels, contrast, screen readers, browser zoom, reduced motion and adequate touch targets.

## AI systems
- AI may propose, classify, summarize, structure and generate content; deterministic code remains responsible for calculations, authorization, invariants and persistence.
- AI/tool/external outputs are untrusted and should use structured contracts, limits, provenance and human checkpoints when commercial or high-impact decisions are involved.
- AI evaluation should include golden and adversarial cases, grounding/source support, format adherence and human review.

## Git, CI and releases
- Work in small coherent batches: focused validation, logical commit and real remote push.
- A clean worktree is not proof that GitHub is synchronized.
- Continue independent tasks while remote CI runs, then checkpoint remote failures between milestones.
- The exact final SHA must be remote and green before tagging a release.
- The artifact released should be the artifact actually validated; use hashes, SBOM and provenance when appropriate.

## Dependencies and supply chain
- New dependencies require justification for necessity, license, maintenance, security, compatibility and runtime/build cost.
- Keep lockfiles and least-privilege CI permissions. Apply static analysis, dependency/security scanning, secret scanning and provenance controls proportionally.

## Documentation and delivery
- README is the current entry point, not a changelog.
- Current docs describe current truth. Release notes preserve history. ADRs preserve decisions. Runbooks preserve operational procedures.
- Use lightweight Kanban with low WIP. Definition of Ready includes objective, business rule, acceptance criteria, risks and explicit out-of-scope. Definition of Done includes correct/negative behavior, security, tests, docs/contracts/migrations, remote push and required green gates.

## Observability, resilience and performance
- Baseline observability includes structured logs, actionable errors and correlation IDs; servers add meaningful readiness/liveness and metrics as needed.
- Add timeouts, retries/backoff, queues, circuit controls, outbox/inbox, feature flags or other resilience patterns only when a real failure mode justifies them.
- Measure performance before optimizing: inspect algorithmic complexity, query plans, repeated work, network calls, allocations, memory and I/O.

## Agent workflow
Use this constitution together with `AGENTS.md`, module docs, ADRs, small task specs and executable checks. Long sessions should keep a concise handoff/build ledger. Single-agent work is the default; parallel agents/worktrees are reserved for truly independent tasks.

## Honest status vocabulary
Use `implemented`, `partial`, `experimental`, `planned`, `deferred` and `not validated`. Never claim completion without evidence.
