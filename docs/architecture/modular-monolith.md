# Monólito Modular

> **Status:** decisão arquitetural de direção; formalizar em ADR quando a implementação iniciar.

## Por que

O Mora Core possui domínio amplo, mas inicialmente uma única equipe/produto. Microserviços criariam custos de rede, autenticação service-to-service, tracing, deploy múltiplo, consistência distribuída e debugging sem benefício comprovado.

O objetivo do monólito modular é combinar:

- simplicidade operacional;
- transações fortes;
- deploy único ou poucos processos;
- fronteiras de negócio claras;
- possibilidade de extração futura.

## Módulos propostos

```text
identity
organizations
authorization
catalog
inventory
purchasing
sales
cash
commissions
customers
commerce
orders
storefront
media
ai
integrations
reporting
audit
billing
```

Os nomes finais podem mudar. O importante é ownership de regras e dependências.

## Estrutura interna sugerida por módulo

Exemplo:

```text
inventory/
├── domain/
│   ├── entities
│   ├── value-objects
│   ├── policies
│   └── events
├── application/
│   ├── commands
│   ├── queries
│   └── ports
├── infrastructure/
│   ├── persistence
│   └── adapters
└── interface/
    └── http
```

Não obrigar essa árvore quando um módulo pequeno não justificar todas as pastas.

## Regras de fronteira

### 1. Ownership

Cada conceito tem dono:

- `InventoryMovement` → Inventory;
- `CommissionEntry` → Commissions;
- `CashSession` → Cash;
- `ChannelListing` → Commerce;
- `MediaAsset` → Media;
- permissions → Authorization.

Outro módulo não altera estado interno diretamente.

### 2. Banco compartilhado não significa acesso livre

Mesmo usando um PostgreSQL, módulos não devem espalhar joins/escritas contra tabelas internas alheias em qualquer lugar.

Leitura cross-module pode usar:

- query contract;
- projection;
- read model;
- repository/application service;
- SQL deliberado em reporting quando justificado e isolado.

### 3. Sem `shared/` como lixeira

Código compartilhado só existe quando o conceito é realmente transversal e estável:

- money;
- identifiers;
- clock;
- result/error primitives;
- observability contracts.

Não mover regra de negócio para `shared` para resolver ciclo.

### 4. Dependências direcionais

Evitar ciclos como:

```text
Sales → Inventory → Commissions → Sales
```

Preferir contratos de aplicação e eventos com ownership claro.

## Integração entre módulos

### Chamada direta

Preferível quando:

- mesma transação;
- resultado imediato necessário;
- baixa complexidade.

### Evento interno

Útil quando:

- consumidores múltiplos;
- side effect pode ser assíncrono;
- ownership separado;
- eventual consistency é aceitável.

Exemplo:

```text
ProductPublished
→ Storefront projection
→ Channel publication scheduler
```

Não usar event bus interno só para parecer “event-driven”.

## Transações

Uma única transação pode atravessar mais de um módulo se isso for necessário para uma invariável crítica, mas essa decisão precisa ficar na application layer.

Exemplo de venda:

```text
validate sale
→ reserve/consume inventory
→ persist Sale/SaleItems
→ persist local movements
→ persist commission entry
→ commit
→ depois side effects externos
```

A atomicidade exata precisa ser definida por caso de uso e banco real.

## Composition root

Responsabilidades:

- carregar config;
- montar dependências;
- inicializar observability;
- registrar routes/modules;
- iniciar processos/workers;
- validar migrations/config.

Não conter regra comercial.

## Repositórios e ports

Domínio/aplicação dependem de abstrações quando há fronteira externa ou persistência relevante.

Evitar abstrações vazias `IWhateverService` para cada classe. Ports precisam representar capacidade real substituível/testável.

## Adapters externos

```text
BlingAdapter
MercadoLivreAdapter
TikTokShopAdapter
ShopeeAdapter
FiscalProviderAdapter
AIProviderAdapter
ObjectStorageAdapter
```

Payload externo é traduzido na boundary e não contamina entidades internas.

## Dados e migrations

Mesmo em monólito, migration precisa preservar compatibilidade durante deploy. Mudança cross-module deve ter owner e blast radius claros.

## Testes de fronteira

Possíveis guards:

- lint/import rules;
- dependency graph;
- testes arquiteturais;
- code review checklist.

Evitar enforcement tão pesado que atrapalhe mudanças pequenas.

## Extração futura

Critérios candidatos:

- volume/escala muito diferente;
- falhas precisam de isolamento independente;
- deploy independente gera benefício real;
- equipe/ownership separado;
- contrato maduro;
- dados e consistência compreendidos;
- observabilidade preparada.

Exemplo plausível futuro: media/AI processing pode escalar de forma diferente. Isso não é motivo para já ser microservice.

## Anti-padrões

- módulo por tabela;
- módulo por tela;
- acesso direto universal ao banco;
- `utils` com regra de domínio;
- eventos para tudo;
- abstração genérica de marketplace que esconde diferenças críticas;
- microservice com banco compartilhado e acoplamento forte;
- módulos nominalmente separados mas com imports cíclicos.

## Critério de sucesso

Um módulo é saudável quando:

- regras principais ficam próximas;
- interface pública pequena;
- dependências claras;
- testes podem focar o módulo;
- mudança local não exige editar dezenas de áreas;
- extração futura é possível, mas não obrigatória.

## Relacionados

- [Arquitetura](system-architecture.md)
- [Modelo de domínio](../domain/domain-model.md)
- [Engineering Constitution](../engineering/constitution.md)
