# Estoque

## Princípio

Estoque é um **ledger auditável de movimentações**, não um campo `quantity` que usuários sobrescrevem.

```text
InventoryMovement
+ recebimento
- venda
+ devolução
- perda
+/- ajuste
- transferência origem
+ transferência destino
```

`InventoryBalance` é uma projeção/materialização otimizada, reconstruível a partir de movimentos quando tecnicamente viável.

## Tipos de movimento

`RECEIPT`, `SALE`, `RETURN`, `TRANSFER_OUT`, `TRANSFER_IN`, `LOSS`, `ADJUSTMENT`, `RESERVATION`, `RESERVATION_RELEASE`, `CORRECTION` conforme necessidade real.

## Invariantes

- Todo movimento tem organization, location, variant, quantidade, razão, origem e timestamp.
- Movimento financeiro/comercial relacionado guarda referência ao documento de origem.
- Ajuste manual exige permissão e motivo.
- Não aceitar saldo negativo silencioso; política de negative stock deve ser explícita.
- Transferência deve evitar dupla recepção e ser idempotente.

## Disponibilidade omnichannel

Separar `physical`, `reserved`, `available` e `safetyStock` quando vendas online forem ativadas.

`available = physical - reserved - safetyStock`, respeitando regras por local/canal.

## Inventário móvel

Contagem cria `InventoryCount`; diferença não altera saldo automaticamente sem policy/aprovação definida. Scanner offline acumula operações idempotentes para sincronização posterior.

## Concurrency

Venda, reserva e ajuste concorrentes usam transação/locking/constraints apropriados. `check stock → later decrement` sem atomicidade é proibido.
