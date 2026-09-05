# Módulos e Capacidades

> **Status:** mapa funcional de longo prazo. Cada módulo será implementado por fases; existência neste documento não significa feature pronta.

## Objetivo

Organizar o Mora Core por capacidades de negócio, evitando uma aplicação onde regras de estoque, venda, IA, hardware e marketplace ficam misturadas.

A arquitetura padrão continua sendo **monólito modular**.

## 1. Identity & Organizations

Responsável por identidade, vínculo e contexto organizacional.

Capacidades: autenticação, sessão/revogação, MFA quando aplicável, `Organization`, `LegalEntity`, `Brand`, `Store`, `StockLocation`, `User`, `Employee`, memberships, roles/capabilities, escopo por loja, convites, lifecycle de funcionário e trilha de auditoria de acesso.

Não confundir login com funcionário. Uma identidade pode participar de mais de uma organização; funcionário desligado mantém histórico.

## 2. Catalog

Fonte canônica das informações de produto.

Capacidades: produto, variante, SKU, GTIN/barcode externo, identificador interno, categorias hierárquicas, atributos estruturados, marca, coleção, faixa etária/audience quando aplicável, dados fiscais necessários, preços/listas de preço, mídia, publicação, lifecycle e status de dado verificado/demonstrativo em protótipos.

Para moda, cada combinação válida de atributos precisa de `ProductVariant` real, não apenas arrays soltos de tamanhos e cores.

## 3. Inventory

Autoridade sobre movimentação e disponibilidade.

Capacidades: ledger de movimentos, saldo materializado, recebimento, venda, devolução, perda/avaria, ajuste autorizado, inventário, transferência, reserva/liberação, disponibilidade física/reservada/disponível, safety stock por canal, múltiplos locais e reconciliação.

Saldo nunca é corrigido silenciosamente apenas sobrescrevendo `quantity`.

## 4. Purchasing & Sourcing

Gestão de fornecedores, compras planejadas e demanda não atendida convertida em possível abastecimento.

Capacidades:

- fornecedores;
- pedido de compra;
- custo/condições;
- recebimento parcial/total;
- divergência pedido x recebido;
- XML/documento de origem quando apropriado;
- histórico por fornecedor;
- `CustomerRequest` e `DemandSignal` como input comercial;
- `SourcingRequest`;
- `ProcurementTrip` para viagens de compra;
- `SourcingCandidate`;
- aprovação da cliente quando aplicável;
- reserva após recebimento;
- `PurchaseSuggestion` baseada em dados reais;
- supplier catalog adapters futuros.

IA pode ajudar a agrupar/explicar demanda, mas não compra mercadoria autonomamente.

## 5. Sales

Venda física/digital e correções comerciais.

Capacidades: carrinho/venda, `SaleItem`, seller/cashier attribution, descontos, aprovação, múltiplos pagamentos, conclusão idempotente, cancelamento, devolução, troca, snapshots históricos e integração com estoque, caixa, comissão, fiscal e analytics.

Venda concluída não é editada arbitrariamente.

## 6. POS & Store Operations

O POS é a superfície operacional de balcão. Ele **não é uma segunda fonte de verdade**: regras críticas continuam nos módulos de domínio.

Capacidades planejadas:

- login/contexto de loja/caixa;
- abertura/continuação de sessão;
- scan de barcode e fallback por digitação/pesquisa;
- identificação de produto/variante;
- carrinho de venda;
- seller e cashier separados;
- seleção de cliente/consumidor padrão conforme política;
- descontos/acréscimos e approvals;
- múltiplas formas de pagamento;
- integração via `PaymentPort`/TEF quando definida;
- finalização idempotente;
- emissão fiscal coordenada via `FiscalPort`;
- recibo/reimpressão;
- cancelamento/troca/devolução conforme regra;
- sangria/suprimento/fechamento por meio dos módulos de Cash;
- status/health de periféricos;
- modo degradado/contingência explicitamente permitido;
- atalhos de teclado/touch focados em velocidade de balcão;
- suporte a instalação/atualização/diagnóstico do cliente local.

### Periféricos

O POS trabalha com capabilities, não fabricantes:

```text
barcode_input
receipt_print
label_print
cash_drawer
payment_terminal
```

A integração concreta fica atrás de adapters/Local Device Bridge quando necessário. Ver [Integração POS/periféricos](../architecture/pos-device-integration.md).

### Baseline real

O piloto precisa considerar as duas estações observadas nas lojas Mora, que hoje usam PDVs e hardware diferentes. Ver [baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md).

## 7. Cash

Controle de caixa/turno: abertura, fundo inicial, sessão, venda em dinheiro, reembolso, sangria, suprimento, ajuste, fechamento, blind close, reconciliação por meio de pagamento, divergência, autorização e audit.

## 8. Commissions

Comissão auditável por funcionário: planos com vigência, regras por base, provisionamento, reversão, aprovação, fechamento/settlement, extrato mensal, drill-down e métricas próprias no app conforme permissão.

Nenhum percentual será hardcoded como verdade universal.

## 9. Customers / CRM

Capacidades progressivas:

- identificação opcional;
- contatos com consentimento/finalidade;
- histórico de compras autorizado;
- preferências;
- wishlist;
- back-in-stock;
- `CustomerRequest`;
- consentimentos de marketing;
- futura fidelidade/relacionamento;
- export/deleção conforme política.

Evitar coletar PII sem necessidade.

## 10. Conversational Commerce

Canal de venda assistido por IA, inicialmente com forte foco em WhatsApp e integração ao site.

Capacidades:

- contexto produto/variante vindo do storefront;
- consulta real de catálogo/preço/estoque;
- personal shopper;
- busca por foto;
- comparação;
- alternativas da mesma organização;
- carrinho;
- reserva;
- cross-sell responsável;
- pagamento por adapter;
- handoff humano;
- resumo da conversa;
- back-in-stock;
- abandono consentido;
- pós-venda;
- demanda não atendida;
- sourcing request.

### Invariante

**Nenhuma busca, recomendação, embedding, marketplace fallback ou agent tool pode sugerir produto de outra `Organization`.**

## 11. Mobile

Ferramenta operacional baseada em capacidades do celular.

Capacidades: login/tenant/store context, scanner, produto, grade, câmera, uploads resilientes, código interno, etiqueta, recebimento, inventário, transferências, consulta de estoque, dashboards, comissão própria, caixa autorizado, notificações, offline seletivo e modo de compras/sourcing.

## 12. Media

Pipeline seguro de ativos: upload original, checksum, object storage, variantes por produto/cor, resize, thumbnails, WebP/AVIF, remoção de EXIF desnecessário, processamento assíncrono, lifecycle, CDN e signed URLs.

## 13. AI

IA como assistente, não autoridade transacional.

Capacidades: descrição, título, tags, classificação, categoria, melhoria de imagem, remoção de fundo, modelo virtual, conteúdo por canal, busca visual, interpretação de intenção comercial, evals, provenance, quota/metering, AI Gateway e revisão humana.

IA nunca decide estoque, preço autorizado, imposto, tenant, permissão, pagamento ou compra de fornecedor.

## 14. Storefront / Websites

Sites próprios alimentados pelo catálogo central.

Capacidades: múltiplos sites por organização/marca, temas, tokens, homepage por blocos seguros, categorias dinâmicas, página de produto, variantes, galeria/zoom, Novidades, Mais Vendidos, Destaques, Promoções, SEO, domínio/subdomínio, publicação, carrinho e entrada para commerce conversacional.

CTAs futuros incluem busca visual, WhatsApp contextualizado, back-in-stock e pedido de estilo/encomenda.

## 15. Commerce / PIM / OMS

### PIM
Produto canônico, enrichment, listing por canal, overrides, mapping e readiness validation.

### OMS
Pedido canônico, status, pagamento, reserva, separação, fiscal, fulfillment, tracking, cancelamento, devolução e reconciliação.

### Canais
`POS`, `WEBSITE`, `WHATSAPP`, `TIKTOK_SHOP`, `MERCADO_LIVRE`, `SHOPEE` e futuros canais.

## 16. Integrations

Adapters para contratos externos.

Primeiros candidatos:

- Bling;
- sistemas legados NOOVA/Lips durante migração, se houver interface/export suportado;
- WhatsApp Business Platform/Meta;
- TikTok Shop;
- Mercado Livre;
- Shopee;
- fiscal/SEFAZ via provider ou integração apropriada;
- pagamento/TEF/Pix;
- supplier catalogs;
- storage/CDN;
- email/notificação.

Infra comum: OAuth/secrets, webhooks, inbox/outbox quando justificadas, retry/backoff, rate-limit awareness, dead letter, reconciliation e observabilidade.

Integrações locais de hardware usam boundary própria e não devem ser confundidas com integração SaaS externa.

## 17. Reporting & Analytics

Capacidades: faturamento, ticket médio, unidades, vendas por loja/canal/vendedor, mais vendidos, estoque baixo/zerado, giro, sell-through, aging, curva ABC, custo/margem quando confiáveis, devoluções, comissão, pagamentos, caixa/divergências, health comercial de integrações, demanda perdida, recovery rate por alternativas e sourcing conversion.

Analytics de negócio é diferente de telemetria técnica.

## 18. Audit

Eventos sensíveis: ator, tenant, loja/contexto, ação, recurso, before/after sanitizado, motivo, aprovação, request/correlation ID e timestamp.

Audit não é debug log e não guarda secrets.

## 19. Fiscal

Integração de alto risco atrás de adapter/port: emissão, cancelamento, status, XML, DANFE/representação, contingência, vínculo com venda/pedido, requisitos de marketplace e legal entity.

A implementação fiscal brasileira do zero não é objetivo inicial.

Uma impressora térmica local não é `FiscalProvider`; ela apenas imprime representação/recibo autorizado quando aplicável.

## 20. Billing SaaS

Domínio separado do dinheiro das vendas: assinatura, plano, entitlement, quota, metering, trial, grace period, billing provider, cobrança, suspensão segura e offboarding.

## 21. Platform Operations

Ferramentas internas futuras: tenant health, suporte, incident response, feature flags, migrations, jobs/replay, integration health, billing health, observabilidade e audit de ações da plataforma.

Para deployments com POS/bridge local, também pode incluir inventory de versões, health e rollout de cliente, sem conceder controle remoto irrestrito por padrão.

Qualquer impersonation/support access futuro exige política e auditoria fortes.

## Dependências principais

```text
Identity/Organizations
        ↓
Catalog ───→ Inventory ───→ Sales
   │            │             │
   │            │          POS/Cash/Commission
   │            │             │
 Media/AI       ├────→ Commerce/OMS
   │            │             │
   │       Purchasing/Sourcing│
   │            ↑             │
Storefront → Conversational Commerce
       \            │        /
        └──── Reporting ─────┘
```

## Regra de ownership

Cada invariável tem um módulo dono. Exemplos: saldo → Inventory; comissão → Commissions; autorização → Identity; listing → Commerce/Integrations; mídia original → Media; fatos de produto → Catalog; sourcing/purchase decision → Purchasing/Sourcing; recomendação ao consumidor → Commerce policy com tenant boundary obrigatório.

O POS pode orquestrar a experiência, mas não toma ownership de estoque, fiscal ou pagamento apenas porque acessa o periférico.

## Relacionados

- [Arquitetura](../architecture/system-architecture.md)
- [POS/periféricos](../architecture/pos-device-integration.md)
- [Baseline real das lojas](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [Modelo de domínio](../domain/domain-model.md)
- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [Roadmap](../roadmap/roadmap.md)
