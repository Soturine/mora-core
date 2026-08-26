# SRE, Observabilidade e AIOps

> **Status:** baseline operacional. SLOs numéricos só serão definidos com objetivos reais de negócio e dados de produção.

## Objetivo

Permitir responder rapidamente:

- o sistema está saudável?
- qual jornada está quebrada?
- quais tenants/canais são afetados?
- desde qual release?
- qual impacto?
- como recuperar com segurança?

Observabilidade serve operação e decisão, não coleção indiscriminada de logs.

---

# Logs estruturados

Campos típicos:

```text
timestamp
level
service/process
module
operation
requestId
correlationId
releaseSha
tenantReference segura
storeReference?
resourceId?
errorCode
latencyMs
```

Nunca logar:

- senha;
- access/refresh token;
- secret;
- CVV/PAN;
- PII completa desnecessária;
- documento fiscal inteiro por padrão;
- prompt/imagem sensível sem finalidade.

## Correlation IDs

Propagar entre:

```text
API
→ DB operation
→ job
→ external adapter
```

External provider IDs também podem ser correlacionados.

---

# Health

## Liveness

`/livez`: processo está vivo e não travado de forma básica.

Não checar todas as dependências e reiniciar loop por uma API externa fora do ar.

## Readiness

`/readyz`: processo pode receber tráfego crítico.

Pode considerar:

- configuração válida;
- DB acessível;
- migrations compatíveis;
- dependências realmente obrigatórias.

Marketplace indisponível não necessariamente tira API inteira de readiness; jobs ficam degradados.

---

# Métricas técnicas

## API

- request rate;
- p50/p95/p99;
- 4xx/5xx;
- saturation;
- response bytes;
- auth failures.

## PostgreSQL

- connections/pool;
- query latency;
- locks;
- deadlocks;
- transaction failures;
- storage;
- replication/PITR health quando existir.

## Jobs

- queue depth;
- oldest age;
- throughput;
- attempts;
- failures/dead-letter;
- processing latency.

## Integrações

- requests;
- 429;
- auth failure;
- webhook lag;
- duplicate rate;
- last success;
- reconciliation drift;
- token health.

## Media/AI

- upload failures;
- processing time;
- image job backlog;
- AI success/invalid output;
- model/provider latency;
- spend/usage por tenant/capability;
- eval regressions em release de modelo.

---

# Business health signals

Não substituem analytics, mas ajudam detectar incidente:

- vendas concluídas/minuto comparadas ao baseline;
- falha de finalização;
- stock movement errors;
- payment ambiguous;
- fiscal rejection spike;
- order backlog;
- publication failures.

Não alertar em receita baixa sem considerar horário/calendário e sem evitar vazamento de dados.

---

# Tracing

Adicionar quando necessário para seguir flows distribuídos:

```text
request
→ DB
→ job
→ marketplace
→ callback
```

Não instrumentar tudo por cerimônia. Sampling e custo precisam ser controlados.

---

# SLIs

Candidatos quando produção existir:

- disponibilidade da API;
- sucesso da finalização de venda;
- latência p95 de product lookup;
- tempo de propagação de stock update;
- processamento de order webhook;
- sucesso de job de publicação;
- disponibilidade de storefront.

## SLO

Definido por janela e objetivo, por exemplo:

```text
SLI: sale_completion_success
SLO: X% / 30 dias
```

Não escolher `99.99%` porque parece profissional.

## Error budget

Quando SLO existir, error budget orienta trade-off entre velocidade e reliability.

---

# Alertas

Um alerta deve:

- indicar impacto;
- ser acionável;
- apontar runbook;
- ter owner/escalation;
- evitar duplicação/ruído.

Tipos:

### Paging/urgente

- cross-tenant suspected;
- sale completion broadly failing;
- DB down;
- payment duplication;
- data corruption;
- storefront widespread outage dependendo SLO.

### Ticket/async

- token expirando;
- reconciliation drift baixo;
- storage crescendo;
- AI cost anomaly não crítica.

---

# Dashboards operacionais

## Platform

- traffic;
- latency;
- errors;
- DB;
- jobs;
- deployments.

## Integrations

Por provider/tenant:

- health;
- lag;
- rate limit;
- last sync;
- dead jobs.

## Commerce

- order backlog;
- stock sync lag;
- listing failures;
- fiscal queue.

## AI

- usage;
- cost;
- latency;
- failure;
- model versions.

---

# Deployment observability

Anotar release SHA em metrics/logs. Após deploy, comparar error/latency e CUJs.

Feature rollout pode ser correlacionado com flags.

---

# Incidentes

Ver [Incidentes e DR](incident-recovery.md).

Durante incidente:

- não perder tempo procurando dashboard inexistente;
- preservar evidence;
- registrar timeline;
- comunicação clara;
- postmortem blameless focado em sistema/controle.

---

# Toil

Automatizar trabalho repetitivo que:

- é manual;
- frequente;
- previsível;
- não gera valor proporcional.

Exemplos futuros:

- reprocessar webhook seguro;
- renovar certificado/token quando possível;
- cleanup de temp assets;
- reconciliation report.

Não automatizar decisão perigosa sem guardrails.

---

# AIOps

IA pode auxiliar:

- sumarizar logs/incidente;
- agrupar stack traces;
- sugerir causa provável;
- correlacionar deploy e erro;
- detectar anomalia;
- sugerir runbook;
- gerar resumo de postmortem.

## Limites

AIOps não recebe autoridade irrestrita para:

- deletar dados;
- executar SQL de escrita em produção;
- rotacionar secret sem workflow;
- derrubar infra;
- fechar incidente;
- alterar firewall/permissions;
- fazer rollback com migration incompatível.

Ações automatizadas exigem policy, dry-run/checkpoint e audit.

## Segurança de AIOps

Logs podem conter input malicioso/prompt injection. Tratar tudo como conteúdo não confiável. O modelo não deve seguir instruções encontradas no log.

---

# Capacity e saturation

Relacionar SRE a [Performance/Capacity](performance-capacity.md). Alertar antes de:

- DB storage esgotar;
- pool saturar;
- queue crescer sem controle;
- object storage/cost explodir;
- AI quota/provider limitar.

---

# Runbooks

Todo alerta de alta severidade deve apontar para procedimento:

- sintomas;
- diagnóstico;
- ações seguras;
- rollback;
- validação;
- escalation.

Ver [Catálogo de runbooks](runbook-catalog.md).

---

# Teste da observabilidade

- correlation atravessa job;
- alert dispara em staging/failure exercise;
- dashboards têm dados;
- redaction funciona;
- health reage corretamente;
- runbook é executável.

Observabilidade não validada é apenas configuração.

## Relacionados

- [Performance](performance-capacity.md)
- [Incidentes](incident-recovery.md)
- [DevSecOps](../devops/devsecops.md)
- [AIOps/IA](../ai/ai-governance.md)
