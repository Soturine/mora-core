# Storefront e Criação de Sites

## Objetivo

Permitir que uma organização sem site crie uma presença digital integrada ao catálogo/estoque do Mora Core, sem duplicar cadastro.

## Modelo

`Organization → Brand → Website → Theme → Navigation/HomeSections → CatalogPublication`.

Uma organização pode possuir várias marcas e sites.

## Capacidades

- subdomínio inicial da plataforma;
- domínio customizado em fase futura;
- logo, cores, tipografia e tokens de marca;
- temas seguros: editorial, minimal, boutique, kids, etc.;
- homepage com blocos configuráveis;
- categorias dinâmicas;
- página individual de produto;
- variantes reais cor/tamanho/modelo;
- galeria e zoom na página do produto;
- SEO, OpenGraph e dados estruturados quando verdadeiros;
- WhatsApp/checkout conforme estágio do produto;
- analytics sem expor PII desnecessária.

## Homepage

Blocos possíveis: Hero, Categorias, Novidades, Mais vendidos, Destaques, Promoções, Instagram, Loja física, Localização e WhatsApp.

**Novidades** = regra temporal (`publishedAt`/`firstAvailableAt`).

**Mais vendidos** = vendas reais por janela definida no backend, nunca quantidade em estoque.

**Destaques** = curadoria manual.

## Segurança

Sem HTML/CSS/JS arbitrário de lojista no MVP. Customização por componentes e tokens evita XSS e suporte impossível.

## SEO

Produtos demonstrativos não devem gerar falsas Offers. Páginas publicadas/verified podem ser indexáveis; drafts/inativos não.
