# Estratégia de Integração com Bling

> **Status:** estratégia de transição e requisitos. Revalidado com documentação pública oficial em 26/08/2026; contratos devem ser revistos novamente antes da implementação.

## Contexto

A operação inicial já utiliza Bling. Substituir imediatamente um ERP que participa de estoque/fiscal/vendas aumentaria risco desnecessário.

A direção é:

> **integrar primeiro, aprender e reconciliar; substituir somente capacidades validadas.**

## Fase A — Bling como fonte operacional

```text
Bling REST + Webhooks
        ↓
Webhook Receiver
        ↓
verify HMAC
        ↓
persist eventId / inbox
        ↓
ack rápido
        ↓
worker
        ↓
refetch estado canônico quando necessário
        ↓
Mora Core PostgreSQL
        ↓
Catalog API
        ↓
sites
```

## API oficial — snapshot 26/08/2026

Documentação pública atual:

- API/guia: https://developer.bling.com.br/
- webhooks: https://developer.bling.com.br/webhooks
- limites: https://developer.bling.com.br/limites

### Autorização

A API usa fluxo de aplicativo/OAuth2. `client_id`, `client_secret`, access/refresh tokens são secrets backend.

### Limites confirmados

Na documentação oficial consultada:

- 3 requisições por segundo por conta;
- 120.000 requisições por dia;
- páginas tipicamente até 100 registros conforme FAQ;
- `429` quando limite é atingido;
- há regras adicionais de bloqueio por IP.

Esses números são snapshot, não constante eterna. Adapter precisa tratar rate limits e documentação deve ser revalidada antes do go-live.

## Webhooks oficiais — comportamentos críticos

A documentação atual especifica:

- autenticação com `X-Bling-Signature-256` usando HMAC e client secret;
- mensagens duplicadas precisam ser tratadas de forma idempotente;
- entrega pode ocorrer fora de ordem;
- processamento assíncrono é recomendado;
- há retentativas por até 3 dias em falhas, com possibilidade de desabilitar a configuração após falhas prolongadas.

Isso justifica inbox/dedupe/reconciliation.

## Verificação de assinatura

Fluxo:

```text
raw payload
+ client secret
→ HMAC SHA-256
→ constant-time comparison
→ aceitar/rejeitar
```

Não parsear/modificar o body antes de calcular a assinatura se o contrato usar payload original.

Secret nunca vai para log.

## Dedupe

Persistir event ID quando fornecido ou fingerprint robusto conforme versão do webhook.

Unique constraint impede processamento lógico duplicado.

Responder 2xx a evento duplicado válido conforme contrato, sem repetir efeitos.

## Out-of-order

Não confiar que `created` chega antes de `updated`.

Estratégia preferida:

```text
webhook = sinal de mudança
→ worker busca recurso atual pela API
→ aplica estado mais novo usando version/timestamps/policy
```

Quando payload for suficiente, ainda assim controlar ordering/version.

## Rate limit e refetch

Refetch indiscriminado pode estourar 3 req/s. Worker precisa:

- fila por conta;
- concurrency limit;
- backoff;
- coalescing quando múltiplos eventos do mesmo recurso chegarem;
- pagination;
- cache/mapping apropriado;
- reconciliation em ritmo controlado.

## Mapeamento canônico

Nunca usar apenas nome.

```text
Bling externalProductId
↔ Mora Product

Bling variationId
↔ Mora ProductVariant

Bling depositoId
↔ Mora StockLocation
```

Mappings carregam tenant/account e provenance.

## Produtos/variantes

Validar no Discovery:

- como os produtos atuais estão cadastrados;
- parent/variation;
- SKU;
- GTIN;
- categorias;
- fotos;
- preços;
- status;
- depósitos.

Importar “como está” sem auditoria pode trazer dívida de dados para o Core.

## Estoque

Durante fase em que Bling é source of truth, Mora Core precisa declarar claramente se saldo é:

- espelho;
- projection;
- reconciliado;
- read-only.

Não permitir writes concorrentes em duas fontes sem estratégia definida.

## Fluxo de transição

### Fase A — leitura/sync

- Bling manda/fornece dados;
- Core espelha;
- sites consomem Core;
- nenhuma operação crítica escreve de volta sem design.

### Fase B — admin Core assistido

- alguns cadastros no Core;
- write-through controlado para Bling;
- reconciliation;
- Bling continua fiscal/PDV.

### Fase C — Core source of truth

Somente após estabilidade:

```text
Mora Core DB
→ adapters
→ Bling/fiscal/canais
```

### Fase D — PDV paralelo/shadow

Comparar resultados antes de substituir.

### Fase E — retirada seletiva

Só após período prolongado de operação estável, fallback e validação fiscal.

## Webhook receiver

Requisitos:

- HTTPS;
- body budget;
- HMAC;
- dedupe;
- tenant/account mapping seguro;
- persist antes de ack quando durability exigir;
- responder rápido;
- sem trabalho pesado na request;
- métricas.

## Reconciliation

Jobs periódicos comparam:

- produto;
- variante;
- preço;
- estoque;
- publicação/status;
- documentos/pedidos conforme integração.

Drift fica visível. Reconciliation não substitui corrigir webhook quebrado.

## OAuth/token lifecycle

- encrypted credential storage;
- refresh controlado;
- single-flight/lock para evitar refresh race;
- scopes mínimos;
- revogação;
- health state;
- alertas quando reconexão manual for necessária.

## Falhas

Tratar:

- 401/expired token;
- 403/scope;
- 404 mapping stale;
- 429;
- 5xx;
- timeout;
- schema change;
- payload inválido;
- webhook desabilitado;
- reconciliation drift.

## Observabilidade

Por connection/tenant:

- last webhook;
- signature failures;
- duplicate rate;
- processing lag;
- API requests/rate-limit headroom quando disponível;
- last successful sync;
- reconciliation mismatch;
- token health;
- dead jobs.

Não logar access token/client secret.

## Testes

- HMAC válido/inválido;
- duplicate;
- out-of-order;
- 429/backoff;
- token expiry;
- refresh race;
- pagination;
- mapping;
- schema drift;
- tenant mismatch;
- reconciliation;
- replay seguro.

## Discovery obrigatório antes de codificar

Auditar em tela/processo real:

- cadastro;
- grade/variantes;
- XML/compra;
- estoque/depósitos;
- preços;
- venda;
- pagamentos;
- NFC-e/NF-e;
- troca/devolução;
- caixa;
- comissão;
- relatórios;
- CNPJs;
- usuários/permissões;
- hardware;
- integrações atuais.

## Relacionados

- [Discovery](../discovery/operational-discovery.md)
- [Confiabilidade](reliability-patterns.md)
- [Estoque](../domain/inventory.md)
- [Fiscal](../domain/fiscal-and-payments.md)
