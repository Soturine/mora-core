# Pipeline de Mídia e IA

> **Status:** arquitetura planejada. Políticas de cada canal devem ser revalidadas no momento da integração.

## Objetivo

Permitir que o lojista fotografe um produto real uma única vez e gere derivados úteis para site, catálogo e canais externos, sem perder fidelidade nem apagar a origem.

## Princípio central: Product Truth vs Generated Content

**Product Truth** contém fatos reais e verificáveis do item: cor, tecido, composição, dimensões, tamanho, estampa, preço, estoque, SKU, categoria e fotos originais.

**Generated Content** contém representações derivadas: recorte, fundo limpo, texto comercial, imagem com modelo virtual, social creative, versão por marketplace.

IA nunca altera silenciosamente Product Truth.

## Pipeline

```text
Captura/upload
→ validação de formato/tamanho/dimensões
→ remoção de metadata sensível quando apropriado
→ armazenamento do ORIGINAL
→ job assíncrono
→ resize/compress/thumbnail
→ derivados determinísticos
→ IA opcional
→ validação automática
→ revisão humana quando exigida
→ publicação por canal
```

## Asset original é imutável

Nunca sobrescrever o arquivo recebido. Derivados apontam para `sourceAssetId`.

```text
MediaAsset
├── id
├── organizationId
├── productId/variantId
├── type
├── sourceAssetId?
├── storageKey
├── mimeType
├── width/height
├── checksum
├── aiGenerated
├── provider/model/version?
├── transformationMetadata
├── approvalStatus
├── approvedBy?
└── timestamps
```

Tipos iniciais:

- `ORIGINAL`
- `ENHANCED`
- `BACKGROUND_REMOVED`
- `AI_ON_MODEL`
- `SOCIAL_CREATIVE`
- `MARKETPLACE_VARIANT`
- `THUMBNAIL`

## Melhorias permitidas

Exemplos seguros quando não distorcem o produto:

- exposição e white balance;
- recorte/enquadramento;
- nitidez moderada;
- resize/compress;
- remoção de fundo;
- fundo neutro;
- limpeza de distrações externas ao item.

## Alterações proibidas sem sinalização/revisão forte

A IA não pode inventar ou modificar de modo enganoso:

- cor;
- tecido;
- textura;
- estampa;
- quantidade de botões;
- manga;
- decote;
- comprimento;
- transparência;
- acessórios incluídos;
- modelagem/caimento que represente outro produto.

## Modelo virtual

A partir de foto real de roupa/cabide/manequim, a plataforma pode gerar imagem com uma pessoa sintética usando a peça.

Requisitos:

- manter o original;
- marcar `aiGenerated=true`;
- registrar source, provider/model/version;
- revisão humana antes de publicar quando risco comercial exigir;
- comparar fidelidade visual com o produto real;
- respeitar regras específicas do canal/categoria;
- não usar imagem IA como prova factual quando o canal exigir fotografia real.

## Derivados por variante

Mídia pode ser associada à variante/cor. Ao selecionar Preto no storefront, a galeria deve preferir imagens daquela variante, com fallback para mídia geral do produto.

## IA para texto

Input preferido é dado estruturado validado:

```text
categoria: vestido
cor: azul
material: viscose
comprimento: midi
tamanhos: P/M/G
```

IA pode sugerir descrição/título/tags, mas não afirmar “100% algodão” ou “lavável em máquina” sem fonte correspondente.

Fluxo:

```text
Product Truth
→ prompt/schema
→ output estruturado
→ validação
→ revisão
→ Generated Content aprovado
```

## Classificação assistida

IA pode sugerir departamento, categoria, subcategoria, cor e tags. Sugestão não vira verdade sem confirmação ou regra determinística confiável.

## Conteúdo por canal

Um produto canônico pode gerar copies diferentes sem alterar o nome interno:

- site próprio: editorial;
- marketplace: objetivo e estruturado;
- social commerce: linguagem curta;
- SEO: título/meta específicos.

Regras de comprimento, campos obrigatórios e mídia vêm do adapter/Channel Compliance Engine.

## Compliance Engine de canais

Antes de publicar:

```text
Product Truth + Generated Assets + ChannelListing
                    ↓
            Channel Policy
                    ↓
         READY / BLOCKED / WARNING
```

Exemplos de checks:

- imagem principal aceita pelo canal;
- dimensões/resolução;
- presença de atributos obrigatórios;
- uso permitido de IA/modelo virtual;
- GTIN quando obrigatório;
- peso/dimensões de pacote;
- título/descrição dentro do contrato;
- categoria mapeada.

Políticas externas mudam; ruleset deve ser versionado e revalidado periodicamente.

## Processamento assíncrono

O usuário não deve ficar 20 segundos bloqueado.

```text
original salvo
→ job queued
→ UI mostra PROCESSING
→ worker cria derivados
→ READY ou FAILED acionável
```

Jobs precisam de idempotência, timeout, limites de tamanho/pixels, retries apenas seguros e dead-letter/reprocessamento quando necessário.

## Object storage e CDN

Bytes de mídia ficam em object storage; PostgreSQL guarda metadata/relacionamentos. Assets públicos podem ser servidos por CDN. Privados usam autorização/URLs assinadas.

## Segurança de upload

- allowlist de MIME/extensões baseada no conteúdo real;
- limite de bytes, pixels e quantidade;
- proteção contra decompression bombs;
- nomes/paths gerados pelo servidor;
- malware scanning quando aplicável;
- EXIF sensível removido quando desnecessário;
- nenhum payload do usuário vira instrução privilegiada para agentes/IA.

## Privacidade e pessoas reais

Fotos de pessoas reais podem envolver consentimento/direitos de imagem. Não reaproveitar fotos pessoais para treinamento/marketing sem base adequada. Conteúdo de crianças exige cuidado adicional e política específica.

## Custos e quotas SaaS

IA e processamento de mídia têm custo variável. Metering futuro pode contabilizar:

- imagens processadas;
- gerações de modelo virtual;
- tokens/text generations;
- storage;
- egress.

Quotas pertencem a entitlements/billing, não a `if plan == PRO` espalhado.

## AI Gateway

Clientes nunca chamam provedor diretamente:

```text
Web/Mobile
→ Mora Core
→ AI Gateway
→ Provider Adapter
```

Gateway aplica autenticação, budget, quota, logging sanitizado, schema, timeout, fallback e provenance.

## Evals e QA

Dataset de avaliação deve incluir:

- fidelidade de cor/estampa/modelo;
- atributos não inventados;
- grounding em Product Truth;
- formatos estruturados;
- casos adversariais;
- imagens ruins/oclusas;
- categorias infantis/sensíveis;
- regressão entre versões de modelo;
- avaliação humana.

Métrica de “imagem bonita” não basta; fidelidade ao item é requisito principal.

## Relacionados

- [Governança de IA](ai-governance.md)
- [Aplicativo mobile](../mobile/mobile-app.md)
- [Omnichannel](../commerce/omnichannel.md)
- [Marketplaces](../integrations/marketplaces.md)
- [Data lifecycle](../data/data-lifecycle.md)
