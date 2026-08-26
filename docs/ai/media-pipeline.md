# Pipeline de Mídia e IA

## Objetivo

Transformar fotos reais em assets úteis para site/canais preservando fidelidade e original.

```text
Upload original
→ validação MIME/dimensões/budget
→ object storage
→ job
→ resize/compress
→ derivados
→ IA opcional
→ revisão/aprovação
→ publicação por canal
```

## Tipos de asset

`ORIGINAL`, `ENHANCED`, `BACKGROUND_REMOVED`, `AI_ON_MODEL`, `SOCIAL_CREATIVE`, `MARKETPLACE_VARIANT`, `THUMBNAIL`.

## Regras de fidelidade

IA não pode alterar de forma enganosa cor, tecido, estampa, botões, decote, manga, comprimento, transparência, acessórios incluídos ou outras características do produto.

## Modelo virtual

Pode gerar representação com pessoa usando a roupa, mantendo referência clara ao asset real. Alguns marketplaces/categorias impõem restrições; o channel compliance engine decide quais assets são aceitos.

## Derivados

Original nunca é sobrescrito. Derivado possui `sourceAssetId`, transformação, crop, formato, versão e status de aprovação.

## Performance

Processamento assíncrono; WebP/AVIF quando compatível; múltiplos tamanhos; lazy loading; CDN para públicos.

## Privacidade

Remover EXIF sensível quando desnecessário. Fotos com pessoas reais exigem tratamento/consentimento apropriado.
