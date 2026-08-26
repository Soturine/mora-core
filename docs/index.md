# Documentação do Mora Core

Este índice é a entrada canônica para a documentação técnica e de produto.

> **Status geral:** planejamento/arquitetura. Não confunda documentação com implementação concluída.

## Comece aqui
- [Visão Executiva](product/executive-overview.md)
- [Discovery Operacional](discovery/operational-discovery.md)
- [Roadmap principal](roadmap/roadmap.md)
- [Matriz de rastreabilidade](roadmap/requirements-traceability.md)

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
- [Identidade, funcionários e permissões](domain/identity-employees-permissions.md)
- [Estoque](domain/inventory.md)
- [Compras, fornecedores e recebimento](domain/purchasing-and-suppliers.md)
- [Vendas, caixa e comissões](domain/sales-cash-commissions.md)
- [Fiscal e pagamentos](domain/fiscal-and-payments.md)

## Catálogo, commerce e canais
- [Taxonomia do catálogo](commerce/catalog-taxonomy.md)
- [PIM, OMS e omnichannel](commerce/omnichannel.md)
- [WhatsApp Commerce Agent — vendas conversacionais com IA](commerce/whatsapp-commerce-agent.md)
- [Demanda, encomendas e sourcing](commerce/customer-demand-and-sourcing.md)
- [Marketplaces](integrations/marketplaces.md)
- [Bling](integrations/bling.md)
- [Integração fiscal no Brasil](integrations/fiscal-brazil.md)
- [Confiabilidade de integrações](integrations/reliability-patterns.md)

## Mobile e IA
- [Aplicativo mobile](mobile/mobile-app.md)
- [Barcode, etiquetas e scanner](mobile/barcodes-and-scanning.md)
- [Governança de IA](ai/ai-governance.md)
- [Pipeline de mídia e IA](ai/media-pipeline.md)

## Segurança, QA e engenharia
- [Engineering Constitution](engineering/constitution.md)
- [Segurança e threat model](security/security-architecture.md)
- [Identidade, RBAC e auditoria — visão de segurança](security/identity-rbac-audit.md)
- [Privacidade e LGPD](security/privacy-lgpd.md)
- [QA e testes](qa/test-strategy.md)
- [DevOps / DevSecOps](devops/devsecops.md)
- [SRE e AIOps](operations/sre-aiops.md)
- [Performance e capacidade](operations/performance-capacity.md)
- [Incidentes e recuperação](operations/incident-recovery.md)
- [Catálogo de runbooks](operations/runbook-catalog.md)
- [Data lifecycle](data/data-lifecycle.md)
- [Import/export/migração](data/import-export-migration.md)
- [Acessibilidade e UX](ux/accessibility-and-design.md)

## Planejamento, rastreabilidade e pesquisa
- [Roadmap](roadmap/roadmap.md)
- [Roadmap de commerce conversacional](roadmap/conversational-commerce-roadmap.md)
- [Riscos e questões em aberto](roadmap/risks-and-open-questions.md)
- [Cobertura e incompletudes documentais](roadmap/documentation-coverage.md)
- [Matriz de rastreabilidade dos requisitos discutidos](roadmap/requirements-traceability.md)
- [Benchmark competitivo — ERP/PDV](research/competitive-benchmark.md)
- [Benchmark — commerce conversacional](research/conversational-commerce-benchmark.md)
- [Landscape de mercado](research/market-landscape.md)
- [Snapshot de políticas dos canais](research/channel-policy-snapshot-2026-08.md)
- [ADRs](adr/README.md)

## Discovery e apresentação
- [Discovery operacional das lojas](discovery/operational-discovery.md)
- [Visão executiva para negócio/família](product/executive-overview.md)
- [Apresentação do projeto e portfólio](project/project-presentation-and-portfolio.md)
- [Marca, naming e titularidade](project/branding-and-ownership.md)

## Como interpretar esta documentação

- `README.md` = visão e mapa do projeto.
- `/docs` = verdade atual aprofundada.
- ADR = decisão arquitetural e trade-offs.
- Runbook = operação, incidente e recuperação.
- Changelog/release notes = história.
- Matriz de rastreabilidade = prova de cobertura temática das decisões/requisitos discutidos.
- Documentos `research/*` = fotografia datada de mercado/política; revalidar antes de implementar.

Status permitidos: `implementado`, `parcial`, `experimental`, `planejado`, `adiado`, `não validado`.

Documentos relacionados devem possuir links entre si e ser atualizados junto com comportamento e decisões. Nenhum documento de visão pode ser usado como prova de que uma feature já existe.
