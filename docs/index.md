# Documentation Index

This directory is the canonical knowledge base for Mora Core. Documents describe intended product behavior, architecture, controls and delivery strategy. Until code exists and is validated, design statements are **planned**, not implementation claims.

## Product
- [Vision](product/vision.md)
- [Modules](product/modules.md)
- [Personas and critical journeys](product/personas-and-journeys.md)
- [Storefront / website builder](product/storefront.md)

## Architecture
- [System architecture](architecture/system-architecture.md)
- [Modular monolith](architecture/modular-monolith.md)
- [API contracts](architecture/api-contracts.md)
- [Domain model](domain/domain-model.md)
- [Inventory](domain/inventory.md)
- [Sales, cash and commissions](domain/sales-cash-commissions.md)
- [Fiscal and payments](domain/fiscal-and-payments.md)

## SaaS
- [Multi-tenancy](saas/multitenancy.md)
- [Onboarding, billing and entitlements](saas/onboarding-billing-entitlements.md)

## Mobile
- [Mobile app](mobile/mobile-app.md)
- [Barcodes and scanning](mobile/barcodes-and-scanning.md)

## AI
- [AI governance](ai/ai-governance.md)
- [Media pipeline](ai/media-pipeline.md)

## Commerce and integrations
- [Catalog taxonomy](commerce/catalog-taxonomy.md)
- [Omnichannel / PIM / OMS](commerce/omnichannel.md)
- [Marketplace strategy](integrations/marketplaces.md)
- [Bling integration strategy](integrations/bling.md)

## Security, privacy and data
- [Security architecture](security/security-architecture.md)
- [Data lifecycle](data/data-lifecycle.md)
- [Privacy and LGPD](data/privacy-lgpd.md)

## Quality and operations
- [Engineering Constitution](engineering/constitution.md)
- [QA/test strategy](qa/test-strategy.md)
- [DevOps / DevSecOps](devops/devsecops.md)
- [SRE / observability / AIOps](operations/sre-aiops.md)
- [Performance and capacity](operations/performance-capacity.md)
- [Incident and recovery](operations/incident-recovery.md)
- [Accessibility and UX](ux/accessibility-and-design.md)

## Planning and research
- [Roadmap](roadmap/roadmap.md)
- [Risks and open questions](roadmap/risks-and-open-questions.md)
- [Competitive benchmark](research/competitive-benchmark.md)
- [ADR index](adr/README.md)

## Documentation rules
1. Current docs describe current truth; do not use them as a release diary.
2. Planned behavior must be labeled as planned.
3. Significant architecture changes require an ADR or an explicit update to an existing decision.
4. Cross-link related documents instead of duplicating rules.
5. Commands/examples should be executable or clearly illustrative.
6. When implementation contradicts documentation, treat it as a defect and reconcile deliberately.
