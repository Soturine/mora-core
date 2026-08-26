# Contratos de API

> **Status:** princípios e convenções. OpenAPI real só existirá junto da implementação.

## Objetivo

A API deve ser previsível, segura, versionável e adequada para admin web, mobile, POS, storefront e integrações, sem expor diretamente o modelo de banco.

## Estilo

HTTP/JSON pragmático, resource-oriented quando fizer sentido. Alvo aproximado de maturidade REST L2+, sem dogma.

## Semântica HTTP

- `GET`: safe; não muda estado comercial.
- `POST`: criação/comandos não idempotentes por natureza, com idempotency key quando retry puder duplicar efeito.
- `PUT`: substituição idempotente quando usado.
- `PATCH`: alteração parcial com contrato explícito.
- `DELETE`: idempotente semanticamente quando aplicável; muitos domínios usarão archive/deactivate em vez de apagar.

Status comuns:

- `200 OK` leitura/ação bem-sucedida;
- `201 Created` + `Location` para criação;
- `202 Accepted` para job assíncrono;
- `204 No Content` quando não há body;
- `400` request estruturalmente inválido;
- `401` não autenticado/token inválido;
- `403` autenticado, mas ação não permitida;
- `404` ausente ou, quando apropriado, usado para não revelar recurso cross-tenant;
- `409` conflito de estado/versão/constraint;
- `412` precondition failed quando ETag/If-Match;
- `422` regra semântica inválida;
- `429` rate/quota;
- `503` dependência/serviço indisponível quando adequado.

## Erros

Preferir `application/problem+json` compatível com RFC 9457.

Exemplo conceitual:

```json
{
  "type": "https://docs.moracore/errors/insufficient-stock",
  "title": "Estoque insuficiente",
  "status": 409,
  "detail": "A variante não possui disponibilidade para concluir a operação.",
  "code": "INVENTORY_INSUFFICIENT",
  "requestId": "..."
}
```

Não retornar stack trace, SQL, secrets ou detalhes internos sensíveis.

## Autenticação e tenant

O cliente não escolhe arbitrariamente a organização enviando `organizationId` e esperando confiança.

O servidor resolve memberships/escopos da sessão. Quando a API precisar de organization/store context, valida explicitamente.

Toda rota privada considera:

```text
authentication
→ membership
→ action permission
→ object authorization
→ store scope
→ state policy
```

## IDs

IDs devem ser estáveis. Não usar índice/posição. Slugs são identificadores de apresentação e podem ter política própria.

IDs públicos não devem facilitar enumeração quando isso aumentar risco; UUID/ULID ou equivalente pode ser considerado via ADR.

## Dinheiro

Contratos nunca usam float binário para valor monetário crítico.

Possíveis representações:

```json
{ "amountCents": 14990, "currency": "BRL" }
```

ou decimal string conforme contrato definido.

Uma única convenção deve ser adotada por API.

## Datas e timezone

- timestamps: ISO 8601 com timezone/UTC;
- datas civis: tipo date quando apropriado;
- regras por loja usam timezone configurado;
- não inferir timezone do dispositivo para fechamento/relatório crítico.

## Paginação

Toda coleção potencialmente grande é paginada.

Preferir cursor quando estabilidade/volume justificar; offset pode ser suficiente em telas simples.

Contrato inclui:

- limite default;
- máximo;
- cursor/next;
- filtros permitidos;
- sort permitido.

Nenhum `GET /sales` pode retornar milhões de registros.

## Filtros e ordenação

Allowlist explícita. Evitar transformar query string em SQL dinâmico arbitrário.

Filtros caros possuem budgets e índices correspondentes.

## Busca

Pesquisa por nome/SKU/barcode pode ter endpoint/índice próprio. Search full-text especializado só entra com evidência.

## Concorrência

Recursos editáveis podem usar:

- `version`;
- optimistic lock;
- ETag + `If-Match`.

Operações críticas de estoque/venda usam transação/constraint/lock conforme invariantes. Nunca depender de last-write-wins.

## Idempotência

Para comandos retryable:

```http
Idempotency-Key: <client-operation-id>
```

O backend associa chave ao tenant/ator/operação, armazena resultado/fingerprint por janela adequada e rejeita reutilização incompatível.

Casos:

- finalizar venda;
- criar pagamento;
- registrar recebimento mobile;
- iniciar import;
- webhook ingest;
- publicação externa quando aplicável.

## Jobs assíncronos

Operações longas retornam `202` com job/resource:

```json
{
  "jobId": "...",
  "status": "QUEUED"
}
```

Status possíveis precisam ser explícitos: `QUEUED`, `RUNNING`, `SUCCEEDED`, `FAILED`, `CANCELLED` quando suportado.

Erro de job é acionável e não desaparece.

## Uploads

Preferir fluxo com limites explícitos e, quando necessário, signed upload URL.

Contrato define:

- MIME aceito;
- bytes máximos;
- dimensões/pixels;
- quantidade;
- lifecycle/status;
- checksum quando útil.

## Storefront/public catalog API

É uma projeção dedicada e cacheável.

Pode expor:

- produto publicado;
- nome/descrição;
- categoria;
- variantes públicas;
- preço verificado;
- availability status;
- mídia;
- SEO.

Não expõe:

- custo;
- margem;
- fornecedor;
- audit;
- quantidade sensível sem política;
- credentials;
- notas internas.

## Admin API

Pode ter mais detalhes, sempre authorization-scoped.

Nunca assumir que “é rota admin” substitui object authorization.

## Mobile

API suporta operações com `operationId` para outbox/offline. Replays precisam ser seguros.

Payloads mobile não podem determinar estoque final localmente sem validação do servidor.

## Integrações externas

Adapters validam payload externo com schema (ex.: Zod/JSON Schema ou equivalente) antes de converter para domínio.

Registrar provider/version e tolerar campos aditivos conforme contrato.

## Webhooks recebidos

Endpoint deve:

- limitar body;
- verificar assinatura/autenticidade;
- prevenir replay conforme provider;
- persistir event/fingerprint;
- retornar rápido;
- processar assíncrono;
- deduplicar.

## Webhooks próprios futuros

Se Mora Core oferecer webhooks SaaS:

- assinatura HMAC;
- event ID;
- versionamento;
- retry policy;
- delivery log;
- secret rotation;
- destination validation/SSRF controls;
- tenant isolation.

## Rate limiting e quotas

Separar:

- proteção técnica contra abuso;
- quota comercial/entitlement.

Rate limits podem variar por endpoint/identidade/tenant. Responder `429` com retry guidance quando possível.

## Caching

Usar headers HTTP quando apropriado:

- `Cache-Control`;
- `ETag`;
- conditional GET.

Dados críticos de disponibilidade precisam de freshness contract.

## Versionamento

Princípio:

- mudança aditiva compatível → mesma versão;
- breaking → versão/estratégia explícita;
- deprecation com prazo e telemetry;
- contract diff em CI quando OpenAPI existir.

Não versionar por hábito a cada release.

## OpenAPI

Quando endpoints existirem:

- schema gerado/validado;
- examples;
- security schemes;
- errors;
- pagination;
- idempotency;
- deprecation;
- SDK/api-client derivados quando útil.

Contrato deve ser testado contra servidor real.

## Segurança de API

Referências:

- OWASP API Security Top 10;
- ASVS conforme risco;
- least privilege;
- BOLA/IDOR adversarial tests;
- SSRF protections para URLs externas;
- body/response size budgets;
- timeouts;
- safe serialization;
- no arbitrary HTML.

## Observabilidade

Cada request gera/carreia correlation ID. Métricas por route/operation, mas tenant ID em logs deve ser tratado de forma segura e nunca usado para expor dados.

## Critérios de aceitação para endpoint novo

- contrato documentado;
- auth/authz;
- tenant isolation;
- validação;
- erros;
- idempotência/concorrência quando aplicável;
- budgets;
- teste unit/integration/contract;
- observabilidade;
- documentação atual.

## Relacionados

- [Arquitetura](system-architecture.md)
- [Segurança](../security/security-architecture.md)
- [Confiabilidade de integrações](../integrations/reliability-patterns.md)
