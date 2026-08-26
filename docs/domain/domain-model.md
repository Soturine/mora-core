# Modelo de Domínio

## Agregados e conceitos principais

### Organização
`Organization`, `LegalEntity`, `Brand`, `Store`, `StockLocation`, `User`, `Employee`, `Membership`, `Role`, `Permission`.

### Catálogo
`Product`, `ProductVariant`, `Category`, `AttributeDefinition`, `MediaAsset`, `PriceList`, `Publication`, `ChannelListing`.

### Estoque
`InventoryMovement`, `InventoryBalance`, `InventoryCount`, `StockTransfer`, `Reservation`, `Receipt`.

### Comercial
`Sale`, `SaleItem`, `Payment`, `Return`, `Exchange`, `Order`, `Fulfillment`.

### Caixa e comissão
`CashRegister`, `CashSession`, `CashMovement`, `CommissionPlan`, `CommissionEntry`, `CommissionStatement`.

### Compras
`Supplier`, `PurchaseOrder`, `PurchaseReceipt`.

### Plataforma
`Website`, `ChannelConnection`, `IntegrationInbox`, `IntegrationOutbox`, `AuditLog`, `Subscription`, `Entitlement`.

## Invariantes fundamentais

- Produto e variante pertencem a uma organização.
- SKU deve ser único dentro do escopo definido para a organização.
- Variante representa combinação válida de atributos; combinações inexistentes não podem ser vendidas.
- Saldo não é alterado diretamente; deriva de movimentos.
- Venda finalizada não é reescrita silenciosamente.
- Comissão deriva de eventos elegíveis e é reversível.
- `sellerId` e `cashierId` são distintos.
- Marketplace sale pode não possuir vendedor humano.
- Fotos originais são preservadas; derivados apontam para origem/proveniência.
- Toda operação sensível registra ator, tenant, timestamp e motivo quando aplicável.

## Valores

- Dinheiro: centavos/decimal exato.
- Quantidade: tipo coerente com unidade; moda normalmente inteiro, mas domínio não deve impedir itens fracionados se a estratégia futura exigir.
- Datas: UTC persistido + timezone contextual.

## Lifecycle

Estados devem ser explícitos para produto/publicação, venda, caixa, comissão, transferência, pedido, listing e integração. Evitar booleanos que escondem state machines complexas.
