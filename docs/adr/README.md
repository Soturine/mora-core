# Architecture Decision Records (ADRs)

> **Status:** processo de decisão. ADRs concretos serão adicionados à medida que escolhas estruturais forem realmente feitas.

## Objetivo

Registrar **por que** uma decisão arquitetural foi tomada, quais alternativas foram consideradas, quais consequências aceitamos e quando revisá-la.

ADR não é documentação de tudo. Use para decisões difíceis/custosas de reverter ou que afetam vários módulos.

## Convenção de arquivo

```text
0001-use-postgresql.md
0002-backend-framework.md
0003-auth-strategy.md
```

Estados:

- `Proposto`;
- `Aceito`;
- `Substituído`;
- `Rejeitado`;
- `Deprecated` quando aplicável.

Não renumerar ADRs publicados.

## Template

```markdown
# ADR-NNNN — Título

- Status: Proposto
- Data: YYYY-MM-DD
- Decisores:
- Relacionados:

## Contexto
Que problema/forças existem?

## Drivers
- requisito
- risco
- restrição

## Opções consideradas
### Opção A
Prós/contras.

### Opção B
Prós/contras.

## Decisão
O que escolhemos e por quê.

## Consequências
### Positivas
### Negativas / dívida deliberada

## Segurança / dados

## Operação / custo

## Validação
Como saberemos se a decisão funcionou?

## Revisit trigger
Quando reavaliar?
```

## ADRs iniciais necessários

### ADR-0001 — PostgreSQL como banco transacional

Validar:

- transactions/constraints;
- multi-tenancy;
- managed provider;
- backup/PITR;
- migrations;
- custo.

### ADR-0002 — Backend/framework

Comparar opções como Fastify, NestJS ou alternativa considerando:

- modularidade;
- DX;
- performance real;
- OpenAPI/validation;
- observability;
- dependency footprint;
- team familiarity.

### ADR-0003 — ORM/query layer

Drivers:

- migrations;
- SQL control;
- transactions;
- type safety;
- RLS;
- performance/debug.

### ADR-0004 — Autenticação

Auth própria vs provider gerenciado/híbrido; sessions, MFA/passkeys, mobile/web/POS, tenant memberships.

### ADR-0005 — Estratégia multi-tenant/RLS

DB compartilhado, tenant scoping, RLS defense-in-depth e possível future tenant placement.

### ADR-0006 — Object storage/CDN

Originais/derivados, signed URLs, lifecycle, custo/egress.

### ADR-0007 — Jobs/queue

Começar PostgreSQL-backed vs provider de fila; requirements de throughput/retry/scheduling.

### ADR-0008 — Mobile stack

React Native + Expo após spike de câmera, barcode, SQLite, secure storage, printing e distribution.

### ADR-0009 — Fiscal provider

Bling vs provider especializado e estratégia de contingência.

### ADR-0010 — Cloud/deployment

Managed services, região, custo, IaC, observability, deploy/rollback.

### ADR-0011 — Monorepo/repo strategy

Mora Core apps/packages e sites existentes; não migrar por estética.

### ADR-0012 — Dinheiro em API/domain

Centavos integer vs decimal type/string e boundaries.

### ADR-0013 — IDs

UUID/ULID/etc e slugs públicos.

### ADR-0014 — Offline mobile

SQLite/outbox, conflict model, encryption/lifecycle.

### ADR-0015 — AI provider architecture

AI Gateway, provider selection/routing, privacy, evals/cost.

## Decisões que NÃO precisam de ADR automaticamente

- renomear componente local;
- pequena refatoração;
- bugfix;
- biblioteca trivial substituível.

Pode haver ADR se a decisão tiver impacto estrutural apesar de parecer técnica.

## Evidência

ADR aceito não significa “verdade eterna”. Se dados contradisserem a decisão:

1. criar novo ADR;
2. referenciar o antigo;
3. explicar mudança;
4. migration plan.

Não editar história para parecer que sempre soubemos.

## Links

ADRs devem linkar para docs, issues/specs, benchmark/POC e métricas relevantes.

## Review

Decisões de alto impacto pedem second reviewer/auditoria proporcional, especialmente:

- auth;
- tenancy;
- fiscal;
- payments;
- data deletion;
- infra/recovery.

## Relacionados

- [Arquitetura](../architecture/system-architecture.md)
- [Riscos](../roadmap/risks-and-open-questions.md)
- [Engineering Constitution](../engineering/constitution.md)
