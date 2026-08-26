<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/brand/mora-core-logo.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/brand/mora-core-logo-dark.png">
    <img src="assets/brand/mora-core-logo-dark.png" alt="Mora Core" width="720">
  </picture>
</p>

<p align="center"><strong>Sistema operacional para o varejo físico e digital.</strong></p>

---

# Mora Core

O **Mora Core** é uma plataforma proprietária, preparada para evoluir como SaaS, projetada para centralizar catálogo, produtos, variantes/SKUs, estoque, compras, vendas, PDV, caixas, vendedores, comissões, sites, aplicativo mobile, IA aplicada ao catálogo, analytics, integrações e comércio omnichannel.

As primeiras operações reais de referência são as lojas Mora. A arquitetura, porém, não assume que a Mora será a única organização: desde o início, o sistema é pensado para atender futuramente lojas independentes, marcas, grupos com múltiplos estabelecimentos e operações multiempresa com isolamento forte de dados.

> [!IMPORTANT]
> **Estado atual:** este repositório está em fase `documentation-first`. Os documentos registram visão, domínio, arquitetura, riscos, contratos e roadmap. Eles **não significam que todas as funcionalidades descritas já existem**. Cada milestone deverá declarar honestamente `implementado`, `parcial`, `experimental`, `planejado`, `adiado` ou `não validado`.

## Para conhecer o projeto sem entrar na parte técnica

Se você é proprietário de loja, familiar, parceiro ou quer entender primeiro a ideia do negócio, comece pela **[Visão Executiva do Mora Core](docs/product/executive-overview.md)**.

Para transformar a operação real das lojas em requisitos corretos, consulte também o **[Discovery Operacional](docs/discovery/operational-discovery.md)**.

## Tese do produto

O Mora Core deve eliminar o retrabalho entre loja física, retaguarda e canais digitais:

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
   ┌────┼───────────────┬──────────────┐
   │    │               │              │
 PDV  Site próprio  Marketplaces   Social commerce
   │    │               │              │
   └────┴───────────────┴──────────────┘
        ↓
Pedidos / Vendas
        ↓
Movimentações de estoque
        ↓
Caixa / comissões / analytics
```

A proposta de longo prazo não é ser apenas “um ERP com PDV”, mas um **sistema operacional para o varejo**, capaz de levar o lojista da entrada da mercadoria até a venda física e digital sem cadastrar a mesma informação em vários lugares.

## Capacidades principais

### ERP / retaguarda
- Organizações, pessoas jurídicas, marcas, lojas e locais de estoque.
- Funcionários, usuários, perfis, papéis e permissões por loja/escopo.
- Catálogo, categorias hierárquicas, atributos, variantes, SKUs e códigos de barras.
- Ledger de estoque, saldos, inventários, ajustes, transferências e recebimentos.
- Compras e fornecedores.
- Vendas, pagamentos, devoluções, trocas e cancelamentos.
- Caixa: abertura, suprimento, sangria, reconciliação e fechamento diário.
- Atribuição de vendedor e cálculo auditável de comissão.
- Clientes e futura evolução para CRM/fidelidade.
- Relatórios operacionais e gerenciais.

### Aplicativo mobile
- Cadastro de produto direto pelo celular.
- Leitura de código de barras com a câmera.
- Geração de SKU/código interno para itens sem código do fabricante.
- Fotografia de produto e upload de mídia.
- Consulta de estoque e inventário.
- Recebimento e transferência entre lojas.
- Dashboard para vendedores, gerentes e proprietários.
- Fluxos offline quando houver justificativa operacional.

### IA aplicada ao comércio
- Melhoria de fotos preservando o arquivo original.
- Remoção/limpeza de fundo e geração de derivados para catálogo.
- Geração opcional de imagens com modelo virtual usando a roupa real, sempre com proveniência e revisão humana.
- Sugestões de título, descrição, categoria, tags e conteúdo por canal.
- IA nunca é autoridade para preço, estoque, cálculo financeiro, regras fiscais, autorização ou invariantes.

### PIM / OMS / Omnichannel
- Produto canônico como fonte de verdade.
- Listings por canal e overrides específicos.
- Sites próprios.
- Adapters planejados para TikTok Shop, Mercado Livre, Shopee e outros canais.
- Ingestão de pedidos e sincronização de estoque.
- Reserva de estoque para evitar overselling.
- Validação de requisitos por canal antes de publicar.
- Webhooks e integrações idempotentes.

### Plataforma de sites
- Cliente sem site pode criar um storefront dentro do Mora Core.
- Temas, identidade visual, navegação, homepage, catálogo, categorias e páginas de produto.
- Domínio próprio em fase futura.
- Catálogo/site alimentado pela mesma fonte de dados do ERP.

### SaaS
- Multi-tenancy desde o modelo inicial.
- Uma organização pode ter várias marcas, CNPJs, lojas, depósitos, sites e canais.
- Isolamento forte entre tenants.
- Entitlements e billing do SaaS separados dos pagamentos das vendas do lojista.
- Importação/exportação e estratégia de migração para evitar lock-in agressivo.
- Operação em nuvem com backup, recuperação e observabilidade.

## Direção arquitetural

A arquitetura padrão é um **monólito modular**, com limites fortes entre módulos, sem microserviços prematuros.

```text
                         MORA CORE
                            │
                    Identidade / Tenancy
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
     ERP Core            Commerce           Experiência
        │                   │                   │
 Catálogo/Estoque      PIM / OMS         Admin / Mobile
 Vendas/Caixa          Canais            PDV / Storefront
 Comissões             Integrações       Analytics
        └───────────────────┼───────────────────┘
                            │
                      Application Core
                            │
          ┌─────────────────┼──────────────────┐
          │                 │                  │
      PostgreSQL       Object Storage        Jobs
          │                 │                  │
          └─────────────────┼──────────────────┘
                            │
                        APIs externas
```

Regras centrais:
- regra de negócio fica no domínio/aplicação;
- PostgreSQL participa da integridade com constraints e transações reais;
- estoque é representado por **movimentações append-only + saldo derivado/materializado**, não por alteração arbitrária de quantidade;
- browser e app mobile nunca são autoridade para estoque, preço, comissão, tenant ou permissão;
- integrações externas ficam atrás de adapters;
- secrets nunca ficam no frontend, APK, Git ou logs;
- APIs usam contratos explícitos, semântica HTTP adequada e limites de recursos;
- Kafka, Kubernetes, CQRS, Event Sourcing, Redis, microserviços e outras complexidades entram apenas se um problema real justificar.

## Estrutura do repositório

```text
mora-core/
├── README.md
├── LICENSE
├── AGENTS.md
├── SECURITY.md
├── CONTRIBUTING.md
├── assets/
│   └── brand/
├── docs/
│   ├── index.md
│   ├── discovery/
│   ├── product/
│   ├── architecture/
│   ├── domain/
│   ├── saas/
│   ├── mobile/
│   ├── ai/
│   ├── commerce/
│   ├── integrations/
│   ├── security/
│   ├── qa/
│   ├── devops/
│   ├── operations/
│   ├── data/
│   ├── ux/
│   ├── roadmap/
│   ├── research/
│   ├── project/
│   ├── engineering/
│   └── adr/
└── ... implementação será adicionada incrementalmente
```

## Documentação

Comece pelo [índice da documentação](docs/index.md).

### Produto e escopo
- [Visão executiva](docs/product/executive-overview.md)
- [Visão do produto](docs/product/vision.md)
- [Módulos e capacidades](docs/product/modules.md)
- [Personas e jornadas críticas](docs/product/personas-and-journeys.md)
- [Storefront e criação de sites](docs/product/storefront.md)
- [Roadmap](docs/roadmap/roadmap.md)

### Arquitetura e dados
- [Arquitetura do sistema](docs/architecture/system-architecture.md)
- [Monólito modular](docs/architecture/modular-monolith.md)
- [Multi-tenancy](docs/saas/multitenancy.md)
- [Modelo de domínio](docs/domain/domain-model.md)
- [Estoque](docs/domain/inventory.md)
- [Vendas, caixa e comissões](docs/domain/sales-cash-commissions.md)
- [Fiscal e pagamentos](docs/domain/fiscal-and-payments.md)
- [Contratos de API](docs/architecture/api-contracts.md)
- [Lifecycle, backup e recuperação](docs/data/data-lifecycle.md)

### Mobile, IA e commerce
- [Aplicativo mobile](docs/mobile/mobile-app.md)
- [Códigos de barras, etiquetas e scanner](docs/mobile/barcodes-and-scanning.md)
- [Governança de IA](docs/ai/ai-governance.md)
- [Pipeline de mídia com IA](docs/ai/media-pipeline.md)
- [Taxonomia do catálogo](docs/commerce/catalog-taxonomy.md)
- [Omnichannel, PIM e OMS](docs/commerce/omnichannel.md)
- [Marketplaces e canais](docs/integrations/marketplaces.md)
- [Estratégia de integração com Bling](docs/integrations/bling.md)

### Engenharia, QA, segurança e operação
- [Engineering Constitution](docs/engineering/constitution.md)
- [Arquitetura de segurança](docs/security/security-architecture.md)
- [Privacidade e LGPD](docs/security/privacy-lgpd.md)
- [Estratégia de QA e testes](docs/qa/test-strategy.md)
- [DevOps, DevSecOps e release engineering](docs/devops/devsecops.md)
- [SRE, observabilidade e AIOps](docs/operations/sre-aiops.md)
- [Performance e capacidade](docs/operations/performance-capacity.md)
- [Incidentes e disaster recovery](docs/operations/incident-recovery.md)
- [Acessibilidade e UX](docs/ux/accessibility-and-design.md)

### Planejamento e pesquisa
- [Discovery operacional](docs/discovery/operational-discovery.md)
- [Benchmark competitivo](docs/research/competitive-benchmark.md)
- [Riscos e questões em aberto](docs/roadmap/risks-and-open-questions.md)
- [ADRs](docs/adr/README.md)

## Padrão de engenharia

> **Não otimizar para parecer sofisticado. Otimizar para resistir a uma auditoria séria.**

A [Engineering Constitution](docs/engineering/constitution.md) exige, entre outros pontos:
- regra de negócio primeiro;
- secure-by-design e secure-by-default;
- isolamento explícito de tenant;
- testes que provem comportamento;
- núcleo determinístico para estoque, dinheiro e autorização;
- IA tratada como entrada não confiável;
- mudanças pequenas, commits lógicos e push remoto real;
- qualificação de release pelo SHA exato;
- governança de dependências e supply chain;
- documentação atual como fonte de verdade;
- auditoria independente em milestones importantes.

## Estratégia incremental de implementação

1. **Fundação:** identidade, organizações, lojas, permissões, catálogo, variantes, ledger de estoque e auditoria.
2. **Mobile operacional:** consulta, scanner, entrada de produto, fotos, etiquetas e inventário.
3. **Integração dos sites:** os storefronts passam a consumir catálogo/estoque do Mora Core.
4. **Vendas/PDV:** vendas, pagamentos, caixa, trocas, devoluções e comissões.
5. **Analytics:** giro, aging, curva ABC, performance por vendedor, loja e canal.
6. **IA:** mídia e conteúdo assistidos, com proveniência, limites e revisão humana.
7. **Omnichannel:** adapters, pedidos externos e sincronização de estoque.
8. **SaaS:** onboarding, billing/entitlements, import/export, suporte e hardening multi-tenant.

Veja o [roadmap completo](docs/roadmap/roadmap.md).

## Vocabulário de status

| Status | Significado |
| --- | --- |
| `implementado` | Existe e há evidência de comportamento correto. |
| `parcial` | Parte dos critérios ainda não foi concluída. |
| `experimental` | Existe, mas ainda não é uma capacidade estável/suportada. |
| `planejado` | Está documentado, mas ainda não foi implementado. |
| `adiado` | Foi postergado conscientemente. |
| `não validado` | Pode existir, mas falta evidência suficiente. |

## Licença

Mora Core é software proprietário. Consulte [LICENSE](LICENSE).

**© 2026 Mora Core. Todos os direitos reservados.**
