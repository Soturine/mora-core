# Landscape de Mercado — E-commerce, Omnichannel e Hubs

> **Status:** snapshot estratégico atualizado em 26/08/2026. Não é comparação comercial definitiva. Funcionalidades, preços, integrações e políticas mudam.

## Objetivo

Complementar o [benchmark de ERP/PDV](competitive-benchmark.md) analisando referências de:

- criação de loja virtual;
- omnichannel;
- POS conectado;
- hubs de marketplace;
- ecossistemas de apps/integradores.

O objetivo não é fazer Mora Core copiar Shopify, Nuvemshop, Tray ou ANYMARKET. É entender expectativas de mercado e identificar onde a proposta integrada do Mora Core pode ter valor.

---

# 1. Shopify + Shopify POS

## Posicionamento atual

A documentação oficial do Shopify POS apresenta capacidades de:

- omnichannel selling;
- inventory management;
- staff management;
- checkout;
- produtos/clientes;
- reporting/analytics;
- hardware e payments.

O fluxo omnichannel destaca experiências como comprar online e retirar na loja, comprar na loja e enviar, e manter back office conectado entre físico e online.

Fontes:

- https://www.shopify.com/pos
- https://www.shopify.com/pos/features
- https://www.shopify.com/pos/omnichannel

## Aprendizados para Mora Core

### Back office conectado

A expectativa moderna é que site e físico não sejam sistemas independentes.

### Staff permissions

Varejo conectado continua precisando de papéis/permissões — reforça nosso modelo de seller/cashier/manager.

### Inventory across locations

Multi-location é parte central de omnichannel.

### Hardware/ecossistema

Experiência de POS inclui dispositivo/pagamento/suporte, não apenas interface.

## O que não copiar cedo

Shopify possui ecossistema global enorme. Mora Core não deve tentar reproduzir app store, pagamentos próprios e logística global antes de validar seu nicho.

---

# 2. Nuvemshop

## Posicionamento atual

A página oficial de funcionalidades em 2026 apresenta:

- loja online;
- integração com Instagram, WhatsApp, TikTok e Pinterest;
- marketplaces como Mercado Livre, Amazon e Shopee;
- ponto de venda conectado ao estoque da loja online;
- integração com ERPs;
- payments/envios/marketing/chat;
- ecossistema de apps.

O guia oficial atualizado em julho de 2026 mostra onboarding de criação de loja a partir de cadastro, layout, produtos/fotos, pagamentos, envio e primeira venda.

Fontes:

- https://www.nuvemshop.com.br/funcionalidades
- https://www.nuvemshop.com.br/canais/marketplaces
- https://atendimento.nuvemshop.com.br/pt_BR/primeiros-passos/como-montar-minha-loja-online-passo-a-passo

## Aprendizados

### Onboarding precisa parecer simples

Criar loja não pode exigir conhecimento de DNS/HTML/ERP.

### Loja própria + marketplaces

Catálogo integrado é expectativa clara.

### Ecossistema

Integrações especializadas reduzem necessidade de construir tudo nativamente.

## Oportunidade Mora Core

A diferença pretendida é começar **antes** do e-commerce, no recebimento físico:

```text
mercadoria
→ scanner/câmera
→ grade
→ estoque
→ IA
→ site/canais
```

Isso precisa ser mais rápido que cadastrar primeiro numa plataforma de e-commerce e depois reconciliar o ERP.

---

# 3. Tray

## Posicionamento atual

A Tray divulga em 2026 uma plataforma de e-commerce com integração direta com mais de 30 marketplaces, incluindo TikTok Shop, Mercado Livre e Shopee, além de amplo ecossistema de ERPs e outras integrações.

Fontes:

- https://tray.com.br/
- https://tray.com.br/marketplace
- https://tray.com.br/integracoes

## Aprendizados

### Marketplace breadth custa manutenção

Suportar muitos canais é diferencial comercial, mas implica grande superfície de contratos mutáveis.

Mora Core deve começar com poucos adapters muito bons.

### Integração com ERP existente

A Tray mostra que plataformas de commerce podem coexistir com ERP. Mora Core também deve permitir integração gradual em vez de exigir migração total no primeiro dia.

### Templates/ecossistema

Site builder maduro precisa de temas, integração e suporte de implantação — não só um gerador de HTML.

---

# 4. ANYMARKET

## Posicionamento atual

O ANYMARKET se posiciona como hub de marketplace para operações de escala, centralizando catálogo, estoque, preço e pedido. Em sua comunicação atual informa centenas de integrações entre marketplaces, ERPs, plataformas de e-commerce e outras ferramentas.

Fonte:

- https://anymarket.com.br/

## Aprendizados

### Hub é operação crítica

Marketplace hub precisa tratar:

- catálogo;
- stock sync;
- preço;
- pedido;
- rate limits;
- disponibilidade;
- observabilidade;
- suporte.

### Confiabilidade é produto

Empresas compram hub para reduzir erro/retrabalho; portanto “tem integração” não basta. O adapter precisa ser confiável e diagnosticável.

### Especialização tem valor

Mora Core não precisa superar um hub especializado em breadth. Pode integrar poucas plataformas diretamente e deixar possibilidade de integrar um hub futuramente.

---

# 5. Categorias de concorrência

Mora Core cruza várias categorias:

```text
ERP/PDV
├── Bling
├── Omie
├── Olist/Tiny
├── Linx
├── TOTVS
├── MarketUP
└── Nex

E-commerce/Storefront
├── Shopify
├── Nuvemshop
└── Tray

Marketplace Hub
├── ANYMARKET
└── outros hubs

Mora Core
└── hipótese: operação física + mobile intake + ERP core + commerce + IA + site
```

Isso cria oportunidade, mas também risco de escopo excessivo.

---

# 6. Feature parity não é estratégia

Seria erro criar backlog:

```text
Shopify tem X → implementar X
Nuvemshop tem Y → implementar Y
TOTVS tem Z → implementar Z
```

Prioridade vem de problema real.

## Capacidade esperada vs diferencial

### Table stakes conforme maturidade

- catálogo;
- estoque;
- venda;
- permissions;
- site;
- mobile;
- relatórios;
- marketplace integrations.

### Hipótese diferenciadora

```text
Entrada da mercadoria pelo celular
→ scan / gerar código
→ fotos
→ IA segura
→ grade/estoque
→ etiqueta
→ publicação multicanal
→ venda física/digital
→ caixa/comissão/analytics
```

A integração dessa jornada pode ser diferencial se reduzir tempo e erro.

---

# 7. Build vs integrate

Para cada capability, perguntar:

| Capability | Construir? | Integrar? | Por quê? |
| --- | --- | --- | --- |
| Catálogo/estoque core | tendência: construir | — | núcleo diferenciador/invariantes |
| Fiscal | inicialmente não | provider/Bling | alto risco regulatório |
| Pagamentos | não processar cartão próprio | provider/TEF | segurança/compliance |
| Site | construir engine controlado | integrações complementares | parte da proposta |
| Marketplaces | adapters selecionados | hub pode ser opção futura | custo de breadth |
| IA | gateway + providers | modelos externos | evitar treinar infra cedo |
| Email/push | integrar | provider | commodity |

A tabela é direção, não ADR final.

---

# 8. SaaS onboarding benchmark

Plataformas maduras reduzem tempo até primeira venda.

Mora Core precisa medir:

- tempo para criar organização;
- importar catálogo;
- conectar equipe;
- publicar primeiro produto;
- criar site;
- conectar canal;
- realizar primeira venda.

O onboarding ideal deve esconder complexidade técnica sem esconder decisões comerciais importantes.

---

# 9. Sites: onde Mora Core precisa ser bom

Não é necessário superar Shopify em todos os aspectos. O storefront do Mora Core precisa entregar bem:

- implantação simples;
- identidade visual;
- catálogo/variantes;
- performance;
- SEO;
- mobile;
- acessibilidade;
- domínio;
- WhatsApp/checkout futuro;
- estoque integrado;
- novidades/mais vendidos reais;
- atualização sem cadastrar duas vezes.

---

# 10. Marketplace: onde Mora Core precisa ser bom

Antes de aumentar quantidade de canais:

- mapping;
- compliance;
- publicação;
- erro acionável;
- estoque;
- pedidos;
- fiscal;
- fulfillment;
- devolução;
- reconciliation;
- token health;
- metrics/runbook.

Suportar três canais de forma confiável vale mais que 30 badges de integração sem qualidade.

---

# 11. Pesquisa futura

A cada milestone de commerce, reavaliar:

- plataformas brasileiras;
- hubs;
- ERPs especializados em moda;
- marketplaces prioritários;
- apps de photo/AI commerce;
- mobile inventory/POS;
- onboarding/pricing/packaging.

Registrar snapshot/data porque mercado muda rapidamente.

## Relacionados

- [Benchmark ERP/PDV](competitive-benchmark.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Storefront](../product/storefront.md)
- [Roadmap](../roadmap/roadmap.md)
