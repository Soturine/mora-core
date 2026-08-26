# Billing, Planos, Entitlements e Quotas

> **Status:** arquitetura SaaS planejada. Preços, nomes de planos e provedor de cobrança não estão definidos.

## Separação crítica

Existem dois domínios de dinheiro diferentes:

```text
Retail Payment
consumidor → comércio

SaaS Billing
comércio → Mora Core
```

Nunca misturar ledger, status, refund ou provider de ambos.

## Subscription

Conceito futuro:

```text
Subscription
├── organizationId
├── plan/version
├── status
├── billingCustomerReference
├── startedAt
├── trialEndsAt?
├── currentPeriodStart/End
├── graceUntil?
└── cancelledAt?
```

Estados precisam de state machine explícita conforme provider/política.

## Entitlements

Entitlement responde:

> “Este tenant tem direito a esta capacidade e em qual limite?”

Exemplos:

```text
stores.max = 3
users.max = 10
websites.enabled = true
websites.max = 2
marketplaces.maxConnections = 3
ai.image.monthlyQuota = 200
ai.text.monthlyQuota = 1000
storage.bytes = ...
analytics.advanced = true
```

Evitar:

```ts
if (plan === 'PRO') { ... }
```

espalhado pela base.

## Feature flags != entitlements

- **Entitlement:** direito comercial.
- **Feature flag:** rollout técnico/experimento/kill switch.

Uma organização pode ter entitlement mas feature temporariamente desabilitada por incidente/rollout. Ambiente interno pode ter flag sem entitlement comercial.

## Catálogo de planos

Planos futuros podem usar versionamento para que mudança comercial não altere contratos existentes silenciosamente.

Não há nomes/preços aprovados neste momento. Exemplos discutidos como Starter/Growth/Pro são hipóteses, não oferta.

## Metering

Uso variável precisa ser determinístico e auditável.

Candidatos:

- gerações de imagem;
- modelo virtual;
- tokens/text generation;
- storage;
- egress;
- volume de integração;
- sites/lojas/usuários ativos.

### Idempotência de consumo

Retry de job de IA não pode cobrar duas vezes pelo mesmo processamento lógico sem política explícita.

`UsageEvent` pode ter:

```text
organizationId
metric
quantity
operationId
source
occurredAt
```

## Quota

Ao chegar no limite, UX deve informar claramente:

- consumo;
- limite;
- período;
- ação possível.

Não falhar silenciosamente.

IA pode entrar em modo desabilitado sem quebrar catálogo/estoque.

## Provedor de billing

Fica atrás de adapter.

Responsabilidades:

- customer/subscription;
- checkout/portal de assinatura;
- invoice/cobrança;
- webhook;
- refund/crédito quando aplicável.

Tokens/secrets ficam no backend.

## Webhooks de billing

```text
verify
→ dedupe
→ persist event
→ ack
→ worker
→ update subscription state
→ reconciliation
```

Não aceitar payload sem assinatura/autenticidade conforme provider.

## Grace period

Falha de cobrança não pode apagar dados ou invalidar vendas históricas.

Política deve definir fases, por exemplo:

```text
ACTIVE
→ PAYMENT_PAST_DUE
→ GRACE
→ RESTRICTED
→ SUSPENDED
```

Comportamento exato precisa de decisão comercial/legal.

## Suspensão segura

Preferir restringir novas operações conforme risco e permitir:

- login do owner;
- export;
- billing update;
- leitura de dados quando contrato permitir.

Nunca impedir acesso a dado legalmente necessário sem política revisada.

## Upgrade/downgrade

Questões:

- redução de `stores.max` abaixo do usado;
- quota já excedida;
- sites extras;
- marketplace connections extras;
- prorata;
- data de vigência.

Padrão deve evitar deletar recursos automaticamente. Pode bloquear criação nova até adequação.

## Trial

Trial ainda precisa de:

- duração;
- features;
- limites;
- anti-abuse;
- conversão;
- retenção após expirar.

## Segurança

- billing admin separado de retail payment;
- webhooks assinados;
- least privilege;
- nenhuma chave no frontend;
- PII mínima;
- audit de alterações;
- step-up em mudança sensível;
- rate limits.

## Observabilidade/FinOps

Monitorar:

- MRR/assinaturas quando produto comercial existir;
- falha de webhook;
- tenants past due;
- quota usage;
- custo de IA/storage por tenant;
- margem unitária do SaaS;
- anomalia de consumo.

AIOps pode detectar anomalias, não suspender tenant automaticamente sem policy.

## Testes

- webhook duplicado/out-of-order;
- upgrade/downgrade;
- trial → paid;
- payment failure;
- grace;
- quota boundary;
- usage retry;
- tenant isolation;
- provider outage;
- reconciliation.

## Relacionados

- [Onboarding](platform-and-onboarding.md)
- [Multi-tenancy](multitenancy.md)
- [FinOps](../operations/performance-capacity.md)
- [IA](../ai/ai-governance.md)
