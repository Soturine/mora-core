# Roadmap do Mora Core

> **Status:** roadmap orientado a capacidades/evidência, não compromisso de datas. A ordem pode mudar após Discovery e piloto.

## Princípio

A visão completa é ampla, mas o produto deve crescer em camadas. Uma fase só libera a próxima quando os fundamentos relevantes estiverem suficientemente estáveis.

Cada fase possui:

- objetivo;
- capacidades;
- não objetivos;
- evidências/gates;
- dependências.

---

# Fase 0 — Discovery e baseline

## Objetivo

Entender a operação real antes de congelar arquitetura/comportamento.

## Entregas

- mapa `as-is`;
- processo desejado `to-be`;
- topologia Organization/LegalEntity/Brand/Store/StockLocation;
- catálogo real/variantes;
- ownership atual por capability entre NOOVA/Lips/Bling e outros sistemas;
- recebimento/compras;
- venda/caixa;
- comissão;
- troca/devolução;
- fiscal/pagamentos;
- hardware/conectividade;
- matriz de papéis/permissões;
- dados a migrar;
- riscos;
- ADRs iniciais;
- critérios Fase 1.

## Evidência já coletada em 05/09/2026

O baseline de campo confirmou:

- NOOVA na loja feminina/familiar;
- Lips Control na loja masculina;
- duas estações Windows com perfis de hardware diferentes;
- dois scanners e duas impressoras térmicas diferentes;
- fluxos reais de venda/caixa/retaguarda observados no NOOVA;
- sinais concretos de dívida de dados no catálogo legado.

Ver [Baseline real de PDV e hardware](../discovery/mora-pos-hardware-baseline-2026-09-05.md).

Isso **não fecha** o Discovery: ainda faltam drivers/portas, rede, TEF/Pix, fiscal ownership, APIs/exports, CNPJs, política de comissão, trocas, estoque e dados de migração.

## Gate

Questões críticas abertas estão respondidas ou explicitamente adiadas com risco aceito.

Ver [Discovery Operacional](../discovery/operational-discovery.md), [baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md) e [riscos/questões abertas](risks-and-open-questions.md).

---

# Fase 1 — Foundation

## Objetivo

Criar o núcleo autoritativo e seguro.

## Capacidades

- identidade/auth baseline;
- Organization/Store/StockLocation;
- memberships/RBAC;
- audit;
- catálogo;
- taxonomia;
- ProductVariant/SKU;
- migrations;
- inventory ledger/balance;
- API baseline;
- observabilidade;
- testes/CI.

## Não objetivos

- PDV completo;
- fiscal próprio;
- marketplace produtivo;
- IA produtiva;
- billing SaaS.

## Gate

- tenant isolation adversarial testado;
- estoque concorrente consistente;
- migrations fresh + upgrade;
- API contract;
- backup strategy;
- CI no SHA exato;
- docs atuais.

---

# Fase 2 — Mobile operacional

## Objetivo

Acelerar entrada/consulta/inventário usando câmera e mobilidade.

## Capacidades

- login/context;
- product lookup;
- scanner;
- cadastro de produto;
- grade de variantes;
- fotos;
- internal barcode/label;
- recebimento;
- inventário;
- transferências;
- dashboards por role;
- offline limitado/outbox;
- uploads resilientes.

## Gate

- testes em dispositivos reais;
- scan conhecido/desconhecido;
- retry sem duplicar estoque;
- câmera negada com fallback;
- offline conflict strategy;
- mobile security.

---

# Fase 3 — Integração dos sites

## Objetivo

Remover dados estáticos/manuais dos sites e transformar Mora Core na fonte do catálogo público.

## Capacidades

- Catalog API pública;
- categorias dinâmicas;
- produto individual `/produto/$slug`;
- variantes;
- mídia por variante;
- disponibilidade;
- Novidades;
- Destaques;
- Mais Vendidos apenas quando houver vendas reais;
- SEO;
- zoom/viewer;
- redirects históricos.

## Gate

- nenhum dado demo fingindo preço/estoque real;
- performance/accessibility;
- deep-links;
- cache/freshness;
- publicação/arquivamento.

---

# Fase 4 — Sales / POS / Caixa / Comissão

## Objetivo

Operar vendas físicas com integridade, auditabilidade e compatibilidade comprovada com a operação real.

## Capacidades

- Sale/SaleItem;
- seller/cashier;
- pagamentos;
- desconto/approval;
- conclusão idempotente;
- devolução/troca/cancelamento;
- CashRegister/CashSession;
- sangria/suprimento;
- fechamento/blind close;
- CommissionPlan/Entry/Statement;
- relatórios operacionais;
- cliente POS;
- scanner/barcode input;
- receipt/reprint;
- integração de gaveta quando aplicável;
- health/diagnóstico de periféricos;
- Local Device Bridge ou alternativa somente após ADR/POC;
- modo degradado/fallback explicitamente definido.

## Hardware

O primeiro piloto deve passar pela matriz física documentada em [Plano de testes do POS](../qa/pos-hardware-test-plan.md):

- loja feminina/familiar: Core i3-3240/6 GB, YHD-8200, Bematech MP-4200 TH;
- loja masculina: Celeron N3350/6 GB, Knup KP-1026, VERDE DYJ-5801.

Esses equipamentos são baseline de migração/shadow, não requisito mínimo permanente do produto.

## Fiscal

Continuar inicialmente via estratégia de menor risco/provedor existente até adapter e contingência validados.

Impressora térmica não é autoridade fiscal.

## Migração/shadow

Antes do cutover:

```text
legado em produção
→ Mora POS/Core em shadow
→ comparar itens/totais/estoque/caixa/pagamento/fiscal
→ corrigir gaps
→ piloto controlado
→ cutover com fallback
```

Ver [Migração dos legados Mora](../data/mora-legacy-data-migration.md).

## Gate

- última unidade concorrente;
- duplicate payment/sale protection;
- caixa reconciliável;
- comissão com reversão;
- política real validada;
- ownership NOOVA/Lips/Bling/fiscal/payment definido;
- scanners e impressoras das duas lojas testados;
- reprint sem duplicar venda;
- disconnect/reconnect e sem papel tratados;
- performance medida nas duas estações;
- cliente/bridge update e rollback testados se aplicável;
- piloto/shadow contra operação atual;
- recovery/fallback.

---

# Fase 5 — Analytics operacional

## Objetivo

Transformar transações reais em decisões.

## Capacidades

- vendas por loja/canal/vendedor;
- ticket médio;
- unidades;
- mais vendidos;
- estoque baixo;
- giro;
- sell-through;
- aging;
- curva ABC;
- comissão;
- caixa/divergências;
- margem quando custo confiável.

## Gate

Cada métrica possui definição/fonte/filtros/freshness e reconciliação com transações.

---

# Fase 6 — IA assistida

## Objetivo

Reduzir trabalho de cadastro/conteúdo sem comprometer Product Truth.

## Capacidades

- AI Gateway;
- descrição/título/tags;
- classificação;
- background removal;
- image enhancement;
- modelo virtual;
- conteúdo por canal;
- evals;
- provenance;
- quotas/metering;
- human review.

## Gate

- golden dataset;
- fidelity/hallucination evals;
- fail-safe manual flow;
- cost budget;
- provider/security review;
- marketplace compliance rules.

---

# Fase 7 — Omnichannel / PIM / OMS

## Objetivo

Publicar e receber pedidos de canais externos mantendo estoque central coerente.

## Ordem recomendada

Não ativar TikTok + Mercado Livre + Shopee em produção simultaneamente no primeiro dia. Implementar um adapter, estabilizar padrão, depois ampliar.

## Capacidades

- ChannelConnection;
- ChannelListing;
- compliance engine;
- publish/update;
- stock/price sync;
- order ingest;
- reservation;
- fiscal integration;
- fulfillment;
- returns;
- webhooks;
- reconciliation;
- runbooks.

## Gate

- sandbox/contract tests;
- duplicate/out-of-order;
- rate limit;
- token lifecycle;
- overselling test;
- drift detection;
- incident/replay.

---

# Fase 8 — SaaS self-service

## Objetivo

Permitir entrada de novos lojistas com segurança e pouco trabalho manual do time Mora Core.

## Capacidades

- onboarding;
- import/migration;
- tenant tooling;
- website builder;
- custom domain;
- billing;
- entitlements;
- quotas;
- support tooling;
- export/offboarding;
- privacy/legal flows;
- per-tenant observability/cost;
- device/workstation onboarding quando o cliente usar POS local.

## Gate

- tenant isolation audit;
- billing reconciliation;
- onboarding idempotente;
- backup/restore exercise;
- RPO/RTO definidos;
- privacy/legal review;
- support/incident process;
- external pilot;
- hardware support matrix e política de versões quando POS fizer parte do plano SaaS.

---

# Fase 9 — Escala e especialização

Só após dados reais.

Possibilidades:

- forecasting/reorder;
- CRM/fidelidade;
- commissions avançadas;
- mais marketplaces;
- RFID;
- advanced warehouse;
- data warehouse;
- enterprise isolation;
- extração de serviços específicos.

Nada disso é promessa.

---

# Gates transversais

Toda fase/milestone importante revisa:

## Produto

- problema/acceptance criteria;
- feedback de usuário;
- métricas.

## Engenharia

- arquitetura/contracts;
- tests;
- security;
- migrations;
- performance;
- observability.

## Operação

- deploy/rollback;
- runbook;
- backup/recovery;
- support;
- compatibilidade de hardware/driver quando a mudança toca POS.

## Documentação

- README/docs atuais;
- ADRs;
- riscos;
- rastreabilidade;
- baseline de campo atualizado quando hardware/processo mudar.

## Git/Release

- commits lógicos;
- remote push;
- CI verde no SHA;
- artifact validado.

---

# Kanban leve

```text
Backlog
→ Ready
→ In Progress
→ Remote Validation
→ Done
```

WIP baixo. Item não entra em `Ready` sem requisitos mínimos.

## Definition of Ready

- objetivo;
- usuário;
- regras/invariantes;
- acceptance criteria;
- dependências;
- risco principal.

## Definition of Done

- comportamento;
- testes;
- docs;
- segurança;
- observabilidade;
- push/CI;
- validação correspondente.

Para POS/hardware, DoD inclui evidência em dispositivo/estação alvo quando a claim depender disso.

---

# Métricas de evolução

Evitar “% do roadmap concluído” como única métrica.

Acompanhar:

- CUJs validadas;
- defects escaped;
- deployment lead time;
- rollback/incidents;
- time saved na loja;
- data quality;
- inventory accuracy;
- onboarding time;
- support burden;
- scan-to-item/finalização/print latency no POS quando aplicável;
- taxa de falha/recovery de periféricos.

## Relacionados

- [Visão](../product/vision.md)
- [Escopo](../product/scope-and-non-goals.md)
- [Baseline real das lojas](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [POS/periféricos](../architecture/pos-device-integration.md)
- [Migração dos legados](../data/mora-legacy-data-migration.md)
- [Plano de testes do POS](../qa/pos-hardware-test-plan.md)
- [Riscos](risks-and-open-questions.md)
- [Cobertura documental](documentation-coverage.md)
