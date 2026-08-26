# Arquitetura do Sistema

## Objetivo

Definir a arquitetura-alvo do Mora Core sem antecipar complexidade operacional desnecessária.

## Estilo arquitetural

O padrão inicial é **monólito modular** com aplicação web/API e workers dentro do mesmo produto, compartilhando contratos e banco transacional, mas mantendo fronteiras de módulo explícitas.

```text
Clientes
├── Admin Web
├── Mobile
├── POS
└── Storefront público
        ↓
API / Application Layer
        ↓
Domínios modulares
├── Identity / Organizations
├── Catalog / Inventory / Purchasing
├── Sales / Cash / Commissions
├── Commerce / Orders / Channels
├── Media / AI
├── Reporting / Audit
└── Billing SaaS
        ↓
Infraestrutura
├── PostgreSQL
├── Object Storage
├── Jobs/Queue
├── Cache opcional
└── Integrações externas
```

## Princípios

- Domínio não depende de framework, provedor fiscal, marketplace ou storage específico.
- Infraestrutura implementa ports definidos pela aplicação/domínio.
- APIs públicas não espelham tabelas.
- Valores monetários usam inteiros em centavos ou tipo decimal exato apropriado.
- Timestamps persistidos em UTC; timezone da loja é configuração de apresentação/regra local.
- IDs públicos não devem expor informação sensível ou permitir enumeração previsível quando isso aumentar risco.
- Operações financeiras/estoque usam transações e invariantes no banco.
- Eventos assíncronos não substituem consistência transacional onde ela é necessária.

## Camadas

### Domínio
Entidades, value objects, invariantes, policies, estados e regras de negócio.

### Aplicação
Casos de uso, orquestração, authorization checks, ports, transações e comandos/queries.

### Infraestrutura
PostgreSQL, storage, HTTP clients, filas, fiscal, marketplaces, email, observabilidade.

### Interface
HTTP/API, web, mobile, POS e storefront.

## Evolução

Microserviços só serão considerados quando existir pressão mensurável de escala, isolamento de falha, ownership organizacional ou deploy independente que justifique o custo. Até lá, os módulos devem ser extraíveis por contrato sem já operar como serviços separados.

## Decisões relacionadas
- [Monólito modular](modular-monolith.md)
- [Contratos de API](api-contracts.md)
- [Multi-tenancy](../saas/multitenancy.md)
- [ADRs](../adr/README.md)
