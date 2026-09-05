<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/brand/mora-core-logo.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/brand/mora-core-logo-dark.png">
    <img src="assets/brand/mora-core-logo-dark.png" alt="Mora Core" width="560">
  </picture>
</p>

<p align="center"><strong>Sistema operacional para o varejo físico e digital.</strong></p>

---

# Mora Core

O **Mora Core** é uma plataforma proprietária, preparada para evoluir como SaaS, concebida para conectar a operação de um comércio do **recebimento da mercadoria à venda física e digital**.

A visão integra, sob uma única fonte de verdade:

- catálogo, categorias, variantes/SKUs e códigos de barras;
- estoque, inventário, transferências e reservas;
- compras, fornecedores e recebimento;
- vendas, PDV, caixa, pagamentos, devoluções e cancelamentos;
- vendedores, funcionários, permissões e comissões;
- clientes e relacionamento;
- sites/storefronts;
- aplicativo mobile;
- WhatsApp e commerce conversacional;
- IA assistida;
- marketplaces e social commerce;
- fiscal;
- analytics;
- integrações e operações de plataforma.

As primeiras operações de referência são as lojas Mora. A arquitetura, porém, **não assume que Mora será a única empresa**: o sistema é desenhado para suportar futuramente outras `Organization`s com isolamento forte de dados e configuração independente.

> [!IMPORTANT]
> **Estado atual:** este repositório é `documentation-first`. Ele contém visão, requisitos, Discovery, arquitetura, regras de negócio, segurança, QA, riscos, benchmarks, migração e roadmap. **Documentação não significa implementação concluída.** Os status oficiais são `implementado`, `parcial`, `experimental`, `planejado`, `adiado` e `não validado`.

---

# Comece por aqui

### Quero entender o projeto sem entrar em código

➡️ **[Visão Executiva do Mora Core](docs/product/executive-overview.md)**

Explica em linguagem de negócio o problema, a proposta, estoque, funcionárias, comissão, caixa, sites, mobile, IA, WhatsApp, marketplaces, fiscal e SaaS.

### Quero ver como tudo funcionaria nas lojas Mora quando estiver completo

➡️ **[Cenário de Referência — Lojas Mora no Estado Futuro](docs/product/mora-reference-future-state.md)**

### Quero entender como os módulos conversam tecnicamente

➡️ **[Fluxos Técnicos End-to-End](docs/architecture/end-to-end-flows.md)**

### Quero entender como as lojas realmente operam hoje

➡️ **[Discovery Operacional](docs/discovery/operational-discovery.md)**  
➡️ **[Baseline real de PDV e hardware — 05/09/2026](docs/discovery/mora-pos-hardware-baseline-2026-09-05.md)**

### Quero entender WhatsApp, demanda não atendida e sourcing

➡️ **[Discovery de WhatsApp, Demanda e Sourcing](docs/discovery/conversational-commerce-discovery.md)**

### Quero navegar pela documentação completa

➡️ **[Índice canônico da documentação](docs/index.md)**

---

# Estado real observado nas lojas Mora

Em **05/09/2026**, o Discovery deixou de ser apenas conceitual e passou a incorporar evidências de campo das duas operações físicas.

## Mora feminina/familiar

Observado em campo:

- PDV **NOOVA Tecnologia**;
- versão exibida `4.01.00.134`;
- caixa `001` em snapshots;
- Windows 11 64-bit;
- Dell OptiPlex 3010;
- Intel Core i3-3240 @ 3.40 GHz;
- 6 GB DDR3;
- LG Flatron E1641;
- leitor YHD-8200;
- impressora Bematech MP-4200 TH.

Também foram observados fluxos/telas de:

- venda;
- seleção de vendedor;
- cliente consumidor e cliente identificado;
- dinheiro, crediário, crédito, débito e Pix;
- desconto e acréscimo;
- abertura/continuação de sessão;
- sangria;
- suprimento;
- fechamento/sessão;
- últimas vendas;
- cancelamento;
- cadastro/listagem de produtos;
- NCM e impostos;
- etiquetas;
- Central de NFC-e;
- gerenciamento de TEF;
- retaguarda administrativa.

A tela de produtos exibiu **5.968 registros** e sinais de dívida de dados, como itens `Sem Categoria`, saldos negativos, resíduos fracionários em unidade `UN`, custos zerados/desconhecidos e nomenclatura histórica heterogênea.

## Mora masculina

Observado em campo:

- PDV **Lips Control**;
- versão exibida `4.01.00.134`;
- caixa `002` em snapshots;
- Windows 10 64-bit;
- Intel Celeron N3350 @ 1.10 GHz;
- 6 GB RAM;
- LG Flatron W1943C;
- leitor Knup KP-1026;
- impressora VERDE / DYJ-5801, 58 mm, USB;
- mini-PC/chassi MLLSE observado, modelo exato ainda não confirmado.

### Regra de evidência

O repositório separa explicitamente:

- **confirmado visualmente**;
- **inferido**;
- **não validado**.

Nenhuma inferência de fotografia vira contrato de software sem teste, configuração, documentação do fornecedor ou validação de campo.

Leia o levantamento completo em **[Baseline real de PDV e hardware — 05/09/2026](docs/discovery/mora-pos-hardware-baseline-2026-09-05.md)**.

---

# O problema que o Mora Core pretende resolver

Hoje, operações físicas e digitais podem exigir múltiplos sistemas, cadastros duplicados, conciliações manuais e conhecimento espalhado entre pessoas, PDVs, planilhas, sites e integrações.

A tese do Mora Core é criar um núcleo único:

```text
Receber mercadoria
        ↓
Identificar / criar SKU
        ↓
Fotografar / enriquecer produto
        ↓
Catálogo + variantes
        ↓
Estoque
        ↓
Publicação
   ┌────┼──────────────┬──────────────┬───────────────┐
   │    │              │              │               │
 PDV  Site(s)      WhatsApp      Marketplaces    Social commerce
   │    │              │              │               │
   └────┴──────────────┴──────────────┴───────────────┘
        ↓
Pedidos / vendas
        ↓
Movimentações de estoque
        ↓
Caixa / comissões / analytics
        ↓
Demanda não atendida → compras / reposição
```

A proposta de longo prazo não é ser apenas “um ERP com PDV”, mas um **sistema operacional para o varejo**.

---

# Sites / Storefront

O Mora Core deve ser a fonte de verdade para qualquer storefront público.

```text
Mora Core
   ↓
CatalogRepository / API
   ↓
Storefront(s)
```

A decisão de produto sobre as lojas Mora ainda está **aberta**:

1. manter vitrines pública feminina/familiar e masculina separadas; ou
2. unificar a experiência pública em um único site Mora com navegação/departamentos adequados.

O domínio **não deve ser acoplado a nenhuma dessas duas opções**. `Organization`, `Brand`, `Store`, `StockLocation`, catálogo, disponibilidade e publicação precisam permitir ambas sem reescrever o Core.

Capacidades planejadas incluem:

- categorias dinâmicas;
- página individual de produto;
- variantes válidas;
- galeria/zoom;
- Novidades;
- Mais Vendidos derivados de venda real;
- Destaques e Promoções semanticamente separados;
- SEO;
- WhatsApp contextualizado;
- busca visual;
- back-in-stock;
- futuro site builder para clientes SaaS sem site.

Leia **[Storefront](docs/product/storefront.md)**.

---

# Arquitetura

O padrão inicial é **monólito modular**, evitando microserviços prematuros.

```text
                              MORA CORE
                                 │
                         Identity / Tenancy
                                 │
        ┌────────────────────────┼────────────────────────┐
        │                        │                        │
      ERP Core                Commerce                Experiência
        │                        │                        │
 Catalog / Inventory        PIM / OMS               Admin / Mobile
 Purchasing / Sourcing     WhatsApp Agent          POS / Storefront(s)
 Sales / Cash              Marketplaces            Inbox / Analytics
 Commissions               Integrations
        └────────────────────────┼────────────────────────┘
                                 │
                           Application Core
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
          PostgreSQL        Object Storage      Jobs / Workers
              │                  │                  │
              └──────────────────┼──────────────────┘
                                 │
      Bling / Meta / TikTok / Mercado Livre / Shopee / Fiscal / Payments
```

Princípios:

- backend/domain é autoridade de regras críticas;
- PostgreSQL reforça invariantes com constraints e transações;
- estoque é ledger de movimentos + saldo derivado/materializado;
- dinheiro usa representação exata;
- integrações ficam atrás de ports/adapters;
- webhooks são deduplicados e reconciliados;
- IA usa tools estreitas e validadas;
- secrets ficam fora de frontend, APK, Git e logs;
- cache não é autoridade de estoque ou autorização;
- jobs preservam tenant context;
- Kafka, Kubernetes, microservices, CQRS, Event Sourcing e Redis só entram com problema comprovado.

Leia:

- **[Arquitetura do sistema](docs/architecture/system-architecture.md)**
- **[Fluxos técnicos end-to-end](docs/architecture/end-to-end-flows.md)**
- **[Monólito modular](docs/architecture/modular-monolith.md)**
- **[Contratos de API](docs/architecture/api-contracts.md)**

---

# Mora POS e periféricos

O Discovery confirmou hardware heterogêneo nas duas lojas. Por isso, o domínio do POS não deve conhecer marcas como Bematech, VERDE, YHD ou Knup.

Ele conhece capacidades:

```text
barcode_input
receipt_print
label_print
cash_drawer
payment_terminal
```

A direção arquitetural estudada é:

```text
Mora Core API
      ↑
    HTTPS
      │
   Mora POS
      │
 local IPC / localhost
      ↓
Local Device Bridge opcional
   ├── scanner
   ├── impressora
   ├── gaveta
   └── health
```

Se existir, o bridge deve ser estreito e seguro, por exemplo:

```text
getDeviceHealth()
printReceipt()
printLabel()
openCashDrawer()
```

Ele não deve executar shell arbitrário, SQL, acessar qualquer arquivo ou receber secrets de banco/marketplace.

A escolha entre PWA + bridge, wrapper desktop leve ou cliente desktop dedicado permanece aberta e será decidida por POC/ADR com **benchmark no hardware real das duas lojas**, incluindo o Celeron N3350 + 6 GB da masculina.

Leia:

- **[Integração do POS com periféricos](docs/architecture/pos-device-integration.md)**
- **[Plano de testes em hardware real](docs/qa/pos-hardware-test-plan.md)**
- **[Barcode, etiquetas e scanner](docs/mobile/barcodes-and-scanning.md)**

---

# Migração dos sistemas atuais

O campo mostrou que não existe apenas “um ERP atual”:

```text
Mora feminina/familiar → NOOVA
Mora masculina         → Lips Control
Bling                   → papel atual ainda precisa ser mapeado
```

Antes de substituir qualquer sistema, precisamos descobrir qual deles é autoridade atual para cada capability:

```text
Catalog
Inventory
Sales
Cash
Payments
Fiscal
Customers
```

A migração **não será um dump direto para o banco canônico**.

```text
extract/read-only
→ snapshot bruto imutável
→ staging
→ profiling/anomalias
→ mapping/dedupe
→ dry-run
→ reconciliação
→ projeção no Core
→ shadow
→ piloto
→ cutover por capability
→ fallback
```

Exemplos de anomalias registradas:

```text
NEGATIVE_BALANCE
FRACTIONAL_UNIT_BALANCE
MISSING_CATEGORY
ZERO_OR_UNKNOWN_COST
DUPLICATE_OR_AMBIGUOUS_IDENTIFIER
UNKNOWN_VARIANT_STRUCTURE
INVALID_OR_MISSING_GTIN
UNMAPPED_TAX_DATA
```

Nada deve ser arredondado, corrigido ou descartado silenciosamente durante a migração.

Leia:

- **[Migração dos legados Mora](docs/data/mora-legacy-data-migration.md)**
- **[Import/export/migração](docs/data/import-export-migration.md)**

---

# Capacidades planejadas

## ERP / retaguarda

- `Organization`, `LegalEntity`, `Brand`, `Store` e `StockLocation`;
- usuários, funcionários, memberships, papéis e permissões;
- catálogo, categorias, atributos, variantes, SKUs e identificadores;
- estoque, inventário, ajustes, transferências e reservas;
- compras, fornecedores e recebimento;
- vendas, pagamentos, devoluções, trocas e cancelamentos;
- caixa: abertura, fundo, sangria, suprimento, reconciliação e fechamento;
- comissão auditável por vendedor;
- clientes e CRM progressivo;
- analytics operacional e gerencial.

## Mobile

- câmera como scanner;
- cadastro de produto;
- variantes;
- fotos/upload;
- SKU/código interno;
- etiquetas;
- recebimento;
- consulta de estoque;
- inventário;
- transferências;
- dashboards por perfil;
- comissão própria;
- sourcing em visita a fornecedores;
- offline seletivo com outbox/idempotência quando necessário.

## IA

- correção de iluminação/enquadramento;
- remoção/limpeza de fundo;
- derivados para web;
- descrição, título, tags e categoria assistidos;
- conteúdo específico por canal;
- busca visual;
- modelos virtuais quando permitido;
- copiloto para funcionária;
- commerce agent;
- evals, provenance, quotas e AI Gateway.

Regra central:

```text
Product Truth
≠
Generated Content
```

IA não é autoridade para preço, saldo, desconto, imposto, pagamento, tenant, autorização ou compra de fornecedor.

## WhatsApp / Commerce conversacional

```text
site / contato direto
→ WhatsApp
→ assistente
→ catálogo / estoque / preço reais
→ alternativas
→ carrinho
→ reserva
→ pagamento
→ fiscal
→ venda
→ estoque
→ pós-venda
```

Capacidades planejadas:

- personal shopper;
- busca por foto;
- cross-sell/upsell responsável;
- carrinho e reserva;
- handoff humano;
- inbox compartilhada;
- modos `AI`, `HUMAN`, `HYBRID` e `PAUSED_AI`;
- back-in-stock;
- status de pedido;
- troca/devolução;
- analytics de conversão e demanda perdida.

### Invariante multi-tenant

**Uma cliente só recebe sugestões de produtos pertencentes à mesma `Organization`.**

## PIM / OMS / Omnichannel

- produto canônico como fonte de verdade;
- `ChannelListing` por canal;
- conteúdo/preço específico por canal quando necessário;
- Mercado Livre, Shopee, TikTok Shop e outros via adapters;
- ingestão de pedidos;
- reserva;
- oversell prevention;
- fulfillment;
- devoluções;
- webhooks;
- dedupe;
- reconciliation;
- compliance por canal.

## Pagamentos e fiscal

Pagamento, venda, caixa, fiscal e impressão são conceitos diferentes.

```text
PaymentPort → autorização/liquidação/reconciliação
FiscalPort  → autorização/documento fiscal
PrinterPort → saída física
```

Direções:

- Pix/link/provider/TEF conforme Discovery;
- webhooks assinados;
- idempotência;
- reconciliação;
- comprovante visual não confirma pagamento automaticamente;
- NF-e/NFC-e conforme operação e regra vigente;
- XML, DANFE/representação, certificado e contingência tratados como alto risco;
- reimpressão nunca refaz venda ou emissão fiscal.

Leia **[Fiscal e pagamentos](docs/domain/fiscal-and-payments.md)**.

---

# SaaS e multi-tenancy

Estrutura conceitual:

```text
Organization
  ├── LegalEntity(s)
  ├── Brand(s)
  ├── Store(s)
  └── StockLocation(s)
```

O isolamento deve existir em:

- banco/query/repository;
- autorização;
- jobs;
- cache;
- IA;
- embeddings/busca;
- mídia;
- integrações;
- analytics;
- billing da plataforma.

A arquitetura pode começar com PostgreSQL compartilhado e isolamento rigoroso, sem assumir banco separado por tenant.

Leia:

- **[Multi-tenancy](docs/saas/multitenancy.md)**
- **[Plataforma e onboarding](docs/saas/platform-and-onboarding.md)**
- **[Billing e entitlements](docs/saas/billing-entitlements.md)**

---

# Padrão de engenharia

> **Não otimizar para parecer sofisticado. Otimizar para resistir a uma auditoria séria.**

A **[Engineering Constitution](docs/engineering/constitution.md)** exige, entre outros pontos:

- business rules first;
- secure-by-design/default;
- tenant isolation explícito;
- banco protegendo invariantes;
- testes por risco;
- núcleo determinístico para dinheiro, estoque e autorização;
- IA tratada como entrada não confiável;
- idempotência e concurrency quando necessárias;
- lifecycle de dados;
- observabilidade;
- acessibilidade;
- DevSecOps/supply chain;
- backup/restore;
- commits lógicos e push remoto real;
- CI no SHA exato;
- auditoria independente em milestones importantes.

`AGENTS.md` funciona como mapa operacional para agentes futuros e aponta para os documentos canônicos em vez de repetir toda a especificação em um prompt gigante.

---

# Documentação

A documentação aprofundada é a fonte de verdade do projeto enquanto o repositório permanece `documentation-first`.

➡️ **[Índice completo da documentação](docs/index.md)**

## Documentos-chave

### Produto e negócio

- [Visão Executiva](docs/product/executive-overview.md)
- [Cenário completo das lojas Mora](docs/product/mora-reference-future-state.md)
- [Visão do produto](docs/product/vision.md)
- [Escopo e não objetivos](docs/product/scope-and-non-goals.md)
- [Módulos e capacidades](docs/product/modules.md)
- [Personas e jornadas](docs/product/personas-and-journeys.md)
- [Storefront](docs/product/storefront.md)
- [Analytics e relatórios](docs/product/analytics-and-reporting.md)

### Discovery

- [Discovery operacional](docs/discovery/operational-discovery.md)
- [Baseline real de PDV e hardware — 05/09/2026](docs/discovery/mora-pos-hardware-baseline-2026-09-05.md)
- [Discovery de WhatsApp, demanda e sourcing](docs/discovery/conversational-commerce-discovery.md)

### Arquitetura e domínio

- [Arquitetura do sistema](docs/architecture/system-architecture.md)
- [Fluxos end-to-end](docs/architecture/end-to-end-flows.md)
- [Monólito modular](docs/architecture/modular-monolith.md)
- [Contratos de API](docs/architecture/api-contracts.md)
- [Integração do POS com periféricos](docs/architecture/pos-device-integration.md)
- [Modelo de domínio](docs/domain/domain-model.md)
- [Identidade, funcionários e permissões](docs/domain/identity-employees-permissions.md)
- [Multi-tenancy](docs/saas/multitenancy.md)
- [Estoque](docs/domain/inventory.md)
- [Compras e fornecedores](docs/domain/purchasing-and-suppliers.md)
- [Vendas, caixa e comissões](docs/domain/sales-cash-commissions.md)
- [Fiscal e pagamentos](docs/domain/fiscal-and-payments.md)

### Mobile, IA e commerce

- [Mobile](docs/mobile/mobile-app.md)
- [Barcode/scanner](docs/mobile/barcodes-and-scanning.md)
- [Governança de IA](docs/ai/ai-governance.md)
- [Pipeline de mídia](docs/ai/media-pipeline.md)
- [Taxonomia do catálogo](docs/commerce/catalog-taxonomy.md)
- [Omnichannel/PIM/OMS](docs/commerce/omnichannel.md)
- [WhatsApp Commerce Agent](docs/commerce/whatsapp-commerce-agent.md)
- [Inbox/handoff/copiloto](docs/commerce/conversation-operations.md)
- [Demanda e sourcing](docs/commerce/customer-demand-and-sourcing.md)
- [Marketplaces](docs/integrations/marketplaces.md)
- [Bling](docs/integrations/bling.md)
- [Fiscal Brasil](docs/integrations/fiscal-brazil.md)
- [Confiabilidade de integrações](docs/integrations/reliability-patterns.md)

### Dados, migração e onboarding

- [Data lifecycle](docs/data/data-lifecycle.md)
- [Import/export/migração](docs/data/import-export-migration.md)
- [Migração dos legados Mora](docs/data/mora-legacy-data-migration.md)
- [Plataforma e onboarding](docs/saas/platform-and-onboarding.md)
- [Billing e entitlements](docs/saas/billing-entitlements.md)

### Engenharia profissional

- [Engineering Constitution](docs/engineering/constitution.md)
- [Segurança](docs/security/security-architecture.md)
- [Identidade, RBAC e auditoria](docs/security/identity-rbac-audit.md)
- [Privacidade/LGPD](docs/security/privacy-lgpd.md)
- [QA/Testes](docs/qa/test-strategy.md)
- [Plano de testes do POS em hardware real](docs/qa/pos-hardware-test-plan.md)
- [DevSecOps](docs/devops/devsecops.md)
- [SRE/AIOps](docs/operations/sre-aiops.md)
- [Performance/Capacidade](docs/operations/performance-capacity.md)
- [Incidentes/Recovery](docs/operations/incident-recovery.md)
- [Runbooks](docs/operations/runbook-catalog.md)
- [Acessibilidade e UX](docs/ux/accessibility-and-design.md)

### Planejamento, rastreabilidade e pesquisa

- [Roadmap](docs/roadmap/roadmap.md)
- [Roadmap de commerce conversacional](docs/roadmap/conversational-commerce-roadmap.md)
- [Riscos e questões em aberto](docs/roadmap/risks-and-open-questions.md)
- [Rastreabilidade](docs/roadmap/requirements-traceability.md)
- [Rastreabilidade do Discovery de campo — 05/09/2026](docs/roadmap/field-discovery-traceability-2026-09-05.md)
- [Cobertura documental](docs/roadmap/documentation-coverage.md)
- [Benchmark ERP/PDV](docs/research/competitive-benchmark.md)
- [Benchmark conversacional](docs/research/conversational-commerce-benchmark.md)
- [Landscape de mercado](docs/research/market-landscape.md)
- [Snapshot de políticas dos canais](docs/research/channel-policy-snapshot-2026-08.md)
- [Auditoria operacional/documental — 05/09/2026](docs/audit/repository-documentation-audit-2026-09-05.md)
- [Auditoria documental — 26/08/2026](docs/audit/repository-documentation-audit-2026-08-26.md)
- [ADRs](docs/adr/README.md)

### Projeto

- [Apresentação do projeto e portfólio](docs/project/project-presentation-and-portfolio.md)
- [Marca, naming e titularidade](docs/project/branding-and-ownership.md)

## Estrutura documental

```text
docs/
├── audit/
├── discovery/
├── product/
├── architecture/
├── domain/
├── saas/
├── mobile/
├── ai/
├── commerce/
├── integrations/
├── security/
├── qa/
├── devops/
├── operations/
├── data/
├── ux/
├── roadmap/
├── research/
├── project/
├── engineering/
└── adr/
```

### Como interpretar

- `README.md` = visão e mapa atual do projeto;
- `/docs` = especificação aprofundada e verdade documental;
- `discovery/*` = fatos observados e perguntas ainda em aberto;
- `architecture/*` = boundaries, ports/adapters, fluxos e trade-offs;
- `domain/*` = regras, entidades e invariantes;
- `research/*` = snapshots datados que precisam ser revalidados antes da implementação;
- `audit/*` = auditorias datadas;
- ADR = decisão arquitetural e trade-offs;
- runbook = operação/incidente/recuperação.

---

# Roadmap resumido

1. **Discovery real** das lojas, sistemas atuais, Bling, WhatsApp, caixa, fiscal, pagamentos, compras, rede e hardware.
2. **Foundation**: identidade, organizations, lojas, catálogo, variantes, estoque, audit e CI.
3. **Mobile operacional**: scanner, produto, mídia, recebimento, inventário e sourcing.
4. **Storefront(s)** conectados ao catálogo/estoque real; decidir experiência pública unificada ou separada sem acoplar o Core.
5. **Commerce conversacional** incremental: catálogo → busca visual → carrinho/reserva → pagamento → fiscal → sourcing.
6. **Sales/POS/Cash/Commissions** com hardware real, shadow, piloto e fallback.
7. **Analytics operacional** baseado em transações reais.
8. **IA assistida** com evals/provenance/human review.
9. **Omnichannel/PIM/OMS** com adapters estabilizados um a um.
10. **SaaS self-service** com onboarding, billing, export, suporte e hardening multi-tenant.
11. **Escala/especialização** apenas quando dados justificarem.

Veja [Roadmap principal](docs/roadmap/roadmap.md) e [Roadmap de Commerce Conversacional](docs/roadmap/conversational-commerce-roadmap.md).

---

# O que ainda depende de Discovery/ADR

A documentação não inventa respostas para pontos ainda desconhecidos. Permanecem deliberadamente abertos, entre outros:

- topologia legal/CNPJ real;
- regra de comissão;
- política de desconto;
- trocas/devoluções;
- reservas;
- decisão de storefront único vs vitrines separadas;
- interfaces/drivers exatos dos periféricos;
- modelo/ligação de gaveta;
- adquirente, TEF e Pix;
- ownership fiscal atual entre NOOVA/Lips/Bling/outros;
- certificado/CSC e contingência fiscal;
- rede, link de backup e nobreak;
- estratégia de substituição dos PCs;
- stack backend/ORM/auth/cloud;
- queue/jobs;
- runtime final de IA;
- contratos vigentes dos marketplaces;
- RPO/RTO/SLO;
- planos e preços SaaS.

Essas decisões devem sair de Discovery, ADR, POC, contrato atual ou validação externa apropriada.

---

# Licença

Mora Core é software proprietário. Consulte [LICENSE](LICENSE).

**© 2026 Mora Core. Todos os direitos reservados.**