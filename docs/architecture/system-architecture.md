# Arquitetura do Sistema

> **Status:** arquitetura de referência. Provedores e frameworks concretos ainda exigem ADR.

## Objetivo

Definir uma arquitetura que:

- suporte operação real de varejo;
- preserve invariantes de dinheiro/estoque/permissões;
- permita mobile, web, POS e storefront;
- integre hardware local sem contaminar o domínio com fabricantes;
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

Para a operação física, o POS pode ter também uma boundary local separada:

```text
Mora POS
   │
   ├── HTTPS → Mora Core API
   │
   └── localhost/IPC → Local Device Bridge? → scanner/impressora/gaveta
```

O `Local Device Bridge` é uma opção arquitetural, ainda sujeita a ADR/POC. Não é um segundo backend de negócio.

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
- payment/fiscal state quando aplicável;
- idempotência;
- audit.

Clientes podem coletar input e apresentar estado, mas não tornam uma operação válida apenas porque a UI permitiu.

Scanner, impressora, browser ou bridge local nunca são autoridade de estoque, pagamento, fiscal ou permissão.

## Camadas

### Domain

Entidades, value objects, state machines, invariantes e policies independentes de HTTP/framework/provedor/dispositivo.

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
- payment/TEF adapters;
- AI;
- email/push;
- observability;
- adapters de dispositivo quando existirem no runtime local apropriado.

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
bridge?   → integração local estreita com periféricos
```

Isso não significa serviços independentes de negócio. API e worker podem fazer parte do mesmo deploy/artifact inicialmente. O bridge, se existir, é um agente local de device I/O, não um microserviço de domínio.

## POS e periféricos locais

O levantamento de campo de 05/09/2026 mostrou que as duas lojas Mora usam hardware e PDVs diferentes. Isso transforma compatibilidade de periféricos em requisito arquitetural real.

### Capabilities

O Core/POS trabalha com capabilities:

```text
barcode_input
receipt_print
label_print
cash_drawer
payment_terminal
```

Não com condicionais de fabricante espalhadas pelo sistema.

### Scanner

Quando confirmado no dispositivo, keyboard-wedge/HID é o caminho inicial preferido por simplicidade. Integração direta com USB/serial só entra quando houver requisito real.

### Impressora

Impressão é efeito posterior/referenciado da operação:

```text
Sale COMPLETED
→ Receipt/PrintableRepresentation
→ PrintJob
→ PrinterAdapter
```

Reimprimir nunca equivale a finalizar a venda novamente.

### Fiscal

A impressora física não autoriza documento fiscal. O fluxo fiscal permanece:

```text
Sale/Order
→ FiscalPort
→ provider/SEFAZ
→ FiscalDocument
→ representação imprimível quando aplicável
```

### Local Device Bridge

Se necessário, deve:

- bindar apenas localmente/IPC por padrão;
- expor command allowlist estreita;
- validar schema;
- autenticar/origin-allowlist conforme desenho;
- operar com least privilege;
- suportar version/health/update seguro;
- nunca expor shell/SQL/filesystem irrestrito/secrets.

WebUSB/Web Serial podem ser avaliados como progressive enhancement, não como única base de operação crítica sem matriz comprovada.

Ver [Integração POS/periféricos](pos-device-integration.md).

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
- envio de notificações;
- impressão/reimpressão quando venda já está concluída e o efeito pode ser repetido de forma segura.

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

Sistemas legados NOOVA/Lips, se possuírem API/export suportado, também devem ficar atrás de adapters de migração/integrations e nunca vazar seu modelo para o domínio canônico.

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

Configuração de dispositivo/estação deve ser identificável e auditável sem transformar `vendor/model` em autorização.

## Ambientes

```text
local
staging
production
```

Dados e credentials separados. Staging não deve usar credencial produtiva por conveniência.

Hardware de teste/homologação deve evitar efeitos produtivos, principalmente pagamento, fiscal e abertura de gaveta.

## Observabilidade

Desde o início:

- logs estruturados;
- request/correlation ID;
- health/readiness;
- métricas básicas;
- error reporting;
- audit separado.

Para POS/local devices, incluir quando aplicável:

- workstation/bridge version;
- device health;
- print failures/unknown;
- scanner failures;
- reconnects;
- latência local.

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
- backups;
- workstation comprometida;
- localhost bridge abusado por origem maliciosa;
- update de cliente/bridge comprometido;
- abertura indevida de gaveta/impressão abusiva.

Ver [Arquitetura de segurança](../security/security-architecture.md).

## Offline

Offline é capability de cliente, não um banco alternativo autoritativo irrestrito.

Mobile pode usar SQLite/outbox para casos permitidos. PDV offline exige design próprio de venda, pagamento, fiscal, conflitos e reconciliação antes de ser prometido.

O fato de o legado oferecer alguma forma de contingência não significa que o Mora Core pode copiar o comportamento sem compreender estados e obrigações.

## Hardware de referência

O baseline inicial real está em [Discovery — PDV e hardware](../discovery/mora-pos-hardware-baseline-2026-09-05.md).

Os computadores atuais são alvos importantes de POC/shadow porque representam a operação existente. Eles **não** definem automaticamente o requisito mínimo permanente do SaaS.

A decisão de runtime POS deve medir CPU, RAM, startup, scan-to-item, impressão, reconnect e update nessas máquinas.

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
- falha da impressora não desfaz venda concluída;
- falha do scanner tem fallback manual;
- bridge ausente tem erro operacional claro;
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

Se houver bridge local, sua localização no monorepo/deploy deve ser decidida por ADR; não é obrigatório colocá-lo dentro do mesmo artifact do POS.

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
- **runtime do cliente POS**;
- **estratégia de Device Bridge/periféricos**;
- **offline/contingência do POS**;
- **payment/TEF adapter**;
- fiscal provider;
- observability backend.

## Critérios de qualidade arquitetural

- regras críticas testáveis sem UI;
- módulos sem ciclos indevidos;
- integrations substituíveis;
- hardware substituível por capability/adapter;
- tenant scope explícito;
- operações idempotentes quando retryable;
- migrations reproduzíveis;
- restore possível;
- deploy observável/rollback;
- sem secrets no cliente;
- sem dependência prematura de distributed systems;
- POS validado em hardware real antes de claim de compatibilidade.

## Relacionados

- [Monólito modular](modular-monolith.md)
- [POS/periféricos](pos-device-integration.md)
- [Baseline de campo](../discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [API](api-contracts.md)
- [Modelo de domínio](../domain/domain-model.md)
- [Migração dos legados Mora](../data/mora-legacy-data-migration.md)
- [DevSecOps](../devops/devsecops.md)
- [SRE](../operations/sre-aiops.md)
