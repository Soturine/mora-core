# Multi-tenancy e Estrutura Organizacional

> **Status:** arquitetura planejada e requisito estrutural de longo prazo.

## Princípio

O Mora Core deve funcionar hoje para uma organização real e amanhã para múltiplos clientes SaaS sem reescrever o domínio. Isso não significa construir infraestrutura enterprise desde o primeiro dia; significa nunca assumir que existe uma única empresa global.

## Hierarquia de negócio

```text
Platform
└── Organization
    ├── LegalEntity
    ├── Brand
    ├── Store
    ├── StockLocation / Warehouse
    ├── Employees
    ├── Websites
    ├── SalesChannels
    └── Integrations
```

### Organization

Fronteira primária de tenant e ownership.

### LegalEntity

Representa entidade jurídica/CNPJ quando necessário. Organização e pessoa jurídica não são sinônimos: um grupo pode possuir mais de uma entidade legal.

### Brand

Marca comercial que pode possuir site/canais próprios, sem necessariamente equivaler a uma loja física.

### Store

Ponto de operação/venda físico ou unidade administrativa conforme domínio validado.

### StockLocation

Local físico/lógico onde estoque é mantido. Pode ser loja, depósito, backroom ou outra localização. Estoque não deve depender de `Store` se futuramente houver depósitos separados.

## Exemplo atual e futuro

```text
Organization: Mora
├── Brand: Mora Moda
│   └── Store: Feminina
└── Brand: Mora Modas Masculina
    └── Store: Masculina
```

Futuro:

```text
Organization: Grupo XPTO
├── LegalEntity A
├── LegalEntity B
├── Brand XPTO
│   ├── Store Centro
│   └── Store Shopping
├── Brand XPTO Kids
│   └── Store Outlet
└── Warehouse Central
```

## `organization_id` como boundary

Entidades pertencentes ao cliente precisam de ownership explícito. A estratégia exata pode variar por tabela, mas recursos como produto, venda, funcionário, caixa, integração e mídia precisam ter tenant scope inequívoco.

Nenhum endpoint deve confiar em `organizationId` arbitrário enviado pelo cliente.

```text
sessão autenticada
→ memberships válidas
→ organização ativa permitida
→ store/resource scope
→ operação autorizada
```

## Recommendation boundary

Multi-tenancy também é uma **fronteira comercial**, não só de leitura CRUD.

Um cliente conversando com a Organization A jamais deve receber como recomendação um produto, loja, listing ou estoque da Organization B.

Isso vale mesmo quando:

- ambos estão no mesmo banco PostgreSQL;
- ambos usam o mesmo modelo de IA;
- embeddings ficam no mesmo mecanismo de busca;
- um marketplace público contém listings das duas empresas;
- o item de B seria “mais parecido” ou mais barato.

### Invariante

```text
recommendationCandidates.organizationId
==
conversation/website/channelConnection.organizationId
```

### Dentro do tenant

A organização pode decidir se a recomendação procura:

```text
STORE_ONLY
BRAND_ONLY
ORGANIZATION
```

O padrão recomendado é permitir recuperação de venda em outras lojas/marcas **da mesma organização**, com transparência sobre local/fulfillment.

### Vector search / IA

Filtros de tenant não podem ser deixados para “o modelo obedecer ao prompt”. Devem existir em query/index/repository/policy determinística.

Testar especificamente:

- embedding search A nunca retorna B;
- cache de recomendação inclui tenant;
- fallback de busca não remove tenant filter;
- agent tool não aceita `organizationId` arbitrário do LLM.

## Estratégia inicial de banco

Preferência arquitetural:

```text
PostgreSQL compartilhado
+ tenant_id/organization_id
+ autorização application-level
+ constraints/indexes adequados
+ RLS avaliado como defesa em profundidade
```

RLS não substitui autorização na aplicação. Se usado, deve possuir testes explícitos e estratégia segura para migrations/background jobs.

## Não usar um banco por tenant no início

Um DB por cliente adiciona provisionamento, migrations, pooling, backup, observabilidade e custo operacional. Não comprar essa complexidade sem necessidade.

## Evolução futura

A arquitetura não deve impedir placement híbrido:

```text
SMB → banco compartilhado
Enterprise/regulado → schema/DB dedicado
```

Se isso ocorrer, placement é infraestrutura, não regra de negócio.

## Segurança contra cross-tenant

Threats principais:

- BOLA/IDOR por ID de outro tenant;
- cache key sem tenant;
- job assíncrono sem tenant context;
- export incluindo organização errada;
- analytics misturando dados;
- webhook/credential associado ao tenant errado;
- logs administrativos expondo PII de outro cliente;
- busca/autocomplete global involuntário;
- vector search/semantic search cross-tenant;
- recommendation engine sugerindo concorrente;
- CustomerRequest/SourcingRequest atravessando tenant;
- marketplace connection resolvida para conta errada.

## Testes de isolamento

Criar uma fixture com tenants A e B e provar:

- leitura A→B negada;
- escrita A→B negada;
- listagem não contém B;
- filtros adulterados não atravessam tenant;
- background job de A não toca B;
- cache não retorna B;
- upload/signed URL de A não acessa B;
- export A contém somente A;
- recomendação A contém somente A;
- busca por imagem enviada em A não consulta produtos de B;
- ChannelListing de B não é oferecido ao cliente de A;
- administração/support respeita autorização especial auditada.

## Membership multi-organização

Uma identidade pode participar de mais de uma organização no SaaS. Troca de organização é mudança explícita de contexto, não alteração de `organizationId` escondida no formulário.

## Organização com múltiplas marcas/sites

`Website` e `Brand` são entidades separadas de `Store`. Um grupo pode possuir mais de uma marca e mais de um site, com estoque compartilhado ou separado conforme configuração real.

## Arquitetura por capabilities, não por cliente

Nunca criar lógica:

```text
if organization == "Mora": ...
```

Necessidades específicas devem virar capability/configuração reutilizável quando fizer sentido:

```text
commissions.enabled
blindCashClosing.enabled
websites.enabled
marketplaces.enabled
whatsappCommerce.enabled
visualSearch.enabled
customerSourcing.enabled
inventoryTransfers.enabled
```

## Dados globais da plataforma

A plataforma pode precisar de dados operacionais globais para billing, segurança, métricas e suporte. Isso não concede ao produto permissão de usar dados comerciais de um tenant para vender produtos de outro.

Qualquer aprendizado agregado entre tenants exige:

- finalidade definida;
- contrato/base legal adequada;
- anonimização/agregação quando possível;
- revisão de privacidade;
- proteção contra reidentificação;
- nunca transformar dado privado de concorrente em recomendação direta.

## Deleção e offboarding de tenant

Excluir organização exige lifecycle de:

- DB;
- mídia original/derivada;
- cache;
- índices de busca/vector stores;
- analytics;
- tokens/credentials;
- webhooks;
- CustomerRequests/SourcingRequests;
- exports temporários;
- backups conforme retention.

Não implementar como `DELETE FROM organizations`.

## Portabilidade

Cliente deve conseguir exportar seus dados autorizados em formatos versionados. Isso reduz lock-in agressivo e ajuda processos de migração/LGPD.

## Relacionados

- [Identidade e permissões](../domain/identity-employees-permissions.md)
- [Plataforma e onboarding](platform-and-onboarding.md)
- [Segurança](../security/security-architecture.md)
- [Data lifecycle](../data/data-lifecycle.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Demanda e Sourcing](../commerce/customer-demand-and-sourcing.md)
