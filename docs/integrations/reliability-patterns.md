# Confiabilidade de Integrações

## Princípio

Rede falha, APIs mudam, webhooks duplicam e mensagens podem chegar fora de ordem. Integrações devem ser projetadas para isso.

## Inbound webhook

1. autenticar/validar assinatura;
2. limitar payload;
3. persistir `eventId`/fingerprint;
4. responder rapidamente;
5. processar em worker;
6. dedupe/idempotência;
7. reconciliar com fonte quando necessário.

## Outbound

Timeout obrigatório, response-size budget, retry apenas para falhas transitórias/idempotentes, exponential backoff + jitter e rate-limit awareness.

## Inbox/Outbox

Usar quando consistência entre transação local e evento externo justificar. Não adicionar apenas por padrão arquitetural.

## Dead letter

Jobs que excedem política de retry entram em estado acionável com diagnóstico e possibilidade segura de replay.

## Reconciliation

Rotinas periódicas detectam drift de estoque, preço, listing e pedidos. Reconciliation não deve mascarar falhas crônicas; métricas/alertas continuam necessários.

## Schema drift

Adapters validam payloads e registram versão/provedor. Mudança externa incompatível deve falhar de forma controlada, não aceitar dados silenciosamente.
