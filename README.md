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

O **Mora Core** é uma plataforma proprietária, preparada para evoluir como SaaS, concebida para conectar a operação de um comércio do **recebimento da mercadoria à venda física e digital**, centralizando catálogo, variantes/SKUs, estoque, compras, fornecedores, vendas, PDV, caixa, vendedores, comissões, sites, mobile, IA, WhatsApp, marketplaces, fiscal, analytics e integrações.

As primeiras operações de referência são as lojas Mora. A arquitetura, porém, não assume que Mora será a única empresa: o sistema é desenhado desde o início para suportar futuramente outras `Organization`s com isolamento forte de dados.

> [!IMPORTANT]
> **Estado atual:** este repositório é `documentation-first`. Ele contém visão, requisitos, arquitetura, regras de negócio, riscos, benchmark e roadmap. **Isso não significa que as funcionalidades descritas já estejam implementadas.** Os status oficiais são `implementado`, `parcial`, `experimental`, `planejado`, `adiado` e `não validado`.

## Comece por aqui

### Quero entender o projeto sem entrar em código

➡️ **[Visão Executiva do Mora Core](docs/product/executive-overview.md)**

Explica em linguagem de negócio o problema, a proposta, estoque, funcionárias, comissão, caixa, sites, mobile, IA, WhatsApp, marketplaces, fiscal e SaaS.

### Quero ver como tudo funcionaria nas lojas Mora quando estiver completo

➡️ **[Cenário de Referência — Lojas Mora no Estado Futuro](docs/product/mora-reference-future-state.md)**

É o exemplo ponta a ponta:

```text
compra
→ fornecedor
→ recebimento
→ cadastro/variantes
→ fotos/IA
→ estoque
→ site/WhatsApp/marketplaces/PDV
→ reserva
→ pagamento
→ fiscal
→ venda
→ caixa
→ comissão
→ analytics
→ nova compra
```

### Quero entender como os módulos conversam tecnicamente

➡️ **[Fluxos Técnicos End-to-End](docs/architecture/end-to-end-flows.md)**

Mostra os boundaries entre catálogo, estoque, vendas, pagamentos, fiscal, WhatsApp, marketplaces, sourcing, jobs e multi-tenancy.

### Quero ajudar a transformar a operação real em requisitos

➡️ **[Discovery Operacional](docs/discovery/operational-discovery.md)**  
➡️ **[Discovery de WhatsApp, Demanda e Sourcing](docs/discovery/conversational-commerce-discovery.md)**

---

## Tese do produto

O Mora Core pretende eliminar o retrabalho entre loja física, retaguarda e canais digitais.

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
 PDV  Site         WhatsApp      Marketplaces    Social commerce
   │    │              │              │               │
   └────┴──────────────┴──────────────┴───────────────┘
        ↓
Pedidos / Vendas
        ↓
Movimentações de estoque
        ↓
Caixa / comissões / analytics
        ↓
Demanda não atendida → compras / reposição
```

A proposta de longo prazo não é ser apenas “um ERP com PDV”, mas um **sistema operacional para o varejo**, capaz de conectar a entrada da mercadoria, a operação da loja e os canais de venda sem cadastrar a mesma informação várias vezes.

---

# Capacidades planejadas

## ERP / retaguarda

- `Organization`, `LegalEntity`, `Brand`, `Store` e `StockLocation`.
- Usuários, funcionários, memberships, papéis e permissões por loja/escopo.
- Catálogo, categorias hierárquicas, atributos, variantes, SKUs e códigos de barras.
- Estoque baseado em ledger de movimentos, saldo derivado, inventário, ajustes, transferências e reservas.
- Compras, fornecedores, pedidos e recebimentos.
- Vendas, pagamentos, devoluções, trocas e cancelamentos.
- Caixa: abertura, fundo, sangria, suprimento, reconciliação e fechamento.
- Comissão auditável por vendedor, com reversões e fechamento mensal.
- Clientes, wishlist, back-in-stock e relacionamento progressivo.
- Analytics operacional e gerencial.

## Aplicativo mobile

O app é pensado como ferramenta operacional, não apenas um painel reduzido.

- câmera como scanner;
- cadastro de produto no celular;
- grade de variantes;
- fotografia e upload;
- geração de SKU/código interno;
- etiquetas;
- recebimento;
- consulta de estoque;
- inventário;
- transferências;
- dashboards por perfil;
- comissão própria conforme permissão;
- modo de compras/sourcing em visitas a fornecedores;
- offline seletivo com outbox/idempotência quando necessário.

## IA aplicada ao comércio

- correção de iluminação e enquadramento;
- remoção/limpeza de fundo;
- derivados para web;
- descrição, título, tags e categoria assistidos;
- conteúdo específico por canal;
- busca visual por foto enviada pelo cliente;
- imagem com modelo virtual quando permitida;
- copiloto para funcionária;
- commerce agent conversacional;
- evals, provenance, quotas e AI Gateway.

### Regra central

```text
Product Truth
≠
Generated Content
```

A IA não é autoridade para preço, saldo, desconto, imposto, pagamento, tenant, autorização ou compra de fornecedor.

## Sites / Storefront

- site próprio alimentado pelo Mora Core;
- categorias dinâmicas;
- página individual de produto;
- variantes válidas;
- galeria/zoom;
- Novidades;
- Mais Vendidos baseados em vendas reais;
- Destaques e Promoções separados;
- SEO;
- WhatsApp contextualizado;
- busca visual;
- back-in-stock;
- pedido de estilo/encomenda;
- futuro site builder para clientes SaaS sem site.

## WhatsApp / Commerce conversacional

O WhatsApp pode ser um dos primeiros canais de venda online realmente conectados ao Core.

```text
site / contato direto
→ WhatsApp
→ assistente
→ catálogo/estoque/preço reais
→ alternativas
→ carrinho
→ reserva
→ pagamento
→ fiscal
→ venda
→ estoque
→ pós-venda
```

Capacidades:

- personal shopper;
- fotos e comparação;
- busca por foto do cliente;
- cross-sell/upsell responsáveis;
- carrinho;
- reserva;
- handoff humano;
- inbox compartilhada;
- modo `AI`, `HUMAN`, `HYBRID` ou `PAUSED_AI`;
- copiloto para vendedora;
- back-in-stock;
- recuperação de carrinho quando consentido;
- status de pedido, troca/devolução e documentos;
- analytics de conversão e demanda perdida.

### Invariante multi-tenant

**Uma cliente só recebe sugestões da mesma `Organization`.**

O Mora Core não usa o catálogo de outros clientes do SaaS como vitrine concorrente. Isso vale para busca textual, visual, embeddings, cache, marketplace fallback e agents.

## Demanda perdida e sourcing

Se a loja não tiver o que a cliente pediu, o sistema pode transformar a conversa em dado operacional:

```text
cliente manda foto ou descreve produto
→ busca alternativas da própria Organization
→ não encontrou
→ CustomerRequest
→ DemandSignal
→ SourcingRequest
→ próxima compra/viagem
→ candidato encontrado
→ cliente aprova
→ compra
→ recebimento
→ reserva
→ cliente avisado
→ venda
```

Isso permite responder futuramente:

> “O que as clientes estão pedindo e nós não temos?”

Sem confundir demanda com venda real.

## PIM / OMS / Omnichannel

- produto canônico como fonte de verdade;
- `ChannelListing` por canal;
- conteúdo/price override específico quando necessário;
- adapters para TikTok Shop, Mercado Livre, Shopee e outros;
- ingestão de pedidos;
- reserva de estoque;
- prevenção de overselling;
- fulfillment;
- devoluções;
- webhooks;
- dedupe;
- reconciliation;
- compliance por canal.

Um listing externo só pode ser oferecido ao consumidor se pertencer à mesma organização.

## Pagamentos e fiscal

Pagamento, venda e caixa são conceitos diferentes.

- Pix/link/provider/TEF conforme arquitetura futura;
- webhooks assinados;
- idempotência;
- reconciliação;
- comprovante visual não confirma pagamento automaticamente;
- `FiscalPort` para abstrair Bling/provedor/SEFAZ;
- NF-e/NFC-e conforme operação e regra vigente;
- NFS-e somente para serviços quando aplicável;
- XML, DANFE/representação, certificado, contingência e legal entity tratados como domínio de alto risco.

A direção inicial de menor risco é manter a função fiscal existente via Bling/provedor validado e só substituir quando houver evidência e contingência testada.

## SaaS

- multi-tenancy desde o modelo inicial;
- várias marcas, lojas, CNPJs, depósitos, sites e canais por organização;
- onboarding;
- importação/exportação;
- entitlements;
- quotas;
- billing separado do dinheiro das vendas;
- WhatsApp e marketplaces conectados por tenant;
- observabilidade/custos por organização;
- backup/restore;
- offboarding e portabilidade;
- isolamento de dados, busca, IA, jobs e cache.

---

# Exemplo de operação Mora

A documentação usa as lojas Mora como **design partner** para validar o produto.

Exemplo conceitual:

```text
Organization: Mora
├── operação feminina/familiar
│   ├── Store
│   ├── StockLocation
│   ├── Website
│   └── canais próprios
└── operação masculina
    ├── Store
    ├── StockLocation
    ├── Website
    └── canais próprios
```

A topologia real de CNPJ, estoque e marcas ainda precisa ser confirmada no Discovery.

Leia o fluxo completo em **[Cenário de Referência — Lojas Mora](docs/product/mora-reference-future-state.md)**.

---

# Arquitetura

O padrão inicial é **monólito modular**, evitando microserviços prematuros.

```text
                         MORA CORE
                            │
                    Identidade / Tenancy
                            │
        ┌───────────────────┼────────────────────┐
        │                   │                    │
     ERP Core            Commerce            Experiência
        │                   │                    │
 Catalog/Inventory      PIM / OMS          Admin / Mobile
 Purchasing/Sourcing   WhatsApp Agent     POS / Storefront
 Sales/Cash            Marketplaces       Inbox / Analytics
 Commissions           Integrations
        └───────────────────┼────────────────────┘
                            │
                      Application Core
                            │
          ┌─────────────────┼──────────────────┐
          │                 │                  │
      PostgreSQL       Object Storage      Jobs/Workers
          │                 │                  │
          └─────────────────┼──────────────────┘
                            │
        Bling / Meta / TikTok / ML / Shopee / Fiscal / Payments
```

Regras arquiteturais:

- backend/domain é autoridade de regras críticas;
- PostgreSQL reforça invariantes com constraints/transações;
- estoque é ledger + saldo derivado/materializado;
- dinheiro usa representação exata;
- integrações ficam atrás de ports/adapters;
- webhooks são deduplicados e reconciliados;
- IA usa tools estreitas e validadas;
- secrets ficam fora de frontend/APK/Git/logs;
- cache não vira autoridade de estoque/autorização;
- jobs preservam tenant context;
- Kafka/Kubernetes/microservices/CQRS/Event Sourcing/Redis só entram com problema comprovado.

Veja **[Arquitetura do Sistema](docs/architecture/system-architecture.md)** e **[Fluxos End-to-End](docs/architecture/end-to-end-flows.md)**.

---

# Módulos

O mapa funcional canônico está em **[Módulos e Capacidades](docs/product/modules.md)**.

Principais boundaries:

```text
identity / organizations
catalog
inventory
purchasing / sourcing
sales
cash
commissions
customers / CRM
conversational commerce
mobile
media
AI
storefront
commerce / PIM / OMS
integrations
reporting / analytics
audit
fiscal
billing SaaS
platform operations
```

Módulo não significa microserviço.

---

# Estrutura documental

```text
mora-core/
├── README.md
├── LICENSE
├── AGENTS.md
├── SECURITY.md
├── CONTRIBUTING.md
├── assets/
│   └── brand/
└── docs/
    ├── index.md
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

A navegação canônica começa em **[docs/index.md](docs/index.md)**.

## Documentos-chave

### Produto e negócio

- [Visão Executiva](docs/product/executive-overview.md)
- [Cenário completo das lojas Mora](docs/product/mora-reference-future-state.md)
- [Módulos e capacidades](docs/product/modules.md)
- [Storefront](docs/product/storefront.md)
- [Analytics](docs/product/analytics-and-reporting.md)

### Arquitetura e domínio

- [Arquitetura do sistema](docs/architecture/system-architecture.md)
- [Fluxos end-to-end](docs/architecture/end-to-end-flows.md)
- [Modelo de domínio](docs/domain/domain-model.md)
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
- [Omnichannel/PIM/OMS](docs/commerce/omnichannel.md)
- [WhatsApp Commerce Agent](docs/commerce/whatsapp-commerce-agent.md)
- [Inbox/handoff/copiloto](docs/commerce/conversation-operations.md)
- [Demanda e sourcing](docs/commerce/customer-demand-and-sourcing.md)
- [Marketplaces](docs/integrations/marketplaces.md)
- [Bling](docs/integrations/bling.md)
- [Fiscal Brasil](docs/integrations/fiscal-brazil.md)

### Engenharia profissional

- [Engineering Constitution](docs/engineering/constitution.md)
- [Segurança](docs/security/security-architecture.md)
- [Privacidade/LGPD](docs/security/privacy-lgpd.md)
- [QA/Testes](docs/qa/test-strategy.md)
- [DevSecOps](docs/devops/devsecops.md)
- [SRE/AIOps](docs/operations/sre-aiops.md)
- [Performance/Capacidade](docs/operations/performance-capacity.md)
- [Incidentes/Recovery](docs/operations/incident-recovery.md)
- [Runbooks](docs/operations/runbook-catalog.md)

### Planejamento e pesquisa

- [Roadmap](docs/roadmap/roadmap.md)
- [Roadmap de commerce conversacional](docs/roadmap/conversational-commerce-roadmap.md)
- [Rastreabilidade](docs/roadmap/requirements-traceability.md)
- [Cobertura documental](docs/roadmap/documentation-coverage.md)
- [Benchmark ERP/PDV](docs/research/competitive-benchmark.md)
- [Benchmark conversacional](docs/research/conversational-commerce-benchmark.md)
- [Auditoria documental de 26/08/2026](docs/audit/repository-documentation-audit-2026-08-26.md)

---

# Padrão de engenharia

> **Não otimizar para parecer sofisticado. Otimizar para resistir a uma auditoria séria.**

A [Engineering Constitution](docs/engineering/constitution.md) exige, entre outros pontos:

- business rules first;
- secure-by-design/default;
- tenant isolation explícito;
- banco protegendo invariantes;
- testes por risco;
- núcleo determinístico para dinheiro/estoque/autorização;
- IA tratada como entrada não confiável;
- idempotência/concurrency quando necessárias;
- lifecycle de dados;
- observabilidade;
- accessibility;
- DevSecOps/supply chain;
- backup/restore;
- commits lógicos e push remoto real;
- CI no SHA exato;
- auditoria independente em milestones importantes.

`AGENTS.md` funciona como mapa operacional para agentes futuros e aponta para os documentos canônicos em vez de tentar repetir toda a especificação num prompt gigante.

---

# Roadmap resumido

1. **Discovery** da operação real, Bling, WhatsApp, caixa, fiscal e compras.
2. **Foundation**: identidade, organizations, lojas, catálogo, variantes, estoque, audit e CI.
3. **Mobile operacional**: scanner, produto, mídia, recebimento, inventário e sourcing.
4. **Sites conectados** ao catálogo/estoque real.
5. **Commerce conversacional** incremental: catálogo → busca visual → carrinho/reserva → pagamento → fiscal → sourcing.
6. **Sales/POS/Cash/Commissions** com piloto/shadow e fallback.
7. **Analytics operacional** baseado em transações reais.
8. **IA assistida** com evals/provenance/human review.
9. **Omnichannel/PIM/OMS** com adapters estabilizados um a um.
10. **SaaS self-service** com onboarding, billing, export, suporte e hardening multi-tenant.
11. **Escala/especialização** apenas quando dados justificarem.

Veja [Roadmap principal](docs/roadmap/roadmap.md) e [Roadmap de Commerce Conversacional](docs/roadmap/conversational-commerce-roadmap.md).

---

# O que ainda depende de Discovery/ADR

A documentação não inventa respostas para pontos ainda desconhecidos. Permanecem deliberadamente abertos, entre outros:

- CNPJ/topologia legal real;
- regra de comissão;
- política de desconto;
- trocas/devoluções;
- reservas;
- hardware;
- adquirente/TEF/Pix;
- entrega/frete;
- provider fiscal;
- stack backend/ORM/auth/cloud;
- queue/jobs;
- runtime final de IA;
- contratos vigentes dos marketplaces;
- RPO/RTO/SLO;
- planos e preços SaaS.

Essas decisões devem sair do Discovery, ADR, POC, contrato atual ou validação externa apropriada.

---

# Licença

Mora Core é software proprietário. Consulte [LICENSE](LICENSE).

**© 2026 Mora Core. Todos os direitos reservados.**