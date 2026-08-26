# Compras, Fornecedores e Recebimento

> **Status:** capacidade planejada. Fluxo real deve ser levantado no Discovery antes da implementação.

## Objetivo

Ligar decisão de compra, fornecedor, mercadoria esperada e mercadoria recebida ao estoque, custo e analytics sem transformar o recebimento em simples “+ quantidade”.

## Conceitos

### `Supplier`

Fornecedor da organização.

Dados mínimos dependem da operação e finalidade. Evitar coletar informação desnecessária.

### `PurchaseOrder`

Pedido planejado:

```text
id
organizationId
supplierId
legalEntityId?
store/location destino?
status
orderedAt
expectedAt?
commercialTerms?
createdBy
```

### `PurchaseOrderItem`

```text
product/variant/reference
quantityOrdered
unitCostExpected
attributes/reference do fornecedor
```

### `PurchaseReceipt`

Registra o que realmente chegou.

Pode ser:

- total;
- parcial;
- com divergência;
- sem purchase order em fluxo simplificado.

## Estados possíveis

Purchase order:

```text
DRAFT
→ APPROVED
→ ORDERED
→ PARTIALLY_RECEIVED
→ RECEIVED
→ CANCELLED
```

A lista final depende do processo real.

## Fluxo completo desejado

```text
necessidade de compra
→ pedido
→ fornecedor envia
→ mercadoria + NF/XML
→ conferência
→ divergências
→ product/variant mapping
→ receipt
→ InventoryMovement(RECEIPT)
→ custo atualizado conforme policy
→ etiquetas/fotos/publicação
```

## Recebimento mobile

O operador pode usar o app:

1. selecionar pedido/fornecedor;
2. escanear produto;
3. identificar variante;
4. informar recebido;
5. registrar item desconhecido como draft;
6. fotografar;
7. sinalizar divergência;
8. concluir receipt;
9. gerar etiquetas.

A UI não deve permitir que um produto desconhecido seja silenciosamente associado ao SKU errado.

## XML/NF-e

Importação futura de XML pode auxiliar:

- fornecedor;
- itens;
- códigos;
- quantidades;
- custos;
- documento de origem.

Mas XML externo é input não confiável e precisa de validação/mapping. Dados fiscais finais dependem de provider/contabilidade.

## Mapping de fornecedor

Referência do fornecedor não substitui SKU interno.

Possível entidade:

```text
SupplierProductMapping
supplierId
supplierReference
productId
variantId?
lastSeenAt
```

## Custo

Custo é dado sensível e não deve aparecer para vendedor sem permissão.

A política de custo precisa decidir:

- último custo;
- custo médio;
- custo por lote;
- frete/despesas;
- impostos recuperáveis/não recuperáveis;
- currency, se necessário.

Não inventar fórmula antes de entender contabilidade/uso.

## Divergências

Exemplos:

- faltou item;
- sobrou item;
- cor/tamanho diferente;
- custo divergente;
- produto avariado;
- código desconhecido.

Receipt deve registrar e permitir resolução explícita.

## Idempotência

Repetir conclusão de receipt não pode duplicar entrada de estoque.

Usar operation/document identity e constraints adequadas.

## Concorrência

Se dois operadores conferem o mesmo recebimento, a state machine precisa impedir conclusão duplicada ou sobrescrita.

## Etiquetas

Após produto/variante confirmado, gerar barcode interno apenas quando necessário. Não inventar GTIN oficial.

## Fotos e publicação

Receber mercadoria e publicar são estados distintos:

```text
RECEIVED
≠
PUBLISHED
```

Produto pode entrar no estoque e aguardar fotos/revisão.

## Analytics de compras

Futuro:

- volume por fornecedor;
- lead time;
- divergência;
- custo;
- giro dos itens recebidos;
- sell-through;
- aging;
- retorno por coleção/fornecedor;
- sugestão de reposição.

Previsão/ML só após histórico confiável.

## Segurança

- custo restrito;
- criação/aprovação de pedido por capability;
- alterações de receipt auditadas;
- tenant/store scope;
- anexos/XML com validação;
- não executar conteúdo de documentos.

## Critérios de teste

- receipt parcial;
- conclusão idempotente;
- divergência;
- item desconhecido;
- mapping correto;
- estoque muda exatamente uma vez;
- tenant isolation;
- custo oculto para perfil não autorizado.

## Questões do Discovery

- existe pedido de compra hoje?
- importam XML?
- quem compra/aprova?
- custo médio ou último custo?
- fornecedor usa referência própria?
- mercadoria chega parcialmente?
- há consignação?
- há devolução para fornecedor?
- precisa controlar contas a pagar no Core ou integrar inicialmente?

## Relacionados

- [Discovery](../discovery/operational-discovery.md)
- [Estoque](inventory.md)
- [Mobile](../mobile/mobile-app.md)
- [Modelo de domínio](domain-model.md)
