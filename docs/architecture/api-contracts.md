# Contratos de API

## Direção

A API do Mora Core será HTTP/JSON pragmática, resource-oriented quando adequado, com OpenAPI como contrato executável quando a implementação existir.

## Regras

- GET é safe.
- PUT/DELETE são idempotentes quando utilizados.
- POST usa idempotency key em operações sensíveis/retryable quando necessário.
- PATCH representa alteração parcial com semântica explícita.
- `201 Created` + `Location` para criação quando aplicável.
- `401` autenticação ausente/inválida; `403` autenticado sem permissão.
- `404` pode ser usado para não revelar recurso cross-tenant quando apropriado.
- `409` para conflitos de estado/concorrência.
- `422` para regras semanticamente inválidas quando fizer sentido.
- `429` para limites excedidos.
- erros preferencialmente em `application/problem+json` compatível com RFC 9457.

## Concorrência

Recursos editáveis de forma concorrente podem usar `version`, ETag/If-Match ou optimistic locking. Operações de estoque/venda não dependem de last-write-wins.

## Paginação e budgets

Toda listagem possui limite máximo. Filtros, sort, batch e expansão de relacionamentos precisam de custo limitado. Nenhum endpoint oferece exportação ilimitada síncrona.

## Contratos externos

Payload de marketplace, Bling, fiscal ou IA é input não confiável e deve ser validado por schema na boundary.

## Versionamento

Mudanças aditivas compatíveis não exigem nova versão. Breaking changes exigem estratégia explícita, migration path e contract diff.

## APIs públicas e storefront

Catálogo público deve usar projeção própria, cacheável, sem expor custo, fornecedor, quantidade sensível ou campos administrativos.
