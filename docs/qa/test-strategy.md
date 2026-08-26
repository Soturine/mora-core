# Estratégia de QA, Verificação e Validação

> **Status:** padrão de qualidade para todas as fases. Ferramentas concretas serão definidas junto da stack.

## Objetivo

Provar que o Mora Core faz o que promete, detecta regressões e resiste a cenários reais de varejo, sem confundir “tem testes” com qualidade.

## Verificação x validação

- **Verificação:** construímos corretamente conforme especificação?
- **Validação:** estamos construindo a coisa certa para a operação?

Os dois são necessários.

Discovery, piloto e uso real fazem parte da validação.

## Estratégia baseada em risco

Quanto maior o impacto de falha, maior a profundidade.

### Alto risco

- tenant isolation;
- estoque;
- venda;
- pagamento;
- fiscal;
- caixa;
- comissão;
- migrations;
- backup/restore;
- marketplace order/sync.

### Médio

- catálogo/publicação;
- import/export;
- permissions comuns;
- mobile sync;
- IA com conteúdo comercial.

### Menor

- componentes visuais sem regra;
- páginas informativas.

## Pirâmide pragmática

### Unit

Muitos testes rápidos para:

- value objects;
- policies;
- state transitions;
- dinheiro;
- comissão;
- disponibilidade;
- mappings;
- validation.

### Component/service

Use cases/módulos com dependências controladas.

### Integration real

PostgreSQL e serviços locais reais onde a integração é parte do comportamento.

Exemplos:

- constraints;
- transactions;
- locking;
- migrations;
- repositories;
- inbox/outbox.

Não mockar o banco em testes que pretendem provar concorrência SQL.

### Contract

Adapters/API:

- OpenAPI;
- marketplace fixtures/sandbox;
- Bling payloads;
- fiscal provider;
- AI structured output.

### E2E

Poucos caminhos críticos, não centenas de testes frágeis de UI.

---

# Critical User Journeys

## CUJ-01 — Receber produto

```text
login autorizado
→ produto/variante
→ recebimento
→ movimento
→ balance
```

Provar quantity, audit e retry.

## CUJ-02 — Venda física

```text
sale
→ payment
→ completion
→ estoque -1
→ cash
→ commission
```

Finalização duplicada não duplica efeitos.

## CUJ-03 — Devolução

Estoque, payment, caixa, fiscal e commission consistentes.

## CUJ-04 — Fechamento

Expected vs counted, blind mode, reason/approval.

## CUJ-05 — Inventário mobile offline

```text
scan
→ local persist
→ replay
→ sem duplicação
→ divergência
→ ajuste autorizado
```

## CUJ-06 — Pedido marketplace

```text
webhook
→ dedupe
→ Order
→ reserva
→ fulfillment
→ stock projection
```

## CUJ-07 — Tenant isolation

Tenant A nunca lê/escreve B em API, jobs, cache, export ou storage.

## CUJ-08 — Produto publicado

Catalog → Storefront → variant/media/price verificados.

## CUJ-09 — Backup restore

Restore real + app + invariantes.

---

# Testes de domínio

## Estoque

- último item concorrente;
- reservation;
- transfer retry;
- adjustment;
- count;
- negative policy;
- reconciliation.

## Dinheiro

- centavos/rounding;
- split payment;
- refunds;
- totals;
- currency consistency.

## Comissão

- desconto;
- devolução parcial;
- cancelamento;
- plan version;
- statement;
- reversal idempotente.

## Caixa

- opening exclusivity;
- sangria/suprimento;
- closing;
- blind closing;
- divergência.

---

# Concorrência

Teste determinístico quando possível:

- dois transactions na última unidade;
- duplicate idempotency key;
- parallel token refresh;
- two transfer receivers;
- quota race;
- two cash openings.

Não usar apenas teste sequencial para afirmar race-safety.

## Property-based tests

Candidatos:

- soma de movimentos = balance;
- comissão reversões nunca excedem origem;
- dinheiro conserva total em splits;
- valid state transitions;
- import idempotency.

## Mutation testing

Usar em regras críticas selecionadas para verificar se a suíte detecta alterações como `>` → `>=` ou remoção de check.

Não precisa rodar em toda CI a cada commit.

## Fuzz/adversarial

- parsers;
- API payload;
- CSV/Excel;
- upload;
- webhook;
- URLs;
- AI output.

## Security tests

- BOLA/IDOR;
- role escalation;
- tenant mismatch;
- mass assignment;
- CSRF/XSS/SSRF conforme superfície;
- replay;
- secret redaction;
- malicious upload;
- prompt injection.

## Migration tests

- fresh DB;
- upgrade da versão anterior;
- dados representativos;
- expand/migrate/contract;
- rollback/forward fix conforme estratégia;
- unique/check constraints.

## Recovery tests

- restore PostgreSQL;
- object storage/mídia;
- failed migration;
- replay inbox/outbox;
- integration reconciliation.

## Performance tests

Somente com pergunta/budget:

- product search;
- catalog list;
- sale completion;
- inventory scan burst;
- 100k import;
- webhook backlog;
- image processing.

Medir p50/p95/p99 quando útil, throughput, DB pool e erro.

## Mobile QA

### Dispositivos reais

Câmera, scanner, gestos, offline e upload exigem device real além de emulador.

### Tamanhos

Cobrir 320/360/390/430 e tablets quando suportados.

### Cenários

- câmera negada;
- baixa luz;
- barcode danificado;
- app background;
- rede cai durante upload;
- token expira;
- storage local cheio;
- sync duplicado.

## Storefront QA

- 320→1440+;
- teclado;
- screen reader;
- browser zoom;
- focus;
- product deep-link;
- 404;
- variant invalid;
- demo vs verified;
- image failure;
- SEO metadata;
- performance.

## IA QA

Ver [Governança de IA](../ai/ai-governance.md): eval dataset, hallucination, fidelity, adversarial, model regression.

## Integrações

Failure injection:

- timeout;
- 429;
- 500;
- duplicate;
- out-of-order;
- malformed payload;
- expired token;
- partial outage;
- provider schema change fixture.

## Anti-fake-smoke

Smoke real deve provar comportamento.

Exemplo de backend:

```text
stack sobe
→ migration aplicada
→ cria tenant fixture
→ write/read
→ unauthorized access falha
→ restart
→ dado persiste
```

Apenas `GET /health = 200` não valida o sistema.

## Negative controls

Para testes críticos, introduzir deliberadamente defeito/fixture que deveria falhar ou usar mutation. Se teste continua verde, ele não prova a regra.

## Flakiness

Flaky test é defeito.

- sem sleep arbitrário;
- aguardar condição com timeout;
- clock injectable;
- seeds reproduzíveis;
- isolation;
- cleanup;
- diagnose antes de retry.

## Test data

- factories/fixtures pequenas;
- sem PII real em CI;
- deterministic IDs/time quando necessário;
- datasets de performance separados;
- golden AI dataset autorizado.

## QA manual

Checklist por milestone e evidência:

- cenário;
- ambiente;
- versão/SHA;
- resultado;
- screenshots/logs quando úteis;
- gaps.

Manual não substitui automação repetível, e automação não substitui validação humana de UX/operação.

## Gates

Durante tarefa:

- testes focados;
- lint/typecheck.

Milestone/release:

- suite ampliada;
- integration;
- E2E crítico;
- security;
- migration;
- build;
- smoke;
- manual QA.

## Evidência de release

Relatório precisa dizer:

```text
SHA
commands
resultados
manual QA
security checks
migrations
known risks
deferred/not validated
```

## Critério de aceitação de teste

Teste deve:

- falhar se comportamento quebrar;
- verificar resultado de negócio, não implementação incidental;
- ser determinístico;
- ter mensagem útil;
- não depender de produção.

## Relacionados

- [Engineering Constitution](../engineering/constitution.md)
- [DevSecOps](../devops/devsecops.md)
- [Segurança](../security/security-architecture.md)
- [Discovery](../discovery/operational-discovery.md)
