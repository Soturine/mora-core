# PIM, OMS e Omnichannel

> **Status:** arquitetura planejada. Integrações reais serão adicionadas incrementalmente.

## Objetivo

Cadastrar o produto uma vez e permitir que o mesmo dado canônico alimente loja física, site próprio e canais externos sem duplicar cadastro e sem transformar cada integração em regra do domínio.

## PIM — Product Information Management

O Mora Core mantém o produto canônico:

```text
Product
├── identidade
├── nome canônico
├── descrição factual
├── marca
├── taxonomia
├── atributos
├── variantes
├── SKU/identificadores
├── mídia
├── dimensões/peso quando aplicável
├── pricing
├── publicação
└── dados fiscais necessários
```

Canais não gravam seus próprios campos diretamente em `Product`.

## ChannelListing

```text
ChannelListing
├── id
├── organizationId
├── productId
├── channelConnectionId
├── externalListingId?
├── externalProductId?
├── channelCategoryId?
├── titleOverride?
├── descriptionOverride?
├── publicationStatus
├── syncStatus
├── lastSyncedAt?
└── lastError?
```

Variantes possuem mapping próprio:

```text
ChannelListingVariant
├── variantId
├── externalSkuId?
├── externalVariationId?
├── channelPrice?
└── channelStockProjection?
```

## Adapter por canal

```text
ChannelConnector
├── publishProduct()
├── updateListing()
├── updatePrice()
├── updateStock()
├── ingestOrders()
├── acknowledgeOrder()
├── cancelListing()
└── health/reconciliation
```

Implementações possíveis:

- `MoraStorefrontConnector`
- `TikTokShopConnector`
- `MercadoLivreConnector`
- `ShopeeConnector`
- futuros canais.

Não espalhar `if marketplace == ...` pelo domínio.

## Publicação multicanal

```text
Produto canônico
→ selecionar canais
→ validar requisitos de cada canal
→ gerar/montar listing
→ publicar jobs independentes
→ registrar resultado por canal
```

Falha em um canal não deve desfazer automaticamente publicações válidas nos outros. Cada job é rastreável e reprocessável com segurança.

## Compliance Engine

Cada canal tem requisitos variáveis de categoria, atributos, GTIN, título, mídia, logística e regras de conteúdo.

```text
Product Truth
+ ChannelListing
+ Media
+ Channel Policy Version
        ↓
READY / WARNING / BLOCKED
```

As políticas precisam ser versionadas e revalidadas porque marketplaces mudam contratos e regras.

## OMS — Order Management System

Pedidos externos e do site convergem em modelo canônico:

```text
Channel Order
→ ingest/dedupe
→ canonical Order
→ pagamento/status
→ reserva de estoque
→ fiscal quando aplicável
→ separação
→ expedição
→ tracking
→ cancelamento/devolução
```

O objetivo é evitar que o lojista precise administrar cada painel isoladamente para tarefas rotineiras.

## Estoque único, múltiplos canais

Se há 3 unidades físicas, publicar “3” em quatro canais não significa 12 unidades.

Modelo conceitual:

```text
physical = 3
reserved = 1
safetyStock = 1
available = physical - reserved - safetyStock = 1
```

A projeção por canal pode aplicar regras adicionais, mas a fonte de verdade continua central.

## Overselling

Controles:

- reserva atômica;
- idempotência;
- safety stock opcional;
- atualização rápida de canais;
- reconciliation periódica;
- tratamento explícito de cancelamento/expiração;
- nunca confiar apenas em cache externo.

## SalesChannel

```text
POS
WEBSITE
MERCADO_LIVRE
SHOPEE
TIKTOK_SHOP
OUTRO
```

A venda registra canal. `storeId` pode ser nulo em venda puramente online. `sellerId` não deve ser preenchido automaticamente para marketplace.

## Analytics

A origem do canal permite calcular:

- faturamento por canal;
- unidades;
- margem quando custo confiável existir;
- taxa/frete;
- devoluções;
- desempenho de listing;
- estoque comprometido;
- rentabilidade por canal.

## Webhooks

Inbound:

```text
validar assinatura
→ limitar payload
→ persistir eventId/fingerprint
→ responder rápido
→ worker
→ dedupe
→ buscar estado canônico quando necessário
```

Eventos duplicados e fora de ordem são esperados.

## Reconciliation

Jobs periódicos comparam Mora Core x canal para detectar drift em:

- estoque;
- preço;
- status de listing;
- pedidos;
- fulfillment.

Reconciliation não substitui alertas sobre falhas contínuas.

## Relacionados

- [Marketplaces](../integrations/marketplaces.md)
- [Confiabilidade de integrações](../integrations/reliability-patterns.md)
- [Pipeline de mídia](../ai/media-pipeline.md)
- [Estoque](../domain/inventory.md)
- [Storefront](../product/storefront.md)
