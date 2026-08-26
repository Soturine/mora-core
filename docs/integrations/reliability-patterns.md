# Confiabilidade de Integrações

> **Status:** padrão arquitetural para Bling, marketplaces, fiscal, pagamentos, IA e futuros providers.

## Princípio

Toda integração externa é uma rede entre sistemas independentes. Devemos assumir:

- timeout;
- indisponibilidade;
- rate limit;
- resposta duplicada;
- evento duplicado;
- evento fora de ordem;
- schema drift;
- credencial expirada;
- estado local e externo divergente.

Confiabilidade vem de design explícito, não de `try/catch` + retry infinito.

---

# Inbound webhooks

Fluxo padrão:

```text
request
→ limite de tamanho
→ identificar provider/connection
→ verificar assinatura/autenticidade
→ proteção replay quando aplicável
→ dedupe eventId/fingerprint
→ persist inbox
→ 2xx rápido
→ worker
→ validar schema
→ resolver tenant seguro
→ processar
→ reconciliation se necessário
```

## Resposta rápida

Webhook receiver não deve:

- chamar várias APIs;
- rodar IA;
- processar imagens;
- fazer relatório;
- esperar operação longa.

Persistir o necessário e responder dentro do contrato do provider.

## Assinatura

Usar raw body quando requerido. Comparação de MAC/signature deve ser segura. Secrets nunca entram em log.

## Replay

Quando provider inclui timestamp/nonce, validar janela conforme contrato. Mesmo com assinatura válida, dedupe continua necessário.

---

# Idempotência

Uma operação repetida com a mesma identidade lógica não pode duplicar efeito.

Casos:

- order ingest;
- stock update;
- payment callback;
- invoice upload;
- mobile sync;
- import;
- publication job.

## Chave

Pode usar:

- external event ID;
- external order ID + account;
- explicit `Idempotency-Key`;
- operation ID gerado pelo cliente;
- fingerprint quando provider não oferece ID adequado.

Contextualizar com tenant/provider para evitar colisão.

---

# Inbox

`IntegrationInbox` guarda eventos recebidos e estado de processamento.

Campos possíveis:

```text
organizationId
connectionId
provider
eventId/fingerprint
eventType
schemaVersion
receivedAt
status
attempts
lastError
processedAt
payloadReference/redacted payload
```

Não guardar secret/PII excessiva só por debugging.

Unique constraint ajuda dedupe.

---

# Outbox

Usar quando precisamos garantir que uma transação local e a intenção de publicar evento/efeito externo não se percam.

```text
DB transaction
├── muda domínio
└── grava outbox
commit
→ publisher/worker
→ external effect
```

Não adicionar outbox para todo side effect sem necessidade.

---

# Retries

Retry somente quando:

- falha é transitória;
- operação é idempotente ou protegida;
- provider permite;
- há limite.

Estratégia:

- exponential backoff;
- jitter;
- honor `Retry-After`;
- max attempts/window;
- deadline total.

Nunca retry agressivo de erro 4xx permanente.

## Classificação de erro

```text
TRANSIENT
PERMANENT
AUTH
RATE_LIMIT
VALIDATION
CONFLICT
UNKNOWN
```

A política pode decidir ação por classe.

---

# Timeout

Todo HTTP externo possui timeout:

- connect;
- response/read;
- total/deadline.

Timeout não significa necessariamente “não aconteceu”. Para pagamento/fiscal/publicação, consultar status antes de repetir quando efeito externo pode ter sido aceito.

---

# Rate limits

Cada adapter conhece limites do provider ou responde dinamicamente a headers/429.

Aplicar:

- concurrency limit por connection;
- queue;
- pacing;
- coalescing de updates;
- batch quando suportado;
- backpressure.

Não deixar um tenant consumir toda capacidade de integração da plataforma.

---

# Circuit breaker

Só adotar quando medição mostrar benefício. Pode evitar tempestade contra provider indisponível, mas também esconder recuperação se mal configurado.

Precisa de métricas e estado observável.

---

# Dead-letter / estado de falha

Após policy de retry:

```text
FAILED_ACTION_REQUIRED
```

Erro precisa mostrar:

- recurso;
- provider;
- causa sanitizada;
- última tentativa;
- próximo passo;
- possibilidade de replay seguro.

Nunca descartar job sem registro.

---

# Reconciliation

Webhooks não são garantia absoluta de consistência.

Rotinas podem comparar:

- estoque;
- preço;
- listing;
- order status;
- fulfillment;
- invoice;
- payment;
- catalog mappings.

## Reconciliation não mascara problema

Se todo dia corrige milhares de divergências, webhook/sync principal está quebrado. Alertar e tratar causa.

---

# Ordering

Se provider não garante ordem:

- usar version/timestamp quando confiável;
- refetch latest state;
- evitar aplicar evento velho sobre estado novo;
- registrar external updatedAt/version.

Não confiar na ordem da fila local como ordem da origem.

---

# Schema drift

Validação por schema na boundary.

Mudança aditiva desconhecida pode ser tolerada conforme parser. Campo obrigatório alterado deve falhar controladamente.

Registrar:

- provider;
- endpoint/event;
- API/schema version;
- adapter version.

Monitorar erro de parsing como sinal de integração.

---

# Credenciais

`ChannelConnection`/`IntegrationConnection` aponta para referência protegida de credential.

Regras:

- encrypt at rest;
- secret manager quando apropriado;
- scope mínimo;
- refresh token seguro;
- rotação;
- revogação;
- nunca cliente/log.

## Refresh race

Duas workers não devem invalidar token uma da outra. Aplicar single-flight/lock/version conforme provider.

---

# SSRF e callbacks

Se usuário configura URL/webhook futuro:

- validar scheme;
- bloquear ranges internos/metadata endpoints;
- DNS rebinding controls quando necessário;
- redirects limitados;
- timeouts;
- response size limits.

---

# Bulkheads e fairness

Um marketplace/tenant lento não deve bloquear todos.

Separar filas/concurrency por provider/tenant quando volume justificar. Aplicar fairness/quotas.

---

# Observabilidade

Métricas:

- requests;
- success/failure;
- p50/p95 latency;
- 429;
- auth failures;
- queue depth;
- age do job mais velho;
- webhook lag;
- duplicate rate;
- dead jobs;
- reconciliation drift;
- token health.

Logs: correlation ID, tenant reference segura, connection ID, external resource ID, attempt — nunca secret.

---

# Runbook

Cada integração produtiva precisa de procedimentos para:

- provider outage;
- token revogado;
- backlog;
- schema change;
- rate limit;
- webhook desligado;
- drift;
- replay.

---

# Testes

## Unit

- error classification;
- signature;
- mapping;
- payload parser;
- retry policy.

## Integration

- inbox dedupe;
- outbox;
- persistence;
- tenant mapping;
- timeout.

## Contract

Fixtures/sandbox do provider.

## Failure injection

- timeout;
- 500;
- 429;
- duplicate;
- out-of-order;
- malformed JSON;
- token expiry;
- response gigante;
- partial outage.

## Reconciliation test

Criar drift sintético e provar detecção/correção autorizada.

---

# Checklist para novo adapter

- [ ] documentação oficial/link/data;
- [ ] auth/scopes;
- [ ] rate limits;
- [ ] pagination;
- [ ] timeouts;
- [ ] idempotency;
- [ ] webhooks/signature;
- [ ] ordering;
- [ ] retry/backoff;
- [ ] mappings;
- [ ] schema validation;
- [ ] reconciliation;
- [ ] observability;
- [ ] tenant isolation;
- [ ] secrets;
- [ ] sandbox/tests;
- [ ] runbook;
- [ ] deprecation/versioning.

## Relacionados

- [Bling](bling.md)
- [Marketplaces](marketplaces.md)
- [API](../architecture/api-contracts.md)
- [SRE](../operations/sre-aiops.md)
