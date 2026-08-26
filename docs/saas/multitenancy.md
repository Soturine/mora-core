# Multi-tenancy e Modelo SaaS

## Fronteira de tenant

`Organization` é a fronteira primária de isolamento. Uma organização pode possuir várias pessoas jurídicas, marcas, lojas, depósitos, websites, integrações e usuários.

```text
Organization
├── LegalEntity
├── Brand
├── Store
├── StockLocation
├── Website
├── ChannelConnection
├── Employee
└── Membership
```

## Estratégia inicial

PostgreSQL compartilhado com `organization_id` explícito nas entidades pertencentes ao cliente, authorization server-side e possibilidade de RLS como defesa em profundidade.

O cliente nunca escolhe livremente o tenant enviando um ID. O contexto válido é derivado da sessão e das memberships.

## Isolamento

Testes obrigatórios:
- usuário A não lê objeto B;
- não altera objeto B por ID conhecido;
- filtros/search/export não vazam cross-tenant;
- background jobs preservam tenant context;
- cache keys incluem tenant quando necessário;
- webhooks/integrations resolvem organização de forma autenticada, não por campo não confiável.

## Membership e escopo

Um usuário pode pertencer a várias organizações. Dentro de cada organização, roles/permissões podem ser escopadas por loja.

Exemplo: `SELLER` na loja A, `CASHIER` na A, sem acesso à B.

## SaaS futuro

Entitlements definem capacidades contratadas; feature flags definem rollout técnico. São conceitos distintos.

## Enterprise

Banco/schema dedicado por cliente grande pode existir no futuro via estratégia de placement, sem contaminar o domínio. Não implementar antecipadamente.

## Offboarding

Cliente deve conseguir exportar dados próprios. Deleção precisa cobrir banco, mídia, derivados, tokens, cache, índices e políticas de backup conforme [data lifecycle](../data/data-lifecycle.md).
