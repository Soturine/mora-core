# Estratégia de Integração com Bling

## Contexto

A migração deve reduzir risco. A direção é inicialmente usar o Bling como fonte de verdade operacional/fiscal enquanto Mora Core aprende o domínio real e integra sites.

## Fase de transição

```text
Bling REST + Webhooks
→ Receiver
→ verificar assinatura
→ dedupe eventId
→ responder rapidamente
→ Integration Inbox/Worker
→ buscar estado canônico pela API
→ PostgreSQL Mora Core
→ Catalog API
→ sites
```

## Por que refetch

Webhooks podem duplicar, atrasar ou chegar fora de ordem. O evento dispara reconciliação, mas o estado atual deve ser confirmado pela API quando necessário.

## Cuidados

Rate limits, paginação, OAuth, backoff, timeouts, reconciliation periódica e mapeamento explícito de produtos/variações/depósitos.

## Migração

Fases: leitura/sync → admin Mora Core com Bling ainda operacional → writes controlados/adapters → PDV/fiscal em paralelo → eventual retirada somente após validação prolongada.

## Antes de codificar

Auditar o uso real: produtos, variantes, compras/XML, estoque, depósitos, venda, pagamentos, fiscal, trocas, caixa, comissões, relatórios, CNPJs, hardware e permissões.
