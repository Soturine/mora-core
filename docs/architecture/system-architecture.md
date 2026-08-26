# Arquitetura do Sistema

> **Status:** arquitetura de referência. Provedores e frameworks concretos ainda exigem ADR.

## Objetivo

Definir uma arquitetura que:

- suporte operação real de varejo;
- preserve invariantes de dinheiro/estoque/permissões;
- permita mobile, web, POS e storefront;
- seja SaaS-ready;
- facilite integração com ERP/marketplaces;
- possa crescer sem microserviços prematuros;
- permaneça testável e operável.

## Estilo arquitetural

O padrão inicial é **monólito modular** com uma aplicação/API e workers, compartilhando banco transacional, contratos e observabilidade, mas mantendo limites de módulos explícitos.

```text
                         CLIENTES
        ┌──────────────┬──────────────┬──────────────┐
        │              │              │              │
    Admin Web       Mora Mobile      POS        Storefront
        │              │              │              │
        └──────────────┴───────┬──────┴──────────────┘
                               │
                         HTTP/API Boundary
                               │
                     Application / Use Cases
                               │
       ┌───────────────────────┼────────────────────────┐
       │                       │                        │
 Identity/Tenancy        ERP/Operação             Commerce
       │                  │   │   │                │   │
       │               Catalog Inventory Sales     PIM OMS
       │                  │      Cash/Commission       │
       └───────────────────────┼────────────────────────┘
                               │
                  PostgreSQL / Transactions
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
 Object Storage            Jobs/Workers        External Adapters
        │                      │             Bling/Marketplaces/
      Media/AI                │             Fiscal/Payments/AI
```

## Núcleo de autoridade

### Backend/domain é autoridade para

- autorização;
- tenant scope;
- saldo/estoque;
- venda;
- caixa;
- comissão;
- preço publicado autorizado;
- lifecycle de produto/listing;
- idempotência;
- audit.

Clientes podem coletar input e apresentar estado, mas não tornam uma operação válida apenas porque a UI permitiu.

## Camadas

### Domain

Entidades, value objects, state machines, invariantes e policies independentes de HTTP/framework/provedor.

Exemplos:

- `ProductVariant` válida;
- cálculo de `available`;
- transição de venda;
- commission policy;
- cash session state.

### Application

Casos de uso e orquestração:

- valida authorization;
- abre transação;
- chama módulos/ports;
- persiste;
- emite evento interno/outbox quando necessário;
- retorna resultado.

### Infrastructure

Implementa ports:

- PostgreSQL;
- object storage;
- queue/job runner;
- HTTP clients;
- Bling;
- marketplaces;
- fiscal;
- AI;
- email/push;
- observability.

### Interface/Delivery

- REST/HTTP;
- background consumers;
- admin web;
- mobile;
- POS;
- storefront.

## Processos executáveis

Uma evolução possível:

```text
api       → requests síncronos
worker    → jobs, webhooks, media, reconciliation
web       → admin/storefront quando necessário
mobile    → app separado consumindo API
pos       → cliente POS
```

Isso não significa serviços independentes. API e worker podem fazer parte do mesmo deploy/artifact inicialmente.

## Persistência

### PostgreSQL

Preferência arquitetural atual devido a:

- transações;
- constraints;
- relational integrity;
- concorrência;
- consultas/analytics iniciais;
- maturidade operacional.

Decisão formal deve virar ADR quando stack for congelada.

### Object storage

Mídias não ficam como bytes grandes no banco relacional. Banco mantém metadata/proveniência; storage guarda arquivos.

### Cache

Cache entra quando medição justificar. Catálogo público é forte candidato; autorização/estoque crítico não pode depender de cache stale sem contrato explícito.

### Jobs

Processamento assíncrono para:

- imagens/IA;
- webhooks;
- publicação multicanal;
- reconciliation;
- import/export;
- notificações;
- relatórios pesados.

A tecnologia de queue ainda não foi congelada. PostgreSQL-backed jobs podem ser suficientes no começo.

## Multi-tenancy

`Organization` é boundary primária.

Princípios:

- server resolve tenant permitido;
- query/write é tenant-scoped;
- IDs externos não determinam tenant sozinhos;
- cache/jobs/logs preservam tenant context;
- RLS pode ser defesa adicional;
- testes adversariais cross-tenant são obrigatórios.

Ver [Multi-tenancy](../saas/multitenancy.md).

## Consistência

### Forte/transacional onde necessária

Exemplos:

- finalizar venda + criar movimentos locais coerentes;
- consumir reserva;
- constraint de SKU;
- abertura exclusiva de caixa;
- comissão ligada a venda elegível.

### Eventual quando aceitável

Exemplos:

- propagação para marketplace;
- thumbnails;
- analytics materializado;
- atualização de search index;
- envio de notificações.

Não transformar consistência eventual em desculpa para invariantes locais incorretas.

## Eventos internos

Eventos podem comunicar módulos quando reduzem acoplamento real, por exemplo:

```text
SaleCompleted
→ Inventory
→ Commission
→ Reporting
```

Mas o desenho deve avaliar consistência e failure modes. Uma chamada transacional direta é preferível quando os efeitos precisam ser atômicos.

## Integrações externas

Todas são tratadas como não confiáveis e mutáveis.

Padrão:

```text
Domain/Application
→ Port
→ Adapter
→ External API
```

Inbound:

```text
webhook
→ verify
→ dedupe/inbox
→ ack
→ worker
→ reconcile
```

## API pública vs API administrativa

Storefront não precisa do mesmo payload do admin.

### Public Catalog Projection

Somente:

- dados publicados;
- preço permitido;
- availability status permitido;
- mídia pública;
- SEO.

Nunca:

- custo;
- fornecedor;
- tokens;
- notas internas;
- quantidade sensível sem policy;
- campos administrativos.

## Configuração

Config externa e validada por ambiente. Secrets ficam em secret manager/environment injection segura, nunca em Git/frontend.

Configuração comercial é persistida por tenant, não misturada com environment config.

## Ambientes

```text
local
staging
production
```

Dados e credentials separados. Staging não deve usar credencial produtiva por conveniência.

## Observabilidade

Desde o início:

- logs estruturados;
- request/correlation ID;
- health/readiness;
- métricas básicas;
- error reporting;
- audit separado.

Tracing entra quando fluxos distribuídos justificarem.

## Segurança

Threat model considera:

- cross-tenant;
- privilege escalation;
- upload;
- OAuth tokens;
- webhook spoof/replay;
- race de estoque;
- fraude de caixa/comissão;
- prompt injection;
- supply chain;
- backups.

Ver [Arquitetura de segurança](../security/security-architecture.md).

## Offline

Offline é capability de cliente, não um banco alternativo autoritativo irrestrito.

Mobile pode usar SQLite/outbox para casos permitidos. PDV offline exige design próprio de fiscal, conflitos e reconciliação antes de ser prometido.

## Evolução de escala

Ordem preferida:

1. otimizar consultas/índices;
2. scale up/ajustar pools;
3. cache/CDN onde apropriado;
4. workers horizontais;
5. read replicas/infra especializada quando necessário;
6. extrair módulo somente com pressão real.

Microserviço é consequência de necessidade, não objetivo.

## Failure domains

Mesmo em monólito, limitar impacto:

- timeouts em integrações;
- jobs separados;
- bulkheads/concurrency limits onde necessário;
- media/AI assíncronos;
- circuit breaker apenas quando comportamento medido justificar;
- fallback seguro, nunca dados falsos.

## Estrutura futura possível

```text
apps/
├── api/
├── admin-web/
├── mobile/
└── pos/
packages/
├── domain/
├── contracts/
├── validation/
├── api-client/
└── observability/
infrastructure/
docs/
```

Não é decisão de monorepo já tomada; exige ADR.

## ADRs necessários antes da implementação estrutural

- backend/framework;
- ORM/query layer;
- auth;
- Postgres/provider;
- storage/CDN;
- queue/jobs;
- deployment/cloud;
- mobile stack após spike;
- fiscal provider;
- observability backend.

## Critérios de qualidade arquitetural

- regras críticas testáveis sem UI;
- módulos sem ciclos indevidos;
- integrations substituíveis;
- tenant scope explícito;
- operações idempotentes quando retryable;
- migrations reproduzíveis;
- restore possível;
- deploy observável/rollback;
- sem secrets no cliente;
- sem dependência prematura de distributed systems.

## Relacionados

- [Monólito modular](modular-monolith.md)
- [API](api-contracts.md)
- [Modelo de domínio](../domain/domain-model.md)
- [DevSecOps](../devops/devsecops.md)
- [SRE](../operations/sre-aiops.md)
