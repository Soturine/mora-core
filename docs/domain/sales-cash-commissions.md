# Vendas, Caixa e Comissões

> **Status:** arquitetura de domínio planejada. Regras comerciais reais de comissão, limites de desconto e política de fechamento ainda precisam ser validadas com a operação e contabilidade.

## Objetivo

Modelar venda, recebimento, caixa e comissão como processos auditáveis e consistentes. Estes domínios envolvem dinheiro, estoque, remuneração e responsabilidade de funcionários; portanto, não podem ser tratados como simples CRUD.

## Fluxo operacional de referência

```text
Funcionário autentica
        ↓
seleciona organização/loja
        ↓
abre turno/caixa quando aplicável
        ↓
registra venda
        ↓
atribui vendedor e caixa corretamente
        ↓
registra pagamentos
        ↓
finaliza venda atomicamente
        ↓
movimenta estoque
        ↓
provisiona comissão elegível
        ↓
fim do turno/dia
        ↓
fecha/reconcilia caixa
        ↓
fim do período
        ↓
fecha comissão
```

## Venda

`Sale` representa a transação comercial, não um formulário editável indefinidamente.

Campos conceituais:

```text
Sale
├── id
├── organizationId
├── storeId
├── cashSessionId?
├── salesChannelId
├── sellerId?
├── cashierId?
├── customerId?
├── status
├── subtotal
├── discountTotal
├── netTotal
├── currency
├── createdAt
├── completedAt?
└── version
```

### Seller != Cashier

`sellerId` identifica a pessoa à qual a venda é atribuída comercialmente. `cashierId` identifica quem processou o recebimento/caixa. Podem ser a mesma pessoa, mas o modelo não assume isso.

Vendas de marketplace/site normalmente têm `sellerId = null`, salvo regra explícita de atribuição. Não creditar venda online aleatoriamente a uma funcionária.

### Venda por item

No primeiro modelo, uma venda pode ter um vendedor principal. Caso a operação real precise dividir comissão por item, adicionar associação explícita por `SaleItem`, sem quebrar o modelo principal.

## Itens da venda

```text
SaleItem
├── id
├── saleId
├── productId
├── variantId
├── skuSnapshot
├── descriptionSnapshot
├── quantity
├── unitPrice
├── discount
├── netAmount
└── sellerId? (somente se política por item existir)
```

Snapshots comerciais evitam que renomear um produto no futuro altere retroativamente o histórico da venda.

## Estados e imutabilidade

Estados possíveis devem ser formalizados em state machine quando a implementação começar. Exemplo inicial:

```text
DRAFT
  ↓
PENDING_PAYMENT
  ↓
COMPLETED
  ├──→ PARTIALLY_RETURNED
  ├──→ RETURNED
  └──→ REFUNDED (conforme semântica real)

DRAFT/PENDING_PAYMENT → CANCELLED
```

Venda concluída não deve ser reescrita livremente. Correções financeiras/estoque usam eventos compensatórios, devoluções, estornos ou documentos específicos.

## Concorrência e idempotência

Finalização de venda deve proteger contra:

- duplo clique/retry criando duas vendas;
- mesma reserva de estoque sendo consumida duas vezes;
- lost update de estoque;
- pagamento confirmado duas vezes;
- webhook duplicado de canal externo.

Ferramentas possíveis: transação, constraint, optimistic version, idempotency key, lock de linha quando necessário e inbox/dedupe para eventos externos.

## Pagamentos

`Payment` é separado de `Sale` porque uma venda pode ter múltiplos meios.

```text
Payment
├── id
├── saleId
├── method
├── amount
├── status
├── providerReference?
├── createdAt
└── settledAt?
```

Meios possíveis incluem dinheiro, Pix, débito, crédito e outros definidos pela organização. Dados sensíveis de cartão nunca são armazenados pelo Mora Core sem necessidade e escopo regulatório explícitos; a estratégia preferida é delegar dados de pagamento a provedores adequados.

## Caixa

### `CashRegister`

Representa o caixa físico/lógico configurado em uma loja.

### `CashSession`

Representa um turno de operação:

```text
CashSession
├── id
├── organizationId
├── storeId
├── cashRegisterId
├── openedBy
├── openedAt
├── openingFloat
├── status
├── closedBy?
├── closedAt?
└── reconciliation summary
```

Não permitir duas sessões concorrentes no mesmo caixa se a política operacional não permitir; proteger com constraint/lock apropriado.

## Movimentações de caixa

`CashMovement` deve registrar fatos, não apenas saldo final:

```text
OPENING_FLOAT
CASH_SALE
CASH_REFUND
WITHDRAWAL        # sangria
SUPPLY            # suprimento
ADJUSTMENT
CLOSING_ADJUSTMENT
```

Cada movimento relevante registra:

- organização/loja/caixa;
- sessão;
- valor;
- motivo;
- autor;
- aprovador quando aplicável;
- referência para venda/operação de origem;
- timestamp.

## Fechamento diário/por turno

O fechamento deve reconciliar o que o sistema espera com o que foi efetivamente contado/confirmado.

Exemplo:

```text
                  Esperado        Informado        Diferença
Dinheiro          R$ 550,00       R$ 545,00        -R$ 5,00
Pix               R$ 920,00       R$ 920,00         R$ 0,00
Débito            R$ 620,00       R$ 620,00         R$ 0,00
Crédito         R$ 1.430,00     R$ 1.430,00         R$ 0,00
```

Dinheiro é contado fisicamente. Pix/cartões são principalmente reconciliados contra registros/provedores. A experiência de UI não deve induzir o usuário a tratar tudo como “dinheiro no caixa”.

### Fechamento cego

Configuração opcional:

```text
blindCashClosing = true
```

Nesse modo, o operador informa o valor contado antes de enxergar o esperado. O objetivo é reduzir ajuste intencional para “fazer bater”. O resultado e diferença só aparecem depois da submissão.

### Divergências

Diferença acima de limite configurado pode exigir:

- motivo;
- aprovação de gerente;
- auditoria;
- alerta.

Nunca ajustar silenciosamente o ledger para apagar a diferença.

## Sangria e suprimento

Toda sangria/suprimento precisa ser rastreável. Pode haver política de aprovação por valor. Exemplo:

```text
R$ 500 de sangria
→ operador solicita
→ manager aprova
→ movimento registrado
→ saldo esperado recalculado
```

## Descontos e cancelamentos

A autorização é server-side. Exemplo:

```text
desconto <= limite do vendedor → permitido
acima do limite → aprovação de manager
```

Cancelamentos de valor elevado ou após fechamento podem exigir política adicional.

## Comissão

Comissão não deve ser hardcoded como `sale.total * 0.03`.

### `CommissionPlan`

Define regra e vigência:

```text
CommissionPlan
├── id
├── organizationId
├── name
├── calculationType
├── rate/value
├── baseRule
├── includeDiscountedItems
├── returnPolicy
├── cancellationPolicy
├── effectiveFrom
├── effectiveTo?
└── active
```

A base pode ser venda líquida, itens elegíveis ou outra regra validada. Alteração de plano não reescreve comissão histórica.

### `CommissionEntry`

Comissão é tratada como ledger:

```text
PENDING   → provisionada
APPROVED  → elegível/confirmada
REVERSED  → revertida por devolução/cancelamento
SETTLED   → fechada/paga conforme processo
```

Campos:

```text
CommissionEntry
├── id
├── organizationId
├── employeeId
├── saleId
├── saleItemId?
├── amount
├── basisAmount
├── planSnapshot/version
├── status
├── reason
├── createdAt
└── reversedByEntryId?
```

## Devolução, troca e comissão

Regra inicial recomendada, sujeita a validação:

```text
Venda concluída → comissão provisionada
Cancelamento → comissão revertida
Devolução total → reversão correspondente
Devolução parcial → reversão proporcional/por item
Troca → eventos de devolução + nova venda/diferença conforme política
```

Nunca apagar uma comissão anterior para “corrigir”. Gerar entrada compensatória rastreável.

## Fechamento mensal de comissão

`CommissionStatement` materializa o período e a composição:

```text
Funcionária
Período
Vendas elegíveis
Base comissionável
Reversões
Ajustes autorizados
Total provisionado
Total aprovado
Total fechado
```

A usuária autorizada deve conseguir abrir o statement e chegar às vendas/itens que formaram o total.

Alteração posterior gera adjustment em período seguinte ou fluxo específico; não reescrever silenciosamente statement já fechado.

## Comissão e aspectos trabalhistas

O Mora Core calcula segundo política configurada, mas **não determina sozinho a regra legal de remuneração**. A política real deve ser validada com responsáveis da empresa e profissionais contábeis/jurídicos quando aplicável.

## Analytics derivados

Vendas válidas alimentam:

- faturamento;
- unidades vendidas;
- ticket médio;
- mais vendidos;
- desempenho por loja/canal;
- desempenho por vendedor;
- comissão;
- formas de pagamento;
- devoluções;
- divergências de caixa.

### Mais vendidos

Nunca usar “maior estoque” como proxy. Ranking vem de `SaleItem` elegível, com janela definida pelo backend (ex.: últimos 30/90 dias, temporada) e tratamento de devoluções/cancelamentos.

## Auditoria

Operações sensíveis: desconto, cancelamento, sangria, ajuste, reabertura de caixa, alteração de comissão e override precisam gerar audit event com ator, aprovador, motivo e before/after sanitizado quando aplicável.

## Testes essenciais

Quando implementado:

- finalização idempotente;
- estoque reduz exatamente uma vez;
- duas vendas concorrentes não tornam saldo inválido;
- cancelamento/devolução gera reversão correta;
- comissão não é perdida nem duplicada;
- statement fecha composição determinística;
- caixa esperado é derivável dos movimentos;
- fechamento cego não vaza esperado antes da submissão;
- autorização falha fechada;
- tenant/store scope não cruza organizações;
- marketplace sem seller não credita comissão indevida.

## Questões a validar no Discovery

- um vendedor por venda ou por item?
- política real de comissão e vigência;
- base antes/depois de desconto;
- tratamento de devolução/troca;
- limites de desconto/cancelamento/sangria;
- caixas por loja e compartilhamento de operadores;
- meios de pagamento efetivos;
- fechamento diário x por turno;
- necessidade de fechamento cego;
- fluxo com adquirentes/TEF/Pix;
- integração fiscal e contábil.

## Relacionados

- [Identidade, funcionários e permissões](identity-employees-permissions.md)
- [Estoque](inventory.md)
- [Fiscal e pagamentos](fiscal-and-payments.md)
- [Analytics](../product/analytics-and-reporting.md)
- [Segurança](../security/security-architecture.md)
