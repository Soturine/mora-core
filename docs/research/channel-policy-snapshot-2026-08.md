# Snapshot de Políticas de Canais — Agosto/2026

> **Status:** snapshot de pesquisa, não regra eterna. O Mora Core deve revalidar políticas oficiais no momento de cada publicação/integração. Atualizado em 26/08/2026.

## Objetivo

Registrar requisitos externos relevantes que influenciam o `Channel Compliance Engine`, especialmente mídia, IA e moda.

Este documento é deliberadamente separado do domínio porque políticas externas mudam sem que o Product Truth do Mora Core mude.

---

# TikTok Shop Brasil

## Listing de produto

Política oficial consultada:

- https://seller-br.tiktok.com/university/essay?knowledge_id=6483182812759824&lang=en

Snapshot 26/08/2026:

- até nove imagens quadradas por listing;
- escolher uma imagem principal;
- principal deve mostrar a visão física frontal do produto;
- fundo principal branco puro;
- imagens com pelo menos 600×600;
- imagens precisam representar com precisão o item vendido;
- placeholders/renderizações digitais que substituem representação real não são aceitos;
- imagens adicionais devem mostrar outros ângulos/detalhes e podem mostrar uso/variações/cenas estilizadas conforme regras;
- roupa infantil não deve usar modelos ou manequins; deve ser mostrada deitada/flat em fundo branco;
- imagens não podem sugerir efeitos/material/aparência que o produto não possui.

Essas regras reforçam que `AI_ON_MODEL` **não pode ser asset universal** para listing.

## Conteúdo gerado por IA

Política oficial consultada:

- https://seller-br.tiktok.com/university/essay?knowledge_id=2324329879832321&lang=pt-BR

Snapshot:

- conteúdo total/significativamente gerado por IA deve ser divulgado de forma apropriada;
- IA não deve enganar ou criar falsa impressão do produto;
- não alterar tamanho, cor, material, forma ou características de modo que deixe de corresponder ao item real;
- não criar falsos endossos/impersonation;
- respeitar propriedade intelectual.

## Tabela de tamanho

Política oficial:

- https://seller-br.tiktok.com/university/essay?knowledge_id=3750784971196177

Em 2026, requisitos obrigatórios de size chart atingem categorias relevantes de moda feminina, masculina, calçados, esportes, infantil e bebê/maternidade conforme subcategoria/sistema.

O adapter deve consultar requisitos atuais por categoria antes de publicar.

## Implicação no Mora Core

Exemplo de validation result:

```text
TikTok Shop — Moda Infantil
✓ produto verificado
✓ size data
✓ imagem real
✓ fundo branco
✕ asset principal AI_ON_MODEL
✕ manequim/modelo infantil

BLOCKED
Ação: selecionar/gerar foto flat real compatível
```

---

# Mercado Livre Brasil

## Integração/publicação

Documentação oficial de developers deve ser revalidada na implementação:

- https://developers.mercadolivre.com.br/pt_br/publicacao-de-produtos

A pesquisa de 2026 identificou evolução do modelo de publicação para `User Products`, que deve ser considerada por novas integrações.

## Mídia/moda

Há orientações por categoria para fotos e apresentação de moda. O Mora Core não deve congelar uma única regra geral de imagem para todas as categorias.

O adapter/compliance engine precisa buscar no momento adequado:

- requisitos de categoria;
- atributos obrigatórios;
- política de imagem;
- variações;
- GTIN;
- dimensões/peso;
- logística.

## Implicação

`ChannelListing` guarda categoria externa e o ruleset aplicado/versionado. Mudança de política pode tornar listing antigo válido e edição nova inválida; tratar como contrato externo.

---

# Shopee Brasil

## Estado da pesquisa

A Shopee possui ecossistema/Open Platform para integrações, porém o portal técnico não ficou plenamente acessível na pesquisa pública anterior.

Portanto:

> **Não congelar endpoints, scopes, versões ou política de mídia da Shopee sem onboarding/documentação oficial atual da conta de parceiro.**

Orientações comerciais públicas podem recomendar fotos realistas/claras, mas a implementação deve usar contrato oficial do seller/developer correspondente.

## Implicação

O Mora Core prepara `ShopeeAdapter`, mas marca requisitos concretos como `não validados` até acesso oficial.

---

# Regras internas do Compliance Engine

Cada ruleset deve ter metadata:

```text
provider
country/market
category/externalCategoryId
rulesetVersion
sourceUrl
effective/checkedAt
checks[]
```

## Checks possíveis

- título obrigatório/tamanho;
- descrição;
- attributes;
- GTIN;
- size chart;
- peso/dimensões;
- imagem principal;
- resolução/aspect;
- `aiGenerated`;
- categoria infantil;
- mídia real obrigatória;
- quantidade de imagens;
- logística/fiscal.

## Resultado

```text
READY
WARNING
BLOCKED
```

Warning não pode ser usado para contornar requisito obrigatório.

---

# Relação com IA

O pipeline pode gerar vários assets:

```text
ORIGINAL
ENHANCED
BACKGROUND_REMOVED
AI_ON_MODEL
SOCIAL_CREATIVE
MARKETPLACE_VARIANT
```

O channel adapter seleciona apenas os compatíveis.

Uma imagem ótima para Instagram pode ser proibida como imagem principal de um marketplace.

---

# Atualização contínua

Antes de publicar versão nova de adapter:

1. revisar documentação oficial;
2. atualizar snapshot/ruleset;
3. criar contract/compliance tests;
4. testar sandbox/conta de desenvolvimento;
5. registrar mudanças no changelog/ADR quando impacto estrutural.

## Relacionados

- [Marketplaces](../integrations/marketplaces.md)
- [Pipeline de mídia](../ai/media-pipeline.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Taxonomia](../commerce/catalog-taxonomy.md)
