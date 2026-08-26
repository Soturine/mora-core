# Contributing to Mora Core

Mora Core is proprietary. Contributions are accepted only under project-owner authorization and do not grant a license to redistribute the project.

## Before coding
A task should have a clear objective, business rule, acceptance criteria, risks, affected modules and explicit out-of-scope. Read `AGENTS.md`, the closest module documentation and relevant ADRs.

## Branching and commits
Prefer small coherent changes. Direct-main may be used when explicitly allowed by the project owner; otherwise use a short-lived branch/PR. A local commit is not a delivery milestone until the remote repository contains the exact SHA.

Commit messages should describe the intent rather than file count, for example:
- `feat(inventory): add transfer receipt workflow`
- `fix(authz): scope product lookup by organization`
- `docs(architecture): record storefront boundary`

Avoid giant mixed commits.

## Definition of Done
A change is not done only because it compiles. Depending on risk, DoD includes:
- business rule and negative paths behave correctly;
- authorization/tenant boundaries are enforced server-side;
- relevant unit/integration/contract/E2E tests exist and fail for the right reasons;
- migrations and contracts are updated safely;
- docs represent current behavior;
- no relevant dead code, debug logs or leaked secrets;
- lint/type/tests/security gates pass;
- remote push is confirmed;
- required CI gates are green for the exact SHA.

## Reviews
Review architecture, correctness, security, data integrity, tests, accessibility, performance and claims. Important milestones require independent review rather than relying only on the implementing agent.

## Dependencies
Do not add a package because it saves a few lines. Check necessity, maintenance, license, security, compatibility, bundle/runtime cost and whether the current stack already solves the problem.

## Documentation
README is the current entry point. `docs/` is current truth. Release history belongs in release notes/changelog. Important architectural decisions belong in ADRs. Operational recovery belongs in runbooks.

## AI-assisted development
AI-generated code is an untrusted draft. Agents must follow `AGENTS.md`, use bounded capabilities and provide evidence. Do not accept implementation reports as proof without tests/runtime inspection.
