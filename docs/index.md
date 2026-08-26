# Documentação do Mora Core

Este índice é a entrada canônica para a documentação técnica e de produto.

> **Status geral:** planejamento/arquitetura. Não confunda documentação com implementação concluída.

## Produto
- [Visão](product/vision.md)
- [Escopo e não objetivos](product/scope-and-non-goals.md)
- [Módulos e capacidades](product/modules.md)
- [Personas e jornadas](product/personas-and-journeys.md)
- [Storefront / criação de sites](product/storefront.md)
- [Analytics e relatórios](product/analytics-and-reporting.md)

## Arquitetura e SaaS
- [Arquitetura do sistema](architecture/system-architecture.md)
- [Monólito modular](architecture/modular-monolith.md)
- [Contratos de API](architecture/api-contracts.md)
- [Multi-tenancy](saas/multitenancy.md)
- [Plataforma e onboarding](saas/platform-and-onboarding.md)
- [Billing e entitlements](saas/billing-entitlements.md)

## Domínio
- [Modelo de domínio](domain/domain-model.md)
- [Estoque](domain/inventory.md)
- [Vendas, caixa e comissões](domain/sales-cash-commissions.md)
- [Fiscal e pagamentos](domain/fiscal-and-payments.md)

## Catálogo, commerce e canais
- [Taxonomia do catálogo](commerce/catalog-taxonomy.md)
- [PIM, OMS e omnichannel](commerce/omnichannel.md)
- [Marketplaces](integrations/marketplaces.md)
- [Bling](integrations/bling.md)
- [Confiabilidade de integrações](integrations/reliability-patterns.md)

## Mobile e IA
- [Aplicativo mobile](mobile/mobile-app.md)
- [Barcode, etiquetas e scanner](mobile/barcodes-and-scanning.md)
- [Governança de IA](ai/ai-governance.md)
- [Pipeline de mídia](ai/media-pipeline.md)

## Segurança, QA e engenharia
- [Engineering Constitution](engineering/constitution.md)
- [Segurança e threat model](security/security-architecture.md)
- [Identidade, RBAC e auditoria](security/identity-rbac-audit.md)
- [QA e testes](qa/test-strategy.md)
- [DevOps / DevSecOps](devops/devsecops.md)
- [SRE e AIOps](operations/sre-aiops.md)
- [Performance e capacidade](operations/performance-capacity.md)
- [Incidentes e recuperação](operations/incident-recovery.md)
- [Catálogo de runbooks](operations/runbook-catalog.md)
- [Data lifecycle](data/data-lifecycle.md)
- [Import/export/migração](data/import-export-migration.md)
- [Acessibilidade e UX](ux/accessibility-and-design.md)

## Planejamento e pesquisa
- [Roadmap](roadmap/roadmap.md)
- [Riscos e questões em aberto](roadmap/risks-and-open-questions.md)
- [Cobertura e incompletudes documentais](roadmap/documentation-coverage.md)
- [Benchmark competitivo](research/competitive-benchmark.md)
- [Landscape de mercado](research/market-landscape.md)
- [ADRs](adr/README.md)

## Convenções

README = visão/marcador de entrada. `/docs` = verdade atual aprofundada. ADR = decisão. Runbook = operação. Changelog/release notes = história. Status: `implementado`, `parcial`, `experimental`, `planejado`, `adiado`, `não validado`.

Documentos relacionados devem possuir links entre si e ser atualizados junto com comportamento/decisões.
