# Taxonomia do Catálogo

## Problema

Uma loja pode vender feminino, juvenil, infantil, bebê, enxoval, acessórios, higiene/cuidados e futuras linhas. Não hardcodar duas categorias no frontend.

## Conceitos separados

`Department`, `Category`, `Subcategory`, `Audience`, `AgeRange`, `AttributeDefinition`, `Product`, `ProductVariant`.

Exemplo: vestido infantil de festa = Department Infantil, Category Vestidos, Subcategory Festa, Audience Menina, AgeRange configurável, variantes por cor/tamanho.

## Árvore inicial possível

```text
Feminino
Juvenil / Teen
Infantil
Bebê
Enxoval
Acessórios
Higiene & Cuidados
```

Subcategorias só aparecem quando ativas/publicáveis.

## Category

Campos: `id`, `organizationId`, `parentId?`, `name`, `slug`, `sortOrder`, `active`, `visibleOnWebsite`, `image?`, `description?`, `seo?`.

## Variantes

Não modelar tamanho e cor como listas independentes. Cada combinação válida é `ProductVariant` com SKU, barcode, atributos, preço/override, mídia e disponibilidade.

## Filtros

Derivados dos atributos existentes e do departamento. Não mostrar filtros vazios.

## Rotas storefront

Preferir categorias dinâmicas (`/catalogo/$slug`) e produtos (`/produto/$slug`) com compatibilidade/redirect para URLs históricas quando necessário.
