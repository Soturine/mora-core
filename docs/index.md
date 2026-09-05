# Documentação do Mora Core

Este índice é a entrada canônica para a documentação técnica e de produto.

> **Status geral:** planejamento/arquitetura. Não confunda documentação com implementação concluída.

## Comece aqui
- [Visão Executiva](product/executive-overview.md)
- [Cenário completo das lojas Mora no estado futuro](product/mora-reference-future-state.md)
- [Fluxos técnicos end-to-end](architecture/end-to-end-flows.md)
- [Discovery Operacional](discovery/operational-discovery.md)
- [Baseline real de PDV e hardware das lojas Mora — 05/09/2026](discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [Discovery de WhatsApp, demanda e sourcing](discovery/conversational-commerce-discovery.md)
- [Roadmap principal](roadmap/roadmap.md)
- [Matriz de rastreabilidade](roadmap/requirements-traceability.md)
- [Auditoria documental de 26/08/2026](audit/repository-documentation-audit-2026-08-26.md)

## Produto
- [Visão](product/vision.md)
- [Visão Executiva](product/executive-overview.md)
- [Cenário de referência — lojas Mora no estado futuro](product/mora-reference-future-state.md)
- [Escopo e não objetivos](product/scope-and-non-goals.md)
- [Módulos e capacidades](product/modules.md)
- [Personas e jornadas](product/personas-and-journeys.md)
- [Storefront / criação de sites](product/storefront.md)
- [Analytics e relatórios](product/analytics-and-reporting.md)

## Arquitetura e SaaS
- [Arquitetura do sistema](architecture/system-architecture.md)
- [Fluxos técnicos end-to-end](architecture/end-to-end-flows.md)
- [Monólito modular](architecture/modular-monolith.md)
- [Contratos de API](architecture/api-contracts.md)
- [Integração do POS com periféricos locais](architecture/pos-device-integration.md)
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
- [Operação de conversas — inbox, handoff e roteamento](commerce/conversation-operations.md)
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
- [Plano de testes do POS em hardware real](qa/pos-hardware-test-plan.md)
- [DevOps / DevSecOps](devops/devsecops.md)
- [SRE e AIOps](operations/sre-aiops.md)
- [Performance e capacidade](operations/performance-capacity.md)
- [Incidentes e recuperação](operations/incident-recovery.md)
- [Catálogo de runbooks](operations/runbook-catalog.md)
- [Data lifecycle](data/data-lifecycle.md)
- [Import/export/migração](data/import-export-migration.md)
- [Migração dos legados Mora](data/mora-legacy-data-migration.md)
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
- [Auditoria documental](audit/repository-documentation-audit-2026-08-26.md)
- [ADRs](adr/README.md)

## Discovery e apresentação
- [Discovery operacional das lojas](discovery/operational-discovery.md)
- [Baseline real de PDV e hardware](discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [Discovery de WhatsApp, demanda e sourcing](discovery/conversational-commerce-discovery.md)
- [Visão executiva para negócio/família](product/executive-overview.md)
- [Cenário completo das lojas Mora](product/mora-reference-future-state.md)
- [Apresentação do projeto e portfólio](project/project-presentation-and-portfolio.md)
- [Marca, naming e titularidade](project/branding-and-ownership.md)

## Como interpretar esta documentação

- `README.md` = visão e mapa do projeto.
- `/docs` = verdade atual aprofundada.
- `discovery/mora-pos-hardware-baseline-2026-09-05.md` = evidência de campo sobre PDVs, PCs e periféricos atuais; não é contrato futuro do Mora POS.
- `architecture/pos-device-integration.md` = arquitetura planejada para integrar periféricos sem acoplar domínio a fabricantes.
- `data/mora-legacy-data-migration.md` = estratégia específica para migrar/reconciliar NOOVA, Lips, Bling e outras fontes atuais.
- `qa/pos-hardware-test-plan.md` = evidência exigida antes de afirmar compatibilidade do POS com o hardware real.
- `product/mora-reference-future-state.md` = exemplo de como todas as capacidades planejadas se conectam numa operação completa das lojas Mora.
- `architecture/end-to-end-flows.md` = visão técnica dos mesmos fluxos atravessando módulos.
- `audit/*` = auditorias datadas do estado documental/arquitetural.
- ADR = decisão arquitetural e trade-offs.
- Runbook = operação, incidente e recuperação.
- Changelog/release notes = história.
- Matriz de rastreabilidade = prova de cobertura temática das decisões/requisitos discutidos.
- Documentos `research/*` = fotografia datada de mercado/política; revalidar antes de implementar.

Status permitidos: `implementado`, `parcial`, `experimental`, `planejado`, `adiado`, `não validado`.

Documentos relacionados devem possuir links entre si e ser atualizados junto com comportamento e decisões. Nenhum documento de visão pode ser usado como prova de que uma feature já existe.
