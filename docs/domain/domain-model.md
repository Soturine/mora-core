# Modelo de Domínio

> **Status:** modelo conceitual. Entidades, cardinalidades, constraints e schema SQL finais dependem de Discovery e ADRs.

## Objetivo

Definir linguagem comum entre negócio e software, evitando que conceitos diferentes sejam representados pelo mesmo campo genérico.

## Princípios

- organização/tenant é fronteira de segurança;
- regra crítica pertence ao módulo dono;
- estados importantes são explícitos;
- dinheiro usa representação exata;
- estoque é ledger;
- venda finalizada não é reescrita silenciosamente;
- histórico preserva snapshots necessários;
- integrações externas não definem o modelo interno;
- fatos de produto são separados de conteúdo gerado por IA.

---

# 1. Estrutura organizacional

## `Organization`

Tenant comercial da plataforma.

```text
Organization
├── id
├── name
├── status
├── timezoneDefault?
├── settings
└── timestamps
```

## `LegalEntity`

Representa pessoa jurídica/CNPJ quando necessário.

Uma organização pode ter uma ou várias entidades legais.

## `Brand`

Identidade comercial. Uma organização pode operar várias marcas.

## `Store`

Estabelecimento/operação comercial.

## `StockLocation`

Local físico/lógico de estoque. Pode ser loja, depósito ou outro local.

Não assumir `Store == StockLocation`.

---

# 2. Pessoas e acesso

## `User`

Identidade digital.

## `Employee`

Pessoa/colaborador no domínio comercial.

## `OrganizationMembership`

Liga usuário a organização.

## `RoleAssignment` / `Permission`

Define capabilities e escopo por organização/loja.

Invariante: ID válido não concede acesso se membership/scope não permitir.

---

# 3. Catálogo

## `Product`

Representa o produto conceitual.

```text
Product
├── id
├── organizationId
├── name
├── slug
├── brandId?
├── categoryId
├── description factual
├── lifecycleStatus
├── publication metadata
└── timestamps
```

## `ProductVariant`

Unidade vendável identificável.

```text
ProductVariant
├── id
├── productId
├── sku
├── attributeValues
├── externalIdentifiers[]
├── pricing references
├── active
└── timestamps
```

Exemplo:

```text
Produto: Vestido Helena
├── Azul / P
├── Azul / M
├── Preto / P
└── Preto / M
```

Não permitir combinação impossível apenas porque cor e tamanho existem separadamente.

## `Category`

Hierárquica:

```text
id
organizationId
parentId?
name
slug
sortOrder
active
visibleOnWebsite
```

## `AttributeDefinition`

Permite atributos por categoria sem transformar tudo em colunas fixas.

Exemplos:

- cor;
- tamanho;
- numeração;
- material;
- faixa etária;
- modelo.

Atributos usados para SKU/variante precisam de semântica clara.

## `PriceList` / `Price`

Preço pode variar por lista/canal/período no futuro. Foundation pode começar simples, mas dinheiro continua exato.

## `ProductMedia` / `MediaAsset`

Original e derivados, com relação a produto/variante e provenance.

---

# 4. Estoque

## `InventoryMovement`

Fato append-only de mudança.

```text
id
organizationId
stockLocationId
variantId
type
quantity
referenceType/referenceId
reason
actorId
operationId?
occurredAt
```

Quantidade usa convenção de sinal ou direção claramente definida.

## `InventoryBalance`

Projeção otimizada do saldo. Não é autoridade editável manualmente.

## `Reservation`

Reserva temporária para pedido/canal.

## `InventoryCount`

Sessão de contagem.

## `InventoryCountLine`

Esperado, contado, diferença.

## `StockTransfer`

Transação de transferência entre locations, com estados quando necessário.

---

# 5. Compras e recebimento

## `Supplier`

Fornecedor no contexto da organização.

## `PurchaseOrder`

Pedido planejado.

## `PurchaseOrderItem`

Produto/variante esperado, quantidade, custo esperado.

## `PurchaseReceipt`

Recebimento real, podendo ser parcial.

Invariante: entrada de estoque acontece por documento/movimento rastreável, não por alteração direta de balance.

---

# 6. Venda

## `Sale`

Transação comercial.

```text
organizationId
storeId
salesChannelId
cashSessionId?
sellerId?
cashierId?
customerId?
status
subtotal
discountTotal
netTotal
currency
completedAt?
```

## `SaleItem`

```text
productId
variantId
skuSnapshot
descriptionSnapshot
quantity
unitPrice
discount
netAmount
```

Snapshots preservam história quando catálogo muda.

## `Payment`

Um ou vários pagamentos ligados à venda.

## `Return` / `ReturnItem`

Devolução rastreável.

## `Exchange`

Pode ser modelada como fluxo que conecta devolução e nova venda/diferença. Semântica final depende da operação/fiscal.

---

# 7. Caixa

## `CashRegister`

Caixa físico/lógico.

## `CashSession`

Abertura → operação → fechamento.

## `CashMovement`

```text
OPENING_FLOAT
CASH_SALE
CASH_REFUND
WITHDRAWAL
SUPPLY
ADJUSTMENT
CLOSING_ADJUSTMENT
```

---

# 8. Comissão

## `CommissionPlan`

Regra com vigência.

## `CommissionEntry`

Ledger de comissão.

## `CommissionStatement`

Fechamento por período/funcionário.

Histórico não é recalculado automaticamente quando plano futuro muda.

---

# 9. Clientes

## `Customer`

Identificação opcional conforme finalidade.

Separar dados necessários para venda/fiscal de dados de marketing/relacionamento e consentimentos.

---

# 10. Commerce / canais

## `SalesChannel`

Exemplos:

```text
POS
WEBSITE
TIKTOK_SHOP
MERCADO_LIVRE
SHOPEE
```

## `ChannelConnection`

Conta externa autorizada por tenant.

## `ChannelListing`

Representação de produto em um canal.

## `ChannelListingVariant`

Mapping da variante canônica para variante externa.

## `Order`

Pedido canônico vindo do site/marketplace.

## `OrderItem`

Mapping para produto/variante.

## `Fulfillment`

Separação/envio/tracking.

---

# 11. Storefront

## `Website`

Site por organização/marca.

## `Theme` / `BrandTheme`

Design tokens/preset.

## `PageSection`

Blocos seguros configuráveis, não HTML/JS arbitrário.

---

# 12. Media e IA

## `MediaAsset`

Original/derivado.

## `AIGeneration`

Provenance, facts usados, provider/model/template, custo, aprovação.

## `GeneratedContent`

Texto/imagem derivada, separada de Product Truth.

---

# 13. Plataforma SaaS

## `Subscription`

Assinatura da organização.

## `Entitlement`

Direito comercial/capacidade.

## `UsageMeter`

Consumo auditável para quotas/billing quando aplicável.

## `FeatureFlag`

Rollout técnico, separado de entitlement.

---

# 14. Integrações e confiabilidade

## `IntegrationInbox`

Eventos recebidos/dedupe.

## `IntegrationOutbox`

Eventos/efeitos a entregar quando transaction/outbound consistency justificar.

## `IntegrationMapping`

Mapeia IDs canônicos ↔ externos.

## `Job`

Processamento assíncrono com estado/retry quando infraestrutura adotar modelo persistente correspondente.

---

# 15. Auditoria

## `AuditEvent`

Ator, tenant, store/context, ação, recurso, reason, approval, request ID, before/after sanitizado e timestamp.

Audit não substitui domain ledger e não é debug log.

---

# Invariantes globais

1. recurso tenant-owned pertence a uma organização;
2. SKU é único no escopo definido;
3. variante precisa representar combinação válida;
4. estoque muda por movimento;
5. operação idempotente não duplica efeito;
6. venda finalizada não é silenciosamente mutada;
7. dinheiro não usa float binário;
8. seller e cashier são conceitos separados;
9. venda online pode não ter seller humano;
10. comissão tem ledger/reversão;
11. mídia original é preservada;
12. IA não altera Product Truth sem aprovação/fluxo explícito;
13. external ID sozinho não autoriza tenant;
14. estado crítico possui transitions permitidas;
15. correções financeiras/estoque deixam rastro.

# Estados que exigem state machines

Ao implementar, formalizar pelo menos:

- Product/Publication;
- Sale;
- CashSession;
- CommissionEntry/Statement;
- StockTransfer;
- Reservation;
- Order/Fulfillment;
- ChannelListing;
- Import/Export job;
- Media processing;
- Onboarding.

Evitar combinações de booleanos como `active=true, published=false, archived=true` sem semântica.

# Valores e tipos

## Dinheiro

Inteiro em centavos ou decimal exato; uma convenção por boundary.

## Quantidade

Moda usa inteiros, mas unidade de medida futura pode exigir decimal. Não escolher float.

## Timestamp

Persistir instante em UTC; timezone da loja para regras civis/relatórios.

## IDs

Estáveis e opacos conforme ADR.

# Integridade no banco

O PostgreSQL deve reforçar invariantes com:

- `NOT NULL`;
- foreign keys;
- unique constraints;
- check constraints;
- transaction isolation/locks apropriados;
- índices que sustentem queries críticas.

Não depender apenas de validação frontend.

# Questões ainda abertas

- topologia CNPJ/lojas;
- escopo exato de unicidade de SKU;
- política de preço;
- produtos fracionados;
- venda compartilhada por item;
- estados finais de troca/devolução;
- modelo fiscal final;
- relação Store/StockLocation real;
- customer data necessária.

Todas devem sair do [Discovery](../discovery/operational-discovery.md).

## Relacionados

- [Estoque](inventory.md)
- [Vendas/caixa/comissões](sales-cash-commissions.md)
- [Identidade](identity-employees-permissions.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Multi-tenancy](../saas/multitenancy.md)
