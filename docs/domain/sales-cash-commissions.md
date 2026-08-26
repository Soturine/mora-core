# Vendas, Caixa e Comissões

## Venda

`Sale` registra `storeId`, `cashSessionId?`, `sellerId?`, `cashierId?`, itens, descontos, pagamentos, status e timestamps.

Seller e cashier não são o mesmo conceito. Em operação simples podem coincidir; a modelagem não deve assumir isso.

## Estados

Venda pode evoluir por estados explícitos como `DRAFT → COMPLETED → PARTIALLY_RETURNED/RETURNED` ou `CANCELLED`, conforme regra definida. Venda concluída não é editada retroativamente; correções geram eventos/documentos compensatórios.

## Caixa

`CashSession` representa abertura e fechamento de um caixa/turno.

Movimentos: fundo inicial, venda em dinheiro, devolução, sangria, suprimento, ajuste e fechamento.

Fechamento compara esperado x contado por meio de pagamento. Dinheiro é contado fisicamente; Pix/cartão são reconciliados. Divergências relevantes exigem motivo e eventualmente aprovação.

### Fechamento cego

Configuração opcional pode ocultar o valor esperado antes da contagem para reduzir viés operacional.

## Comissões

Comissão não é `sale.total * percent` hardcoded. `CommissionPlan` define base, percentual/regra, elegibilidade de desconto/devolução/cancelamento e vigência.

`CommissionEntry` funciona como ledger:
- `PENDING`/provisionada após venda elegível;
- `APPROVED` conforme política;
- `REVERSED` em cancelamento/devolução;
- `SETTLED` no fechamento/pagamento.

Devolução parcial reverte apenas a parcela correspondente quando a regra comercial determinar.

## Fechamento mensal

`CommissionStatement` congela composição auditável por período e funcionária: vendas válidas, base comissionável, reversões e total. Alterações posteriores geram ajuste, não reescrita silenciosa.

## Segurança

Descontos altos, cancelamentos, sangrias e ajustes podem exigir autorização de gerente. A UI nunca substitui a autorização server-side.
