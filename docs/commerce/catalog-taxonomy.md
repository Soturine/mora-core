# Catálogo, Taxonomia, Atributos e Variantes

> **Status:** arquitetura de catálogo planejada. A árvore inicial reflete a operação observada/discutida, mas categorias reais devem permanecer configuráveis.

## Objetivo

Permitir que o Mora Core represente um catálogo amplo de varejo sem hardcodar “Feminino” e “Infantil” no código, e sem misturar conceitos diferentes em um único campo `category`.

## Princípio

Separar:

```text
Department
Category
Subcategory
Audience
AgeRange
AttributeDefinition
Product
ProductVariant
```

Um produto pode pertencer a uma taxonomia e possuir atributos/segmentos sem transformar cada combinação em uma categoria nova.

---

# Taxonomia inicial da operação de referência

A loja familiar/feminina possui linhas mais amplas do que apenas moda feminina + infantil. Uma estrutura inicial possível:

```text
Feminino
Juvenil / Teen
Infantil
Bebê
Enxoval
Acessórios
Higiene & Cuidados
```

Essa lista **não é enum hardcoded**. O Mora Core deve permitir adicionar depois, por exemplo:

```text
Calçados
Moda Praia
Pijamas
Maternidade
Brinquedos
Perfumaria
```

sem alterar/deployar frontend por causa da nova categoria.

## Exemplos de subárvores

### Feminino

- Vestidos
- Blusas
- Camisetas
- Calças
- Shorts
- Saias
- Conjuntos
- Looks/coleções
- Plus size, se fizer parte da operação real

### Juvenil

- Feminino juvenil
- Masculino juvenil, somente se vendido
- Vestidos
- Camisetas
- Blusas
- Shorts
- Calças
- Conjuntos

### Infantil

- Menina
- Menino
- Vestidos
- Camisetas
- Conjuntos
- Shorts/Bermudas
- Calças
- Festa
- Acessórios

### Bebê

- Bodies
- Macacões
- Conjuntos
- Saída de maternidade
- RN
- Roupas de bebê

### Enxoval

- Mantas
- Toalhas
- Cobertores
- Kits maternidade
- Fraldas/Panos
- Banho

### Acessórios

- Bolsas
- Bijuterias
- Pulseiras
- Laços/Cabelo
- Acessórios infantis
- Acessórios de bebê

### Higiene & Cuidados

- categorias reais de cuidado/higiene comercializadas, confirmadas antes de publicar.

A árvore precisa refletir estoque real; fotos antigas/sazonais não são prova de linha permanente.

---

# `Category`

Modelo conceitual:

```text
Category
├── id
├── organizationId
├── parentId?
├── name
├── slug
├── sortOrder
├── active
├── visibleOnWebsite
├── image?
├── description?
├── seo?
└── timestamps
```

## Invariantes

- `slug` único no escopo definido;
- parent pertence à mesma organização;
- impedir ciclo na árvore;
- categoria inativa não aceita nova publicação conforme policy;
- exclusão de categoria com produtos precisa de migração/arquivamento explícito;
- ordem é editorial, não identidade.

## Árvore profunda

Evitar profundidade ilimitada. Para UX/admin, limitar ou orientar estrutura a poucos níveis úteis.

`Department` pode ser uma categoria raiz ou conceito explícito se o domínio exigir comportamento distinto. Essa decisão vai para ADR/domain spec após Discovery.

---

# Audience e AgeRange

Categoria não deve carregar tudo.

Exemplo:

```text
Produto: Vestido festa tule rosa
Department: Infantil
Category: Vestidos
Subcategory: Festa
Audience: Menina
AgeRange: 4–10 anos, se aplicável
```

Outro:

```text
Produto: Kit toalhas bebê
Department: Enxoval
Category: Banho
Audience: Bebê
AgeRange: RN/bebê
```

Outro:

```text
Produto: Pulseira
Department: Acessórios
Category: Bijuterias
Audience: Feminino
```

## Idade

Faixas não devem ser strings rígidas globais, pois fornecedores/tamanhos variam.

Modelo possível:

```text
AgeRange
minAgeMonths?
maxAgeMonths?
label
```

Segmentos conceituais podem incluir `baby`, `kids`, `teen`, `adult`, mas não substituir medida/configuração real.

---

# Atributos

## `AttributeDefinition`

Exemplos:

- Cor
- Tamanho
- Numeração
- Modelo
- Material
- Estampa
- Faixa etária
- Voltagem em outro varejo futuro

Campos possíveis:

```text
id
organizationId
name
code
dataType
variantDefining
filterable
requiredForCategories[]
allowedValues?
unit?
active
```

## Atributo de variante vs produto

`material=viscose` pode ser do produto; `color=preto` pode definir variante.

Não transformar todos os atributos em variante.

## Valores controlados

Cor/tamanho podem usar controlled vocabulary para filtros, preservando label de fornecedor quando necessário.

Exemplo:

```text
canonicalColor: BLUE
label: Azul Marinho
```

Não inferir cor comercial por IA sem confirmação.

---

# Produto e variante

## `Product`

Identidade conceitual compartilhada entre suas variantes.

## `ProductVariant`

Combinação vendável real.

Exemplo:

```text
Vestido Helena
├── Azul / P      SKU VES-...-AZ-P
├── Azul / M      SKU VES-...-AZ-M
├── Preto / P     SKU VES-...-PR-P
└── Preto / M     SKU VES-...-PR-M
```

Cada variante pode ter:

- SKU;
- barcode/GTIN;
- preço/override;
- mídia;
- stock balance;
- status;
- external mappings.

## Não usar arrays como verdade de estoque

Evitar:

```text
sizes = [P,M,G]
colors = [Azul,Preto]
```

como representação de disponibilidade, porque isso cria combinações inexistentes.

A UX pode mostrar matriz, mas persiste apenas variantes válidas.

---

# Cadastro rápido de grade

Admin/mobile pode oferecer:

```text
           P   M   G   GG
Azul       ✓   ✓   ✓   —
Preto      ✓   ✓   —   —
Bege       —   ✓   ✓   ✓
```

Ao confirmar:

- criar variantes faltantes;
- manter IDs de existentes;
- impedir remoção destrutiva se há histórico;
- solicitar quantidade/identificador por combinação.

---

# SKU e identificadores

SKU é interno e único conforme policy da organização.

Identificadores externos:

- GTIN/EAN;
- supplier reference;
- marketplace SKU mapping;
- barcode interno.

Não inventar GTIN oficial. Ver [Barcode](../mobile/barcodes-and-scanning.md).

---

# Mídia

Produto possui mídia geral e variante pode ter mídia específica.

```text
Product media
├── editorial geral

Variant Azul
├── frente
├── costas
└── detalhe
```

Storefront seleciona mídia específica da variante com fallback para geral.

---

# Publicação

Separar lifecycle de cadastro e canal.

```text
Product DRAFT
→ READY
→ PUBLISHED
→ ARCHIVED
```

E cada `ChannelListing` possui seu status próprio.

Produto em estoque pode estar `DRAFT` no site.

## `publishedAt` e Novidades

Não usar `updatedAt` como novidade. Migração antiga não pode transformar produto velho em novo só por importar hoje.

Candidatos:

- `publishedAt`;
- `firstAvailableAt` quando estoque integrado e regra escolhida.

A regra fica no backend e documentada.

---

# Mais vendidos

É analytics de venda, não taxonomia e não estoque.

```text
SaleItem elegível
→ janela
→ ranking
```

Nenhum `isBestSeller=true` automático baseado em quantidade armazenada.

---

# Filtros

Derivar de atributos existentes e dados publicados.

### Feminino

- categoria;
- tamanho;
- cor;
- preço;
- novidade.

### Infantil

- categoria;
- tamanho;
- idade;
- cor;
- preço.

### Bebê

- categoria;
- tamanho;
- faixa etária;
- cor.

### Enxoval

- categoria;
- tipo;
- cor.

### Acessórios

- categoria;
- tipo;
- cor.

Não renderizar filtros vazios nem valores inexistentes.

## Facets

Futuramente backend pode retornar facets/counts para evitar frontend descobrir opções percorrendo catálogo inteiro.

---

# Storefront navigation

Desktop sugerido:

```text
INÍCIO | CATÁLOGO ▾ | NOVIDADES | MAIS VENDIDOS | GALERIA | SOBRE | LOCALIZAÇÃO
```

Dropdown/mega menu acessível pode listar departamentos reais.

Mobile usa hierarquia/accordion navegável por toque/teclado.

A navegação vem da taxonomia, não de arrays duplicados em componentes.

---

# Rotas

Preferir arquitetura dinâmica:

```text
/catalogo/$slug
/produto/$slug
```

Exemplos:

```text
/catalogo/feminino
/catalogo/juvenil
/catalogo/infantil
/catalogo/bebe
/catalogo/enxoval
/catalogo/acessorios
/catalogo/higiene-e-cuidados
```

Rotas históricas como `/feminino`/`/infantil` podem redirecionar para preservar links/SEO.

## Slug

- estável;
- único;
- redirect histórico quando renomeado;
- não usar nome como primary key.

---

# SEO

Categoria publicada pode ter:

- title;
- description;
- canonical;
- OpenGraph;
- conteúdo editorial limitado;
- index/noindex policy.

Não criar milhares de páginas faceted indexáveis automaticamente sem estratégia de SEO.

---

# Dados demonstrativos

Enquanto sites usam protótipo:

```text
dataStatus = verified | demonstration
```

Informações inferidas de foto não viram fatos comerciais.

Para demo:

- “Consulte o valor”;
- “Consulte tamanhos e cores disponíveis”;
- sem falsa oferta schema.org;
- não chamar produto de mais vendido sem vendas.

---

# Multi-tenant

Taxonomia é tenant-owned. Futuro pode haver templates de categorias, mas copiar template cria categorias da organização; não compartilhar row mutável entre tenants inadvertidamente.

---

# Importação

Ao importar ERP:

- mapear categorias externas;
- não depender da mesma árvore;
- permitir preview/mapping;
- preservar external IDs;
- evitar duplicar categoria por diferenças de maiúscula/acentuação sem confirmação.

---

# IA

IA pode sugerir:

- categoria;
- atributos;
- cor;
- tags.

Sugestão precisa de schema/confidence e confirmação. IA não cria `ProductVariant`/estoque silenciosamente a partir de uma foto.

---

# Validações

- category cycle;
- duplicate slug;
- invalid attribute type;
- invalid variant combination;
- duplicate SKU;
- GTIN format/check conforme symbology;
- inactive category publish;
- cross-tenant reference.

---

# Testes essenciais

- criar árvore;
- mover categoria sem ciclo;
- novo departamento sem deploy específico;
- variante matrix;
- filtro só com dados existentes;
- route/slug redirect;
- demo vs verified;
- mídia por variante;
- tenant isolation;
- import mapping;
- `publishedAt` não muda por edição comum.

## Questões do Discovery

- quais departamentos são recorrentes de verdade?
- masculino juvenil existe na loja familiar?
- plus size?
- coleções/estações?
- como tamanhos de bebê são representados?
- quais atributos vêm de fornecedor?
- categorias compartilhadas entre as duas marcas?

## Relacionados

- [Storefront](../product/storefront.md)
- [Modelo de domínio](../domain/domain-model.md)
- [Mobile](../mobile/mobile-app.md)
- [IA/mídia](../ai/media-pipeline.md)
