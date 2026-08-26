# Matriz de Rastreabilidade dos Requisitos Discutidos

> **Objetivo:** provar que as decisões e ideias discutidas na concepção do Mora Core foram transformadas em documentação canônica, sem depender da memória de uma conversa. Esta matriz registra cobertura temática; os documentos aprofundados são a fonte de verdade atual.

## 1. Funcionários, vendas, comissão e caixa

| Assunto discutido | Documento canônico |
| --- | --- |
| login individual por funcionária | [Identidade e permissões](../domain/identity-employees-permissions.md) |
| `User` separado de `Employee` | [Identidade e permissões](../domain/identity-employees-permissions.md) |
| papéis Owner/Manager/Cashier/Seller/Stock/Accountant | [Identidade e permissões](../domain/identity-employees-permissions.md) |
| escopo por loja | [Identidade](../domain/identity-employees-permissions.md) + [Multi-tenancy](../saas/multitenancy.md) |
| seller diferente de cashier | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| venda atribuída a vendedor | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| possível atribuição por item no futuro | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| plano de comissão configurável | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| comissão provisionada/aprovada/revertida/fechada | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| devolução/cancelamento afetando comissão | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| fechamento mensal auditável | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| abertura/fechamento de caixa | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| dinheiro vs Pix/cartão | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| fechamento cego | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| sangria/suprimento | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |
| desconto/cancelamento com aprovação | [Identidade](../domain/identity-employees-permissions.md) + [Vendas](../domain/sales-cash-commissions.md) |
| dashboard de funcionária e proprietários | [Analytics](../product/analytics-and-reporting.md) + [Mobile](../mobile/mobile-app.md) |
| audit trail | [Identidade](../domain/identity-employees-permissions.md) + [Segurança](../security/security-architecture.md) |
| cuidado trabalhista/contábil | [Vendas, caixa e comissões](../domain/sales-cash-commissions.md) |

## 2. Aplicativo mobile, scanner e estoque

| Assunto | Documento |
| --- | --- |
| app como ferramenta operacional, não mini-admin | [Mobile](../mobile/mobile-app.md) |
| cadastro de produto pelo celular | [Mobile](../mobile/mobile-app.md) |
| câmera como scanner | [Barcodes](../mobile/barcodes-and-scanning.md) |
| produto com código existente | [Barcodes](../mobile/barcodes-and-scanning.md) |
| produto sem código | [Barcodes](../mobile/barcodes-and-scanning.md) |
| SKU/código interno | [Barcodes](../mobile/barcodes-and-scanning.md) |
| não inventar GTIN/EAN oficial | [Barcodes](../mobile/barcodes-and-scanning.md) |
| geração/impressão de etiqueta | [Barcodes](../mobile/barcodes-and-scanning.md) |
| inventário modo coletor | [Mobile](../mobile/mobile-app.md) + [Barcodes](../mobile/barcodes-and-scanning.md) |
| contagem não altera saldo silenciosamente | [Estoque](../domain/inventory.md) |
| transferências entre lojas | [Mobile](../mobile/mobile-app.md) + [Estoque](../domain/inventory.md) |
| estoque baixo/alertas | [Mobile](../mobile/mobile-app.md) + [Analytics](../product/analytics-and-reporting.md) |
| fechamento pelo celular | [Mobile](../mobile/mobile-app.md) |
| notificações úteis | [Mobile](../mobile/mobile-app.md) |
| offline SQLite + outbox | [Mobile](../mobile/mobile-app.md) |
| `operationId` idempotente | [Mobile](../mobile/mobile-app.md) |
| rascunho offline | [Mobile](../mobile/mobile-app.md) |
| upload assíncrono | [Mobile](../mobile/mobile-app.md) + [Mídia](../ai/media-pipeline.md) |
| QR interno | [Barcodes](../mobile/barcodes-and-scanning.md) |
| RFID futuro sem remodelar estoque | [Barcodes](../mobile/barcodes-and-scanning.md) |
| React Native + Expo como direção, não decisão final | [Mobile](../mobile/mobile-app.md) + [ADRs](../adr/README.md) |

## 3. IA aplicada a catálogo e fotos

| Assunto | Documento |
| --- | --- |
| foto original preservada | [Pipeline de mídia](../ai/media-pipeline.md) |
| correção de luz/crop/fundo | [Pipeline de mídia](../ai/media-pipeline.md) |
| derivados WebP/AVIF/thumbnail | [Pipeline de mídia](../ai/media-pipeline.md) |
| IA não alterar características reais | [Pipeline de mídia](../ai/media-pipeline.md) |
| modelo virtual usando a roupa | [Pipeline de mídia](../ai/media-pipeline.md) |
| proveniência de conteúdo IA | [Pipeline de mídia](../ai/media-pipeline.md) + [Governança de IA](../ai/ai-governance.md) |
| descrição gerada a partir de fatos | [Pipeline](../ai/media-pipeline.md) + [Governança](../ai/ai-governance.md) |
| classificação/tags assistidas | [Pipeline de mídia](../ai/media-pipeline.md) |
| revisão humana | [Governança de IA](../ai/ai-governance.md) |
| AI Gateway server-side | [Pipeline de mídia](../ai/media-pipeline.md) |
| quotas/custos de IA SaaS | [Pipeline](../ai/media-pipeline.md) + [Billing](../saas/billing-entitlements.md) |
| evals, grounding e hallucination | [Governança de IA](../ai/ai-governance.md) |
| Product Truth vs Generated Content | [Pipeline de mídia](../ai/media-pipeline.md) |

## 4. Sites, catálogo e experiência de produto

| Assunto | Documento |
| --- | --- |
| site criado pelo próprio SaaS | [Storefront](../product/storefront.md) |
| múltiplas marcas/sites por organização | [Storefront](../product/storefront.md) + [Multi-tenancy](../saas/multitenancy.md) |
| temas seguros e configuráveis | [Storefront](../product/storefront.md) |
| homepage montável por blocos | [Storefront](../product/storefront.md) |
| categorias dinâmicas | [Taxonomia](../commerce/catalog-taxonomy.md) |
| feminino, juvenil, infantil, bebê, enxoval, acessórios, higiene | [Taxonomia](../commerce/catalog-taxonomy.md) |
| página individual `/produto/$slug` | [Storefront](../product/storefront.md) |
| seleção de variantes válidas | [Storefront](../product/storefront.md) + [Modelo de domínio](../domain/domain-model.md) |
| zoom somente na página de produto | [Storefront](../product/storefront.md) |
| fotos por variante | [Storefront](../product/storefront.md) + [Mídia](../ai/media-pipeline.md) |
| `Novidades` por publicação/data apropriada | [Storefront](../product/storefront.md) + [Analytics](../product/analytics-and-reporting.md) |
| `Mais vendidos` por vendas, não estoque | [Analytics](../product/analytics-and-reporting.md) |
| destaques como curadoria separada | [Storefront](../product/storefront.md) |
| demo vs dado comercial verificado | [Storefront](../product/storefront.md) |
| SEO/Offer só com dados verdadeiros | [Storefront](../product/storefront.md) |
| site consumindo Catalog API futura | [Storefront](../product/storefront.md) + [Contratos de API](../architecture/api-contracts.md) |

## 5. PIM, OMS e marketplaces

| Assunto | Documento |
| --- | --- |
| cadastrar uma vez e publicar em vários canais | [Omnichannel](../commerce/omnichannel.md) |
| PIM canônico | [Omnichannel](../commerce/omnichannel.md) |
| OMS | [Omnichannel](../commerce/omnichannel.md) |
| `ChannelListing` e variantes externas | [Omnichannel](../commerce/omnichannel.md) |
| adapter por marketplace | [Omnichannel](../commerce/omnichannel.md) + [Marketplaces](../integrations/marketplaces.md) |
| título/descrição por canal | [Pipeline de mídia](../ai/media-pipeline.md) + [Marketplaces](../integrations/marketplaces.md) |
| Compliance Engine | [Pipeline](../ai/media-pipeline.md) + [Omnichannel](../commerce/omnichannel.md) |
| TikTok Shop | [Marketplaces](../integrations/marketplaces.md) |
| Mercado Livre | [Marketplaces](../integrations/marketplaces.md) |
| Shopee | [Marketplaces](../integrations/marketplaces.md) |
| publicar em vários canais com jobs independentes | [Omnichannel](../commerce/omnichannel.md) |
| evitar overselling | [Omnichannel](../commerce/omnichannel.md) |
| `physical/reserved/safetyStock/available` | [Omnichannel](../commerce/omnichannel.md) + [Estoque](../domain/inventory.md) |
| venda externa sem comissão automática | [Vendas](../domain/sales-cash-commissions.md) |
| webhooks rápidos + dedupe | [Confiabilidade](../integrations/reliability-patterns.md) |
| reconciliation | [Confiabilidade](../integrations/reliability-patterns.md) + [Omnichannel](../commerce/omnichannel.md) |
| credenciais por tenant no backend | [Marketplaces](../integrations/marketplaces.md) + [Segurança](../security/security-architecture.md) |

## 6. SaaS e nuvem

| Assunto | Documento |
| --- | --- |
| desenvolver para Mora sem assumir único cliente | [Multi-tenancy](../saas/multitenancy.md) |
| `Organization` | [Multi-tenancy](../saas/multitenancy.md) |
| `LegalEntity`, `Brand`, `Store`, `StockLocation`, `Website` | [Multi-tenancy](../saas/multitenancy.md) |
| `organization_id` como boundary | [Multi-tenancy](../saas/multitenancy.md) |
| PostgreSQL multi-tenant inicial | [Multi-tenancy](../saas/multitenancy.md) |
| RLS como defesa em profundidade, não substituto | [Multi-tenancy](../saas/multitenancy.md) |
| possibilidade futura de placement dedicado | [Multi-tenancy](../saas/multitenancy.md) |
| um único app para vários tenants | [Mobile](../mobile/mobile-app.md) |
| onboarding self-service | [Plataforma e onboarding](../saas/platform-and-onboarding.md) |
| importação de Bling/planilha/outros | [Import/export/migração](../data/import-export-migration.md) |
| portabilidade/export | [Import/export/migração](../data/import-export-migration.md) + [Multi-tenancy](../saas/multitenancy.md) |
| billing SaaS separado de pagamentos do lojista | [Billing](../saas/billing-entitlements.md) |
| entitlement separado de feature flag | [Billing](../saas/billing-entitlements.md) |
| object storage para fotos | [Arquitetura](../architecture/system-architecture.md) + [Mídia](../ai/media-pipeline.md) |
| secrets manager/credential vault | [Segurança](../security/security-architecture.md) |
| ambientes local/staging/prod | [DevSecOps](../devops/devsecops.md) |
| migrations seguras | [DevSecOps](../devops/devsecops.md) |
| CI/CD e exact SHA | [DevSecOps](../devops/devsecops.md) |
| backup/restore testado | [Data lifecycle](../data/data-lifecycle.md) + [Incidentes](../operations/incident-recovery.md) |
| observabilidade por tenant | [SRE/AIOps](../operations/sre-aiops.md) |
| sem microservices/K8s/Kafka prematuros | [Monólito modular](../architecture/modular-monolith.md) + [Escopo](../product/scope-and-non-goals.md) |
| arquitetura por capabilities, não exceção por cliente | [Multi-tenancy](../saas/multitenancy.md) |
| Mora como design partner inicial | [Visão](../product/vision.md) + [Roadmap](roadmap.md) |

## 7. Engenharia profissional

| Assunto | Documento |
| --- | --- |
| Engineering Constitution | [Constitution](../engineering/constitution.md) |
| business rules first | [Constitution](../engineering/constitution.md) |
| modular monolith | [Monólito modular](../architecture/modular-monolith.md) |
| contratos e ADRs | [ADRs](../adr/README.md) |
| QA por risco | [QA](../qa/test-strategy.md) |
| anti-fake-smoke | [QA](../qa/test-strategy.md) |
| segurança by design | [Segurança](../security/security-architecture.md) |
| DevSecOps/supply chain | [DevSecOps](../devops/devsecops.md) |
| SRE/AIOps | [SRE/AIOps](../operations/sre-aiops.md) |
| performance/capacity/FinOps | [Performance](../operations/performance-capacity.md) |
| incident/recovery | [Incidentes](../operations/incident-recovery.md) |
| runbooks | [Runbooks](../operations/runbook-catalog.md) |
| lifecycle de dados | [Data lifecycle](../data/data-lifecycle.md) |
| acessibilidade | [UX/Acessibilidade](../ux/accessibility-and-design.md) |
| agentes e `AGENTS.md` | [Engineering Constitution](../engineering/constitution.md) + `/AGENTS.md` |

## 8. Itens que continuam deliberadamente abertos

Cobertura temática não significa que todas as decisões já podem ser congeladas. Permanecem dependentes de discovery, contrato externo ou implementação:

- política real de comissão;
- regras e limites de caixa/desconto;
- CNPJs e topologia fiscal reais;
- adquirente/TEF/Pix;
- estratégia fiscal definitiva;
- stack final de backend/ORM/auth/queue/cloud;
- políticas/contratos atuais dos marketplaces no momento da integração;
- schema SQL e OpenAPI executáveis;
- RPO/RTO/SLO numéricos;
- preços/planos do SaaS;
- design system final;
- estratégia offline detalhada do PDV;
- política jurídica/LGPD final;
- regras de suporte/impersonation SaaS;
- hardware real de etiquetas/scanner/PDV.

Esses gaps estão catalogados em [Cobertura documental](documentation-coverage.md) e [Riscos e questões em aberto](risks-and-open-questions.md).
