# Fluxos Técnicos End-to-End

> **Status:** arquitetura de referência para conectar módulos. As tecnologias concretas, payloads e detalhes de transação ainda dependem de ADR/spec.

## Objetivo

Mostrar como os principais fluxos atravessam módulos sem misturar responsabilidades.

---

## 1. Recebimento de mercadoria

```text
Mobile/Admin
→ Purchasing.receive
→ valida PO/fornecedor/tenant
→ Catalog resolve Product/Variant
→ Inventory cria InventoryMovement(RECEIPT)
→ balance materializado
→ AuditEvent
→ async: labels/media/publication readiness
```

### Falhas

- variante inexistente → draft/cadastro;
- quantidade inválida → rejeita;
- retry com mesmo `operationId` → no-op idempotente;
- divergência pedido x recebido → registra exceção, não sobrescreve pedido.

---

## 2. Cadastro com foto e IA

```text
Mobile
→ create product draft
→ upload original
→ Media valida MIME/tamanho/checksum
→ Object Storage
→ job de derivados
→ AI Gateway (opcional)
→ GeneratedContent / AIGeneration
→ revisão humana
→ READY/PUBLISHED
```

Product Truth não é alterado silenciosamente pela IA.

---

## 3. Publicação no site

```text
Catalog Product PUBLISHED
→ Public Catalog Projection
→ cache/CDN
→ Storefront
```

A projeção pública exclui custo, fornecedor, notas internas e segredos.

---

## 4. Publicação em marketplace

```text
Product/Variant
→ Commerce readiness validation
→ ChannelListing draft
→ MarketplaceAdapter.publish
→ external ID mapping
→ status/listing audit
```

Falha em um canal não invalida o produto canônico nem outros canais.

---

## 5. Site → WhatsApp

```text
Product page
→ signed/contextual commerce entry
→ WhatsApp Conversation
→ agent/tool layer
→ Catalog/Inventory/Price
→ resposta natural
```

O agent não recebe acesso genérico a banco ou HTTP.

---

## 6. Busca por foto

```text
Customer image
→ media validation
→ visual extraction/embedding
→ tenant filter = organizationId
→ vector/catalog search
→ availability filter
→ ranking
→ response
```

Filtro tenant acontece antes da recomendação final.

Sem match:

```text
→ CustomerRequest
→ DemandSignal
→ optional SourcingRequest
```

---

## 7. Carrinho e reserva

```text
Conversation/Storefront
→ Cart
→ validate variant/price
→ Reservation transaction
→ Inventory.available decreases
```

Expiração:

```text
TTL/job
→ release reservation idempotently
```

---

## 8. Pagamento online

```text
Checkout/WhatsApp
→ Payment intent/provider
→ provider webhook
→ verify signature
→ IntegrationInbox/dedupe
→ Payment = PAID
→ workflow resumes
```

Screenshot não muda Payment para `PAID`.

---

## 9. Pedido pago → venda

```text
PaymentConfirmed
→ Order transition
→ Sale finalize
→ consume Reservation / InventoryMovement(SALE)
→ CommissionEntry? policy
→ Fiscal job
→ Fulfillment
→ Reporting
→ Audit
```

Cada etapa assíncrona tem idempotência/reconciliation quando aplicável.

---

## 10. Venda física

```text
POS
→ scan variant
→ pricing
→ Sale draft
→ seller/cashier
→ payment(s)
→ finalize transaction
→ InventoryMovement(SALE)
→ CashMovement quando cash
→ CommissionEntry
→ Fiscal
→ receipt/confirmation
```

---

## 11. Pedido de marketplace

```text
Webhook/channel poll
→ signature/auth
→ IntegrationInbox
→ mapping external listing → canonical variant
→ Order
→ Reservation
→ payment/status reconciliation
→ Fiscal/Fulfillment
→ Sale lifecycle
→ Inventory
→ outbound stock sync
```

Eventos duplicados ou fora de ordem não podem duplicar efeitos.

---

## 12. Troca/devolução

```text
ReturnRequest
→ eligibility policy
→ Return/ReturnItem
→ Payment refund/adjustment
→ Inventory movement conforme condição
→ Fiscal event/document
→ Commission reversal
→ Audit
```

Troca pode ser orquestrada como devolução + nova venda/diferença conforme regra final.

---

## 13. Fechamento de caixa

```text
CashSession OPEN
→ movimentos durante turno
→ closing input por meio
→ expected vs counted/reconciled
→ discrepancy
→ approval/reason se necessário
→ CLOSED
→ report/audit
```

---

## 14. Sourcing

```text
CustomerRequest
→ SourcingRequest
→ ProcurementTrip
→ SourcingCandidate
→ customer approval
→ PurchaseOrder/ad-hoc purchase
→ PurchaseReceipt
→ InventoryMovement(RECEIPT)
→ Reservation para cliente
→ notification
→ Sale
```

---

## 15. Back-in-stock

```text
BackInStockSubscription
→ Inventory availability transition 0 → >0
→ eligibility/consent/template
→ notification job
→ WhatsApp/site notification
```

Não disparar marketing sem policy/consentimento.

---

## 16. Funcionária assume conversa

```text
Conversation WAITING_HUMAN
→ assign employee transaction
→ mode = HUMAN/HYBRID
→ AI paused or copilot-only
→ human responds
→ optional handback to AI
```

`ConversationAssignment` não define comissão automaticamente.

---

## 17. Bling na fase inicial

```text
Mora Core
→ Bling Adapter
→ fiscal/ERP function
→ webhook/poll/reconciliation
→ canonical state update
```

Nunca tratar webhook como verdade suficiente quando o contrato pede refetch do estado canônico.

---

## 18. Falha de integração

```text
external call
→ timeout/5xx/rate limit
→ retry policy
→ backoff/jitter
→ dead-letter/manual intervention quando necessário
→ reconciliation
```

Nenhuma falha externa deve deixar estoque ou dinheiro em estado silenciosamente inconsistente.

---

## 19. Multi-tenancy em todos os fluxos

Todo request/job/event carrega contexto resolvido de organização.

```text
Auth/session or ChannelConnection
→ resolve organization
→ authorize scope
→ query/write scoped
```

Nunca:

```text
payload.organizationId
→ confiar diretamente
```

Search, vector index, cache, exports, analytics e logs também preservam tenant boundary.

---

## 20. Observabilidade transversal

Todo fluxo importante deve permitir correlação por:

```text
requestId / correlationId
organizationId
actor/channel
resource IDs
external event/provider refs
```

Sem logar secrets ou dados sensíveis indiscriminadamente.

---

## 21. Relações principais

```text
Product
 └── ProductVariant
      ├── InventoryMovement / Balance
      ├── Reservation
      ├── SaleItem
      ├── OrderItem
      └── ChannelListingVariant

Customer
 ├── Conversation
 ├── Cart / Order
 ├── Wishlist
 ├── BackInStockSubscription
 └── CustomerRequest

CustomerRequest
 └── SourcingRequest
      └── SourcingCandidate
           └── Purchase/Receipt
```

## Relacionados

- [Cenário futuro da Mora](../product/mora-reference-future-state.md)
- [Arquitetura](system-architecture.md)
- [Modelo de domínio](../domain/domain-model.md)
- [Módulos](../product/modules.md)
- [Roadmap](../roadmap/roadmap.md)
