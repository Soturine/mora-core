# Storefront e Criação de Sites

> **Status:** produto/arquitetura planejados.

## Objetivo

Permitir que uma organização sem site crie uma loja online integrada ao Mora Core e que organizações com múltiplas marcas mantenham vários storefronts sem duplicar catálogo/estoque.

## Princípio

O Storefront não é um editor de código arbitrário. É um motor de componentes seguros alimentado pelo mesmo catálogo canônico do ERP.

```text
Mora Core
├── Catálogo
├── Estoque
├── Preço
├── Mídia
├── Publicação
└── Websites
        ↓
   Storefront Engine
        ↓
 site da marca
```

## Entidades

```text
Website
├── id
├── organizationId
├── brandId
├── name
├── slug/subdomain
├── customDomain?
├── themeId
├── publicationStatus
└── settings

BrandTheme
├── logo
├── color tokens
├── typography tokens
├── radius/spacing tokens
├── photographyStyle
└── layoutPreset
```

`Website`, `Brand` e `Store` são conceitos diferentes. Uma marca pode ter um site e várias lojas; uma organização pode ter várias marcas/sites.

## Onboarding de site

```text
Criar site
→ selecionar marca
→ nome/logo
→ identidade visual
→ WhatsApp/contatos
→ localização/social
→ escolher tema
→ montar homepage
→ selecionar canais/categorias
→ preview
→ publicar subdomínio
→ opcionalmente conectar domínio próprio
```

## Templates

Temas possíveis: Minimal, Editorial, Boutique, Modern, Kids, Luxury ou outros. São presets de design system, não bases de código independentes.

Evitar CSS/JS arbitrário no início para reduzir XSS, quebra de layout, suporte e incompatibilidade de upgrades.

## Homepage componível

Blocos permitidos, conforme template/capabilities:

- Hero;
- categorias;
- novidades;
- mais vendidos;
- destaques;
- promoções;
- coleções;
- Instagram/social;
- prova social;
- localização;
- WhatsApp;
- conteúdo institucional.

A ordem pode ser configurável, mas componentes continuam controlados pela plataforma.

## Catálogo dinâmico

Não criar páginas manuais para cada departamento. Categorias e árvore de navegação vêm do catálogo:

```text
/catalogo/feminino
/catalogo/juvenil
/catalogo/infantil
/catalogo/bebe
/catalogo/enxoval
/catalogo/acessorios
```

A lista real depende de `active`, `visibleOnWebsite` e produtos/publicação. Novas categorias não devem exigir deploy do frontend.

## Página de produto

Cada produto publicado possui URL estável:

```text
/produto/$slug
```

A página suporta:

- breadcrumb;
- nome/categoria;
- galeria;
- variantes válidas;
- tamanho/cor/modelo quando aplicável;
- disponibilidade;
- descrição;
- zoom desktop e viewer mobile;
- CTA de WhatsApp/checkout futuro;
- produtos relacionados;
- SEO específico.

### Zoom

Zoom avançado pertence à página individual, não ao card de catálogo. Desktop pode usar hover/focus com região ampliada + viewer; mobile usa pinch/pan/double tap no viewer, preservando scroll natural fora dele.

## Commerce conversacional integrado ao site

O site deve funcionar como ponto de entrada para o mesmo commerce agent do WhatsApp, sem duplicar regras.

CTAs possíveis:

```text
[ Comprar pelo WhatsApp ]
[ Perguntar sobre este produto ]
[ Encontrar algo parecido ]
[ Enviar uma foto ]
[ Avise-me quando voltar ]
[ Quero pedir este estilo ]
```

### Contexto seguro

Ao abrir uma conversa, o site envia somente contexto identificável/assinado. O backend reconsulta:

- produto;
- variante;
- preço;
- publicação;
- disponibilidade;
- organização.

Nunca confiar em preço/estoque vindos do browser.

### Busca por imagem

O cliente pode enviar uma foto no site.

```text
upload validado
→ AI/visual features
→ tenant-scoped search
→ produtos SOMENTE da mesma Organization
```

Se não houver resultado:

> “Ainda não encontrei algo assim no nosso catálogo. Quer registrar o que procura para a equipe avaliar?”

Pode criar `CustomerRequest` com consentimento, sem prometer que a loja irá comprar.

### Sem estoque

A UI não precisa simplesmente mostrar “indisponível”. Pode oferecer, conforme policy:

- mesma variante em outra loja da organização;
- outra cor/tamanho válido;
- alternativas semelhantes da organização;
- back-in-stock;
- pedido/encomenda para avaliação;
- falar com atendente.

Nunca sugerir concorrentes ou produtos de outro tenant.

## Carrinho e WhatsApp

Dois fluxos podem coexistir:

```text
Produto → WhatsApp → agente monta Cart
```

ou

```text
Site → Cart → Finalizar pelo WhatsApp
```

O carrinho é canônico no Mora Core. WhatsApp e site são interfaces/canais.

## Canais próprios como continuidade de compra

Se a mesma `Organization` possui uma loja oficial conectada em Mercado Livre, Shopee ou TikTok Shop, o storefront/agente pode oferecer esse listing quando uma policy comercial permitir.

Pré-condições:

- mesmo `organizationId`;
- `ChannelConnection` autorizada;
- listing ativo;
- preço/frete/status consultados do canal apropriado.

O storefront **não é comparador de preço de concorrentes**.

## Novidades

Não usar `updatedAt` como proxy de novidade. Regra deve considerar `publishedAt` ou `firstAvailableAt` conforme definição futura.

```text
published = true
active = true
ORDER BY publishedAt DESC
```

## Mais vendidos

Nunca derivar de estoque atual. Deve vir de `SaleItem` elegível em janela definida pelo backend.

## Destaques

Curadoria manual, conceito diferente de novidade, promoção e mais vendidos.

## Dados demonstrativos

Durante protótipo, distinguir `verified` e `demonstration`. Informações não verificadas não podem aparecer como preço/estoque/promoção real. Produtos demo podem usar “Consulte o valor” e “Consulte disponibilidade”.

## SEO

Produto publicado/real pode possuir title/meta/canonical/OpenGraph/schema.org conforme dados verificados. `Offer` só quando preço/disponibilidade são reais. Produto demonstrativo não deve fingir oferta comercial indexável.

## Performance

- imagens responsivas;
- lazy loading secundário;
- prioridade da imagem principal;
- dimensões para evitar CLS;
- CDN;
- cache público apropriado;
- não pré-carregar todas as imagens grandes;
- prefetch medido;
- evitar biblioteca pesada quando componente simples atende.

Busca visual/upload precisa de limites de tamanho, formatos e processamento assíncrono quando necessário.

## Acessibilidade

- semântica HTML;
- navegação por teclado;
- foco visível;
- labels;
- alt text;
- contraste;
- zoom de navegador não bloqueado;
- touch targets adequados;
- reduced motion;
- modal/viewer com Escape, focus management e restore focus;
- fallback textual para recursos baseados em imagem;
- não exigir WhatsApp como único caminho de contato quando a página precisa continuar acessível.

## Segurança

- sem secrets no storefront;
- descrições não entram como HTML arbitrário;
- URLs e mídia validadas;
- CSP/headers quando aplicável;
- cookies/sessões seguros se checkout/login existir;
- frontend nunca consulta Bling/marketplace diretamente;
- Catalog API expõe somente dados públicos permitidos;
- upload do cliente é não confiável;
- busca/recomendação carrega `organizationId` derivado do website/host configurado, não do input livre;
- IDs de produto de outro tenant falham fechado.

## SaaS e domínio

Primeiro pode existir subdomínio da plataforma. Custom domain exige verificação de ownership, TLS e operação segura de DNS/certificados.

O host/domínio resolve inequivocamente para `Website → Organization/Brand`. Cache e CDN também precisam dessa fronteira.

## Integração com os sites atuais

A migração desejada é:

```text
site com dados estáticos
→ repository boundary
→ Catalog API Mora Core
→ dados administrados no Core
```

Sem reescrever o site inteiro antes do backend existir.

A primeira evolução comercial pode ser:

```text
site atual
→ página individual de produto
→ disponibilidade real
→ WhatsApp contextualizado
→ agente/handoff
```

antes de checkout próprio completo.

## Analytics

Eventos possíveis:

- product viewed;
- variant selected;
- WhatsApp CTA clicked;
- visual search started;
- recommendation shown;
- back-in-stock requested;
- CustomerRequest created;
- add to cart;
- checkout/handoff.

Não coletar PII/sensibilidade além da finalidade.

## Relacionados

- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda, Encomendas e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [Taxonomia](../commerce/catalog-taxonomy.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Mídia/IA](../ai/media-pipeline.md)
- [Multi-tenancy](../saas/multitenancy.md)
