# Roadmap — Commerce Conversacional e Demanda Assistida

> **Status:** sub-roadmap orientado a risco/evidência. Complementa o [roadmap principal](roadmap.md) e não define datas.

## Objetivo

Colocar o WhatsApp e o site como canais de venda conectados ao Mora Core de forma incremental, começando por assistência comercial e evoluindo para pedido, pagamento, fiscal, sourcing e SaaS.

---

## CC-0 — Discovery

Levantar:

- número(s) WhatsApp atual(is);
- app Business vs Platform/API;
- catálogo existente;
- quem responde;
- tempo de atendimento;
- como distribuem conversas;
- como clientes pedem produtos;
- quantidade de pedidos por foto;
- como tratam produto sem estoque;
- pedidos para “trazer de São Paulo”;
- reserva;
- pagamento/Pix;
- entrega/retirada;
- nota fiscal;
- política de handoff;
- consentimento de mensagens futuras.

### Gate

Fluxo atual e política comercial documentados.

---

## CC-1 — Concierge conectado ao catálogo

### Capacidades

- site → WhatsApp com contexto;
- consulta real de produto/variante;
- disponibilidade por loja/location;
- fotos;
- alternativas internas;
- handoff humano;
- logs/analytics básicos.

### Regra

**Todas as recomendações são restritas à mesma `Organization`.**

### Não inclui

- cobrança automática;
- baixa automática;
- fiscal automático.

### Gate

- zero cross-tenant em testes adversariais;
- nenhuma resposta inventa preço/estoque;
- handoff preserva contexto;
- fallback manual existe.

---

## CC-2 — Busca visual + demanda não atendida

### Capacidades

- cliente envia foto;
- visual search tenant-scoped;
- produtos semelhantes;
- `CustomerRequest`;
- `DemandSignal`;
- back-in-stock;
- dashboard “o que clientes pediram e não tínhamos”.

### Gate

- imagem/lifecycle/PII tratados;
- vector index tenant-safe;
- demanda não confundida com venda;
- opt-out/notificação definidos.

---

## CC-3 — Carrinho e reserva

### Capacidades

- `Cart` canônico;
- adicionar/remover item pela conversa;
- carrinho do site retomado no WhatsApp;
- cross-sell responsável;
- reserva com TTL;
- última unidade transacional;
- expiração/release.

### Gate

- concorrência da última unidade;
- idempotência;
- preço recalculado no servidor;
- reserva não deixa estoque preso indefinidamente.

---

## CC-4 — Pagamento

### Capacidades

- Payment Adapter;
- Pix/link/experiência WhatsApp elegível;
- webhook/consulta de confirmação;
- estado ambíguo;
- manual review quando necessário;
- refund/cancelamento básico.

### Regra

Comprovante visual nunca confirma pagamento automaticamente.

### Gate

- duplicate webhook não duplica cobrança/venda;
- timeout reconciliável;
- provider sandbox validado;
- secrets protegidos.

---

## CC-5 — Pedido, venda e baixa automática

```text
PaymentConfirmed
→ Order state
→ Sale
→ InventoryMovement(SALE)
→ CommissionEntry? conforme policy
```

### Gate

- transaction/workflow recuperável;
- retry idempotente;
- estoque exato;
- venda rastreável ao canal/conversation.

---

## CC-6 — Fiscal + fulfillment

### Capacidades

- FiscalPort inicialmente via Bling/provedor;
- NF-e/NFC-e conforme operação;
- XML/DANFE;
- retirada;
- entrega;
- tracking;
- pós-venda.

### Gate

- contador valida;
- homologação;
- contingência;
- reconciliation;
- fallback.

---

## CC-7 — Sourcing / encomendas

### Capacidades

- `SourcingRequest`;
- `ProcurementTrip`;
- ida a São Paulo/lista de compra;
- candidato encontrado;
- foto/preço para cliente;
- aprovação;
- compra;
- receipt;
- reserva para cliente;
- notificação de chegada.

### Gate

- interesse ≠ obrigação;
- preço/prazo claros;
- policy de sinal/cancelamento;
- PII mínima no app de comprador;
- sourcing tenant-safe.

---

## CC-8 — Automação de relacionamento

### Capacidades

- back-in-stock;
- carrinho abandonado consentido;
- wishlist;
- mensagens de retirada;
- pós-venda;
- recomendações personalizadas permitidas.

### Gate

- consentimento/template/policy Meta;
- frequência/anti-spam;
- opt-out;
- analytics sem PII excessiva.

---

## CC-9 — Canais próprios conectados

O agente pode reconhecer listings da **mesma organização** em:

- Mercado Livre;
- Shopee;
- TikTok Shop;
- site próprio.

Pode oferecer continuidade de compra nesse canal quando policy permitir, consultando preço/frete/status específicos.

Nunca pesquisa marketplace público para sugerir concorrentes.

---

## CC-10 — SaaS self-service

### Capacidades

- Embedded Signup WhatsApp;
- WABA/phone por tenant;
- templates;
- catalog mapping;
- quotas;
- metering/billing;
- health por conexão;
- suporte;
- onboarding idempotente.

### Gate

- App Review/permissões;
- tenant isolation audit;
- credentials lifecycle;
- webhook routing seguro;
- custos por tenant.

---

## CC-11 — Agent runtime avançado

Avaliar com dados reais:

- Mora Agent próprio;
- Meta Business Agent/Business Agent Platform;
- multi-provider LLM;
- chat no site;
- Instagram;
- voz/calls quando disponível.

Decisão via ADR, sem lock-in de domínio.

---

## Métricas do sub-roadmap

- conversão WhatsApp;
- atendimento sem handoff;
- handoff bem sucedido;
- tempo até compra;
- demanda sem estoque;
- venda recuperada por alternativa;
- back-in-stock conversion;
- sourcing conversion;
- erro de preço/estoque (meta: zero);
- cross-tenant leakage (meta: zero);
- payment ambiguity;
- incidentes por integração;
- custo de IA/mensagem por venda.

## Relacionados

- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [Fiscal Brasil](../integrations/fiscal-brazil.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Roadmap principal](roadmap.md)
