# Estoque

> **Status:** arquitetura de domínio planejada. Políticas de saldo negativo, reserva, transferência e inventário precisam ser validadas no Discovery.

## Objetivo

Manter um histórico confiável de **por que** cada saldo mudou, permitindo venda, inventário, transferência, omnichannel e auditoria sem depender de um campo `quantity` sobrescrito manualmente.

## Princípio: ledger de movimentos

```text
InventoryMovement
+ recebimento
- venda
+ devolução
- perda/avaria
- transferência origem
+ transferência destino
+/- ajuste autorizado
```

`InventoryBalance` é uma projeção/materialização para leitura rápida. O saldo não é a única evidência.

## Entidades

### `InventoryMovement`

Campos conceituais:

```text
id
organizationId
stockLocationId
variantId
type
quantityDelta
referenceType
referenceId
operationId?
reason?
actorId?
occurredAt
createdAt
```

### `InventoryBalance`

```text
organizationId
stockLocationId
variantId
physical
reserved
version
updatedAt
```

`available` pode ser calculado conforme policy, inclusive safety stock.

## Tipos de movimento

Base inicial:

- `OPENING_IMPORT`;
- `RECEIPT`;
- `SALE`;
- `RETURN`;
- `TRANSFER_OUT`;
- `TRANSFER_IN`;
- `LOSS`;
- `ADJUSTMENT_IN`;
- `ADJUSTMENT_OUT`;
- `CORRECTION` quando necessário;
- reserva pode ser ledger separado ou movimento lógico, conforme design final.

Não multiplicar tipos sem semântica operacional.

## Recebimento

```text
PurchaseReceipt/manual receipt
→ validar variante/local/quantidade
→ persistir documento
→ InventoryMovement(RECEIPT)
→ atualizar balance
```

Recebimento retryable usa `operationId`/idempotência para não somar duas vezes.

## Venda

A finalização precisa impedir race:

```text
verificar disponibilidade
+ reservar/consumir atomicamente
+ criar Sale
+ InventoryMovement(SALE)
```

Não fazer:

```text
GET saldo = 1
... 500ms ...
UPDATE saldo = 0
```

sem proteção de concorrência.

Ferramentas possíveis: transação, row lock, optimistic version, atomic update e constraints, escolhidas após testes.

## Saldo negativo

Política deve ser explícita:

- proibido;
- permitido somente em cenários autorizados;
- contingência/offline com reconciliation posterior.

Padrão seguro é não aceitar saldo negativo silencioso.

## Reservas

Para OMS/site/marketplace:

```text
physical = 5
reserved = 2
safetyStock = 1
available = 2
```

Uma reserva possui:

- tenant;
- location;
- variant;
- quantity;
- order/reference;
- status;
- expiration quando aplicável.

Estados possíveis:

```text
ACTIVE → CONSUMED
ACTIVE → RELEASED
ACTIVE → EXPIRED
```

Expiração precisa ser job/reconciliation seguro.

## Safety stock por canal

Pode evitar overselling, mas não duplica estoque físico.

Exemplo:

```text
physical: 3
reserved: 0
channel safety: 1
publishable: 2
```

Policy pode variar por canal/local no futuro.

## Transferência

### Imediata

Para lojas adjacentes, se a operação confirmar:

```text
transfer
→ TRANSFER_OUT origem
→ TRANSFER_IN destino
```

na mesma transação.

### Com trânsito

Se houver cadeia de custódia:

```text
CREATED
→ SENT
→ RECEIVED
```

Durante trânsito, quantidade fica explicitamente em estado/local correspondente; não pode aparecer simultaneamente nos dois saldos.

Idempotência impede dupla recepção.

## Inventário

Contagem não é ajuste.

```text
InventoryCount
→ linhas esperadas
→ scans/contagem
→ diferença
→ revisão
→ aprovação
→ InventoryAdjustment
```

### Contagem móvel

Modo coletor:

```text
scan → +1
scan → +1
```

Offline pode guardar contagens/eventos localmente e sincronizar com IDs estáveis.

Não aceitar que replay duplique contagem/ajuste.

## Ajuste

Ajuste manual exige:

- permissão;
- motivo;
- local;
- variante;
- quantidade;
- ator;
- aprovação em thresholds quando definida;
- audit event.

Não permitir editar balance diretamente no admin.

## Perda, avaria e furto

Precisam de motivo/categoria para analytics e auditoria. A política real de motivo é definida pela operação.

## Devolução

Devolução que retorna item vendável gera movimento positivo no local correto. Item danificado pode ir para location/status não vendável ou movimento específico conforme modelo futuro.

Não somar automaticamente toda devolução ao estoque disponível sem inspeção quando operação exigir.

## Múltiplos locais

```text
Organization
├── Loja Feminina / salão
├── Loja Masculina / salão
└── Depósito futuro
```

Disponibilidade no storefront pode:

- consolidar;
- escolher location prioritário;
- mostrar por loja;
- esconder quantidade exata.

É policy, não regra hardcoded.

## Produtos e variantes

Estoque pertence à **variante vendável**, não apenas ao produto.

```text
Vestido Azul P = 2
Vestido Azul M = 0
Vestido Preto P = 4
```

Isso é essencial para moda.

## Identificadores

Barcode/GTIN/SKU resolvem para `variantId`; o ledger não depende da tecnologia de leitura. Futuro RFID pode ser adicionado sem remodelar saldo.

## Importação inicial

Migrar saldo de Bling/planilha gera:

```text
OPENING_IMPORT
```

com origem/fingerprint/data, permitindo reconciliar.

Não escrever balance cru e apagar provenance.

## Reconciliation

Rotinas verificam:

- balance x movimentos;
- reservas expiradas;
- marketplace projection x available;
- transferências presas;
- eventos externos não processados.

Drift gera alerta/tarefa, não correção silenciosa automática sem política.

## Concorrência — cenários obrigatórios

Testar:

- duas vendas na última unidade;
- venda + inventário;
- venda + transferência;
- reserva + expiração;
- dois receives da mesma transferência;
- retry do mobile;
- webhook duplicado;
- ajuste concorrente.

## Auditoria

Movimento em si já é evidência de domínio. Ajustes/aprovações também geram audit events de ação humana.

Não usar audit log como substituto do ledger.

## Performance

Índices por tenant/location/variant/time. Balance evita somar histórico inteiro em cada leitura.

Rebuild/reconciliation precisa ser possível em ambiente controlado e testado.

## Métricas

- estoque atual;
- baixo/zerado;
- turnover;
- aging;
- perdas;
- ajustes;
- divergências de inventário;
- reservas;
- transferências pendentes;
- sync lag por canal.

## Critérios de aceitação da primeira implementação

- movimento cria saldo correto;
- retry não duplica;
- concorrência protege última unidade;
- cross-tenant proibido;
- ajuste exige permissão/motivo;
- balance é reconciliável;
- histórico não é apagado;
- variante é unidade de estoque;
- testes reais com PostgreSQL.

## Questões do Discovery

- negativo é permitido hoje?
- quantos locations?
- transferência imediata ou sent/received?
- reserva para clientes existe?
- consignado existe?
- devolução retorna automaticamente?
- frequência de inventário?
- motivos de ajuste?

## Relacionados

- [Mobile](../mobile/mobile-app.md)
- [Barcode](../mobile/barcodes-and-scanning.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Vendas](sales-cash-commissions.md)
- [Purchasing](purchasing-and-suppliers.md)
