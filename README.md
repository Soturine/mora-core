# Mora Core

> **Retail operating system for physical + digital commerce.**

Mora Core is a **proprietary, SaaS-ready retail platform** being designed to unify product catalog, variants/SKUs, inventory, purchasing, sales, POS, cash sessions, employee attribution, commissions, storefronts, mobile operations, AI-assisted product content, analytics, integrations and omnichannel commerce.

The first real design partner is the Mora retail operation, with two physical fashion stores. The architecture, however, is intentionally designed so the product can evolve into a secure multi-tenant SaaS for independent retailers, brands and multi-store organizations.

> [!IMPORTANT]
> **Current status:** this repository is documentation-first. The documents describe the intended product, architecture, constraints and engineering standards. They do **not** imply that all described features are implemented. Every future milestone must distinguish `implemented`, `partial`, `experimental`, `planned`, `deferred` and `not validated`.

## Product thesis

Mora Core should reduce duplicate work between the physical store, backoffice and digital channels:

```text
Receive merchandise
        ↓
Identify / create SKU
        ↓
Photograph / enrich product
        ↓
Catalog + variants
        ↓
Inventory
        ↓
Publish
   ┌────┼───────────────┬──────────────┐
   │    │               │              │
 POS  Storefront   Marketplaces    Social commerce
   │    │               │              │
   └────┴───────────────┴──────────────┘
        ↓
Orders / Sales
        ↓
Inventory movements
        ↓
Cash / commissions / analytics
```

The long-term value proposition is not merely “ERP + POS”. It is a retail operating system that can take a merchant from **merchandise intake to physical and digital sale without re-entering the same product data across multiple systems**.

## Core capabilities

### ERP / retail backoffice
- Organizations, legal entities, brands, stores and stock locations.
- Employees, users, roles and store-scoped permissions.
- Product catalog, hierarchical categories, attributes, variants, SKUs and barcodes.
- Inventory ledger, balances, counts, transfers, adjustments and receiving.
- Purchasing and supplier flows.
- Sales, payments, returns, exchanges and cancellations.
- Cash registers, opening, sangria/withdrawal, supply, reconciliation and daily closing.
- Seller attribution and auditable commission calculation.
- Customers and future CRM/loyalty capabilities.
- Operational and management reports.

### Mobile operations
- Product registration from the phone.
- Camera-based barcode scanning.
- Internal SKU/barcode generation for products without manufacturer codes.
- Product photography and media upload.
- Inventory counting and stock lookup.
- Transfers and receiving.
- Seller/manager dashboards.
- Offline-capable operational workflows where justified.

### AI-assisted commerce
- Product photo enhancement while preserving the original asset.
- Background cleanup and marketplace-ready derivatives.
- Optional AI-generated model imagery from real garments, with provenance and human approval.
- Description/title/tag/category suggestions based only on verified product facts.
- Channel-specific content drafts.
- AI outputs are **untrusted proposals**, never the authority for price, stock, tax, permissions or deterministic rules.

### Omnichannel / PIM / OMS
- Canonical product information management.
- Channel listings and per-channel overrides.
- First-party storefronts.
- Planned adapters for channels such as TikTok Shop, Mercado Livre and Shopee.
- Order ingestion, inventory reservation and stock synchronization.
- Marketplace/channel compliance validation before publication.
- Idempotent webhook/integration processing.

### Storefront platform
- A customer without a website should be able to create one from Mora Core.
- Brand themes, navigation, homepage sections, catalog and product pages.
- Dynamic categories and channel publication rules.
- Custom domains in future phases.
- A single product source of truth feeds the site automatically.

### SaaS platform
- Multi-tenant organization model from day one.
- Multiple stores, brands, legal entities and websites per organization.
- Tenant-safe authorization boundaries.
- Entitlements and future subscription billing separated from retail payments.
- Import/export and migration support to reduce lock-in.
- Secure cloud operation, backups, recovery and observability.

## Architectural direction

The default architecture is a **modular monolith** with strong module boundaries, not premature microservices.

```text
                         MORA CORE
                            │
                    Identity / Tenancy
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
     ERP Core            Commerce           Experience
        │                   │                   │
 Catalog/Stock         PIM / OMS          Admin / Mobile
 Sales/Cash            Channels           POS / Storefront
 Commissions           Integrations       Analytics
        └───────────────────┼───────────────────┘
                            │
                      Application Core
                            │
          ┌─────────────────┼──────────────────┐
          │                 │                  │
      PostgreSQL       Object Storage        Jobs
          │                 │                  │
          └─────────────────┼──────────────────┘
                            │
                        External APIs
```

Key architectural rules:
- Business rules are centralized in the domain/application layer.
- PostgreSQL protects important invariants with real constraints and transactions.
- Inventory is represented by **append-only movements + derived/materialized balance**, not arbitrary quantity edits.
- Browser/mobile clients never become the authority for stock, price, commissions, tenant identity or permissions.
- External providers are isolated behind adapters.
- Secrets never live in the frontend, APK, repository or logs.
- APIs use pragmatic HTTP semantics and bounded resource usage.
- Complexity such as Kafka, Kubernetes, CQRS or microservices is introduced only when a proven requirement justifies it.

## Repository map

This repository currently focuses on the architecture and product specification. The intended implementation layout is documented, not yet guaranteed to exist in code.

```text
mora-core/
├── README.md
├── LICENSE
├── AGENTS.md
├── SECURITY.md
├── CONTRIBUTING.md
├── docs/
│   ├── index.md
│   ├── product/
│   ├── architecture/
│   ├── domain/
│   ├── saas/
│   ├── mobile/
│   ├── ai/
│   ├── commerce/
│   ├── integrations/
│   ├── security/
│   ├── qa/
│   ├── devops/
│   ├── operations/
│   ├── data/
│   ├── ux/
│   ├── roadmap/
│   ├── research/
│   └── adr/
└── ... implementation will be introduced incrementally
```

## Documentation

Start with the [documentation index](docs/index.md).

### Product & scope
- [Vision and product principles](docs/product/vision.md)
- [Functional scope and modules](docs/product/modules.md)
- [Personas, roles and critical journeys](docs/product/personas-and-journeys.md)
- [Storefront and website builder](docs/product/storefront.md)
- [Roadmap and delivery phases](docs/roadmap/roadmap.md)

### Architecture & data
- [System architecture](docs/architecture/system-architecture.md)
- [Modular monolith and module boundaries](docs/architecture/modular-monolith.md)
- [Multi-tenancy architecture](docs/saas/multitenancy.md)
- [Domain model](docs/domain/domain-model.md)
- [Inventory model](docs/domain/inventory.md)
- [Sales, cash and commissions](docs/domain/sales-cash-commissions.md)
- [API and integration contracts](docs/architecture/api-contracts.md)
- [Data lifecycle, backup and recovery](docs/data/data-lifecycle.md)

### Mobile, AI and commerce
- [Mobile app](docs/mobile/mobile-app.md)
- [Barcode, labels and inventory scanning](docs/mobile/barcodes-and-scanning.md)
- [AI architecture and governance](docs/ai/ai-governance.md)
- [AI media pipeline](docs/ai/media-pipeline.md)
- [Omnichannel, PIM and OMS](docs/commerce/omnichannel.md)
- [Marketplace/channel integration strategy](docs/integrations/marketplaces.md)

### Engineering, QA and operations
- [Engineering Constitution](docs/engineering/constitution.md)
- [Security architecture and threat model](docs/security/security-architecture.md)
- [QA and test strategy](docs/qa/test-strategy.md)
- [DevOps / DevSecOps / release engineering](docs/devops/devsecops.md)
- [SRE, observability and AIOps](docs/operations/sre-aiops.md)
- [Performance and capacity](docs/operations/performance-capacity.md)
- [Incident, backup and disaster recovery](docs/operations/incident-recovery.md)

### Research and decisions
- [Competitive/benchmark analysis](docs/research/competitive-benchmark.md)
- [Risks, assumptions and open questions](docs/roadmap/risks-and-open-questions.md)
- [ADR index](docs/adr/README.md)

## Engineering standard

Mora Core follows a strict engineering principle:

> **Do not optimize to look sophisticated. Optimize to withstand a serious audit.**

The project follows the permanent Engineering Constitution in [docs/engineering/constitution.md](docs/engineering/constitution.md). Among other things, it requires:
- business rules first;
- secure-by-design and secure-by-default;
- explicit tenant isolation;
- meaningful tests instead of coverage theater;
- deterministic cores for financial/stock/security rules;
- AI output treated as untrusted;
- small logical changes and real remote pushes;
- exact-SHA release qualification;
- dependency and supply-chain governance;
- current documentation as the source of truth;
- independent audit for important milestones.

## Current implementation strategy

The preferred sequence is intentionally incremental:

1. **Foundation:** identity, organizations, stores, permissions, catalog, variants, inventory ledger and audit.
2. **Mobile operations:** lookup, scanner, product intake, photos, labels and inventory count.
3. **Storefront integration:** sites consume Mora Core catalog and stock projections.
4. **Sales/POS:** sales, payments, cash sessions, returns and commissions.
5. **Analytics:** sell-through, stock aging, ABC analysis, seller performance and channel metrics.
6. **AI:** assisted media/content flows with provenance, budgets and human review.
7. **Omnichannel:** channel adapters, order ingestion and stock synchronization.
8. **SaaS hardening:** onboarding, billing/entitlements, tenant operations, self-service import/export and support tooling.

See the complete [roadmap](docs/roadmap/roadmap.md).

## Status vocabulary

Every issue, milestone and release should use honest status labels:

| Status | Meaning |
| --- | --- |
| `implemented` | Exists and has evidence of correct behavior. |
| `partial` | Some acceptance criteria are not complete. |
| `experimental` | Exists but is not yet a supported/stable capability. |
| `planned` | Documented intent; not implemented. |
| `deferred` | Intentionally postponed. |
| `not validated` | May exist, but evidence is insufficient. |

## Proprietary license

Mora Core is proprietary software. See [LICENSE](LICENSE).

**© 2026 Mora Core. All rights reserved.**
