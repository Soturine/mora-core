# Performance, Capacidade e FinOps

> **Status:** princípios e budgets a definir com medições. Não há números de SLA/capacidade aprovados ainda.

## Princípio

**Medir antes de otimizar** e colocar budgets antes de uma superfície crescer sem controle.

Performance do Mora Core precisa considerar:

- experiência no balcão;
- scanner/mobile;
- catálogo público;
- DB;
- jobs;
- imagens;
- marketplaces;
- IA;
- custo por tenant.

---

# Perguntas de performance por jornada

## Busca de produto no PDV/mobile

- quantos produtos/variantes?
- latência aceitável para operador?
- busca por SKU/barcode/nome?
- rede local/4G?

## Finalizar venda

- qual p95 aceitável?
- dependência de payment/fiscal síncrono?
- como contingência funciona?

## Inventário

- scans por minuto?
- offline?
- batch sync?

## Storefront

- LCP/INP/CLS;
- imagens;
- cache/CDN;
- catalog API latency.

## Import

- milhares ou milhões de linhas?
- memória;
- duration;
- progress.

## Marketplace

- quantos listings/orders?
- rate limits;
- stock sync lag.

## IA

- latência tolerável;
- async;
- pixels/tokens;
- custo.

---

# Budgets

Budgets reais serão definidos com dados, mas categorias precisam existir desde cedo:

## HTTP

- request body max;
- response body max;
- pagination max;
- timeout;
- concurrent requests.

## DB

- connection pool;
- query time budget;
- transaction duration;
- lock timeout;
- statement timeout para endpoints apropriados.

## Upload

- bytes/file;
- pixels;
- images/product;
- concurrent uploads.

## Import

- file size;
- rows;
- chunk size;
- concurrent imports/tenant.

## Jobs

- concurrency/provider;
- queue age;
- max attempts;
- execution deadline.

## IA

- token limit;
- image resolution;
- generations/day/month;
- spend cap;
- concurrency.

Os valores não devem ser infinitos por ausência de decisão.

---

# Banco de dados

Investigar primeiro:

- N+1;
- índices ausentes;
- full scans;
- cardinalidade;
- query plans;
- lock contention;
- long transactions;
- pool saturation;
- chatty queries.

## Índices

Orientados por queries reais:

- tenant + SKU;
- tenant + category;
- location + variant;
- sales por período/loja;
- inbox external event;
- listings/provider.

Não indexar toda coluna automaticamente.

## Paginação

Evita response/memory blowups. Cursor para feeds grandes/estáveis quando necessário.

## Analytics

Agregados/materialized views podem ser usados quando queries transacionais ficarem caras. Data warehouse só entra com volume/necessidade.

---

# Caching

## Bons candidatos

- storefront assets;
- catálogo público;
- categorias;
- static configuration.

## Cuidado

- preço;
- disponibilidade;
- permissions;
- tenant-private data.

Cache de estoque precisa de freshness/invalidation explícito. Não esconder stale data.

## CDN

Para mídia pública e storefront assets.

Signed URLs/authorized delivery para privado.

---

# Imagens

Pipeline assíncrono:

```text
original
→ resize
→ thumbnail
→ WebP/AVIF
→ CDN
```

Storefront:

- responsive `srcset`;
- lazy load secundário;
- main image priority;
- dimensões para CLS;
- não baixar original 10 MB para card.

## AI image cost

Meter:

- pixels;
- generations;
- retries;
- provider;
- derived storage.

---

# Jobs e backpressure

Queue sem limite pode transformar outage de provider em milhões de jobs.

Aplicar:

- concurrency limit;
- rate-aware workers;
- coalescing de stock updates;
- bounded retries;
- dead-letter;
- tenant fairness;
- max queue age/alerts.

---

# Integrações

Cada provider tem throughput diferente. Não usar global concurrency infinito.

Exemplo Bling snapshot 2026: documentação oficial indica 3 req/s e 120k/dia por conta. Worker precisa respeitar isso.

## Stock updates

Pode coalescer:

```text
stock changed 5x em 2s
→ publish latest effective quantity
```

quando semântica do canal permitir.

---

# Mobile

Objetivos:

- app inicia rápido;
- scanner responde sem lag;
- lista não carrega catálogo inteiro;
- uploads em background;
- imagens comprimidas apropriadamente;
- offline DB indexado;
- sync incremental.

Testar aparelho médio, não apenas flagship.

---

# Storefront / Web Vitals

Quando site estiver produtivo, usar metas baseadas em Core Web Vitals e negócio, não obsessão por score sintético.

Medir:

- LCP;
- INP;
- CLS;
- JS bytes;
- image bytes;
- TTFB;
- cache hit.

---

# Escalabilidade

Ordem preferida:

1. corrigir algoritmo/query;
2. ajustar índice/pool;
3. cache/CDN;
4. scale vertical;
5. workers horizontais;
6. read replicas;
7. infra especializada;
8. separar serviço somente com evidência.

Não microservices-first.

---

# Capacity planning

Antes de SaaS amplo, estimar por tenant e total:

- organizações;
- usuários ativos;
- lojas;
- produtos;
- variantes;
- movimentos/dia;
- vendas/dia;
- imagens;
- storage;
- orders/webhooks;
- AI usage.

Usar percentis, não “cliente médio” apenas.

## Headroom

Planejar margem para pico (datas sazonais, Black Friday etc.) sem comprar capacidade excessiva o ano todo.

---

# Load/performance testing

Testar cenários realistas:

- product search concurrent;
- sale completion;
- stock updates;
- import 100k rows;
- webhook storm;
- marketplace outage backlog;
- media batch.

Métrica de teste inclui erros e integridade, não só requests/sec.

---

# FinOps

Custos relevantes:

- compute;
- PostgreSQL;
- backup;
- object storage;
- CDN/egress;
- observability;
- CI;
- email/push;
- marketplaces/providers;
- fiscal;
- IA.

## Unit economics SaaS

Futuro:

```text
revenue/tenant
- infra shared allocation
- storage/egress
- AI
- support
- provider variable cost
= contribution margin
```

Não oferecer feature de IA ilimitada sem conhecer custo.

## Cost attribution

Tag/metric por tenant/capability quando viável, especialmente IA e storage.

## Anomalia

AIOps pode alertar aumento de custo; bloqueio automático exige policy para não interromper operação indevidamente.

---

# Performance regressions

CI/milestone pode comparar budgets selecionados. Produção usa telemetry por release SHA.

Não bloquear commit por microbenchmark instável.

---

# Anti-patterns

- otimização antes de profiling;
- cache sem invalidation;
- query sem paginação;
- `SELECT *` gigante;
- processar IA na request;
- imagem original em thumbnail;
- unlimited batch;
- unlimited retry;
- Redis “porque todo SaaS usa”;
- read replica antes de otimizar query.

## Relacionados

- [SRE](sre-aiops.md)
- [DevSecOps](../devops/devsecops.md)
- [API](../architecture/api-contracts.md)
- [Billing](../saas/billing-entitlements.md)
