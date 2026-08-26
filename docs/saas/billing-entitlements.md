# Billing, Planos e Entitlements do SaaS

## Separação de domínios

`Retail Payment` = dinheiro pago por consumidor à loja.

`SaaS Billing` = assinatura que a organização paga ao Mora Core.

Nunca misturar ambos.

## Entitlements

Capacidade comercial deve ser representada como entitlement, por exemplo:

- `stores.max`
- `users.max`
- `websites.enabled`
- `marketplaces.maxConnections`
- `ai.image.monthlyQuota`
- `analytics.advanced`

Evitar `if plan == PRO` espalhado no código.

## Feature flags

Feature flag controla rollout técnico; entitlement controla direito comercial. Um cliente pode ter direito a uma feature ainda desabilitada por rollout e vice-versa em ambiente interno.

## Metering

Métricas de consumo precisam ser determinísticas, auditáveis e idempotentes. IA, storage e volume de integração são candidatos a quotas futuras.

## Billing provider

Provedor de cobrança deve ficar atrás de adapter. Webhooks de pagamento são assinados/deduplicados. Falha de billing não deve corromper dados operacionais; política de grace period precisa ser explícita.

## Planos

Preços/nomes não estão definidos. Qualquer tabela comercial atual é `planejada`, não contrato.
