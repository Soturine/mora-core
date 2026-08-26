# Integrações com Marketplaces e Social Commerce

## Objetivo

Publicar catálogo e receber pedidos sem duplicar produto/estoque.

## Canais planejados

- TikTok Shop
- Mercado Livre
- Shopee
- futuros canais via adapter

## Adapter

Interface conceitual: `publishProduct`, `updateListing`, `updateStock`, `updatePrice`, `deactivateListing`, `fetchOrders`, `acknowledgeOrder`, `syncStatus`.

Cada provider traduz contratos, categorias, atributos, mídia e erros para o modelo canônico.

## Compliance engine

Antes de publicar, validar requisitos do canal: campos obrigatórios, dimensões/peso, mídia, categoria, imagem principal, restrições de IA e dados fiscais.

As regras mudam; manter versionadas/configuráveis e revalidar documentação oficial antes de implementação.

## Webhooks

Validar assinatura quando suportada → persistir event ID → responder rápido → processar async → dedupe/idempotência → reconciliar estado canônico.

## Credenciais

OAuth/tokens criptografados no backend, scopes mínimos, renovação/revogação e sem exposição ao frontend/mobile.

## Mídia por canal

Foto real original/derivada e imagem IA são tipos distintos. Canal escolhe asset compatível com suas políticas atuais.
