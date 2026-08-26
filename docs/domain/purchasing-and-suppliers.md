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

## Demanda do cliente como entrada para compras

Compras não devem depender apenas do feeling do comprador ou de histórico de venda. O Mora Core também pode registrar **demanda não atendida** proveniente de site, WhatsApp, PDV ou outros canais.

Exemplo:

```text
cliente pede vestido preto M
→ estoque = 0
→ alternativas não converteram
→ CustomerRequest / DemandSignal
→ possível SourcingRequest
→ próxima compra/viagem
```

Isso não significa compra automática. O sistema gera informação e sugestão; comprador/proprietário continua aprovando conforme budget, fornecedor e estratégia.

Ver [Demanda, Encomendas e Sourcing](../commerce/customer-demand-and-sourcing.md).

## Viagem de compras / sourcing

A operação pode agrupar solicitações em uma `ProcurementTrip`, por exemplo uma ida a São Paulo.

O comprador no mobile pode ter:

- lista de solicitações;
- fotos de referência;
- tamanho/cor/atributos;
- teto de custo autorizado;
- prioridade;
- cliente vinculado quando necessário;
- fornecedor;
- status `encontrado/não encontrado`;
- captura de foto/preço de candidato.

Um `SourcingCandidate` ainda não é `Product`; só vira produto/variante após aprovação/cadastro.

Se uma cliente já confirmou uma encomenda, o recebimento pode criar uma `Reservation` para que a unidade não seja vendida a outra pessoa antes do prazo acordado.

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

## Catálogo B2B de fornecedor

Se futuramente um fornecedor disponibilizar API/arquivo/catálogo estruturado, criar `SupplierCatalogAdapter` isolado do catálogo público.

Possíveis dados:

- referência do fornecedor;
- atributos;
- custo;
- disponibilidade B2B;
- lote/grade;
- prazo;
- mídia de referência autorizada.

Esses dados não entram automaticamente no `Product Truth`; precisam de mapping/revisão.

Um agente de compras autorizado pode usar pesquisa externa para descobrir fornecedores, mas isso é **backoffice**. Resultados externos/concorrentes nunca podem ser sugeridos diretamente ao consumidor como alternativa de compra.

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
- demanda não atendida;
- taxa de sourcing convertido em venda;
- fornecedor que mais resolve solicitações;
- sugestão de reposição.

Previsão/ML só após histórico confiável.

## Sugestão de compra

Uma `PurchaseSuggestion` pode combinar, quando houver dados confiáveis:

```text
vendas reais
+ demanda não atendida
+ estoque atual
+ estoque em trânsito
+ reservas
+ lead time
+ sazonalidade
+ budget
```

A saída é uma **sugestão explicável**, nunca uma ordem de compra autônoma produzida por LLM.

## Segurança

- custo restrito;
- criação/aprovação de pedido por capability;
- alterações de receipt auditadas;
- tenant/store scope;
- anexos/XML com validação;
- não executar conteúdo de documentos;
- CustomerRequest/SourcingRequest nunca atravessa `organizationId`;
- comprador vê somente PII necessária da cliente;
- pesquisa externa de fornecedor é capability administrativa separada.

## Critérios de teste

- receipt parcial;
- conclusão idempotente;
- divergência;
- item desconhecido;
- mapping correto;
- estoque muda exatamente uma vez;
- tenant isolation;
- custo oculto para perfil não autorizado;
- sourcing de A não aparece em B;
- reserva de item encomendado após receipt;
- demanda não atendida não é contada como venda.

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
- como funcionam as viagens de compra?
- clientes já pedem para trazer mercadorias específicas?
- a loja aceita sinal/depósito para encomenda?
- qual prazo de reserva após a mercadoria chegar?
- fornecedores enviam catálogo/grade por WhatsApp, planilha, site ou API?

## Relacionados

- [Discovery](../discovery/operational-discovery.md)
- [Estoque](inventory.md)
- [Mobile](../mobile/mobile-app.md)
- [Modelo de domínio](domain-model.md)
- [Demanda, Encomendas e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
