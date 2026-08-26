# Roadmap

Roadmap é orientado a capacidade e evidência, não datas artificiais.

## Fase 0 — Discovery e baseline
Mapear operação real, Bling atual, CNPJs, regras de comissão, caixa, hardware, fiscal, fluxos e dados. ADRs iniciais e threat model.

## Fase 1 — Foundation
Identity, Organization/Store, RBAC, audit, catálogo, variantes, categorias, preços, inventory ledger, migrations, API e observabilidade básica.

## Fase 2 — Mobile operacional
Scanner, consulta, produto, fotos, barcode interno, recebimento, inventário, transferências e offline limitado.

## Fase 3 — Storefront integration
Sites deixam dados estáticos e consomem Mora Core; páginas de produto, categorias, novidades, disponibilidade e publicação.

## Fase 4 — Sales/POS
Venda, pagamentos, cash sessions, seller attribution, devoluções/trocas, comissões e relatórios operacionais. Fiscal ainda via estratégia de menor risco.

## Fase 5 — Analytics
Mais vendidos reais, giro, ABC, aging, ticket médio, vendas por loja/vendedor/canal, estoque baixo e divergências.

## Fase 6 — IA
Pipeline de mídia, background removal, modelo virtual, descrições/tags/classificação, evals, quotas e human review.

## Fase 7 — Omnichannel
PIM/OMS, marketplace adapters, webhooks, reservas, sync de estoque/preço, reconciliation e fulfillment.

## Fase 8 — SaaS
Onboarding self-service, importação, website builder, billing/entitlements, tenant tooling, export/offboarding, suporte e hardening.

## Gate para cada fase
Regra de negócio clara, threat/risk review, testes proporcionais, docs atuais, observabilidade, push remoto, CI verde e validação real antes de expandir escopo.
