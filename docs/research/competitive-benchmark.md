# Benchmark Competitivo

> Snapshot de pesquisa de 2026. Revalidar preços, APIs e funcionalidades antes de decisão comercial/técnica.

## Bling
Pontos fortes: ERP cloud, PDV, estoque, compras, financeiro, fiscal, multi-depósito, moda/variações, REST/JSON/OAuth2 e webhooks. Bom benchmark de ecossistema e integração. Limitação percebida para operação física dependente de conectividade. Estratégia inicial do Mora Core: integrar antes de substituir.

## NOOVA PDV
Benchmark local de PDV com discurso de operação híbrida/offline, Windows/Android/Smart POS, TEF/Pix e suporte de implantação. API pública/documentada não foi confirmada; antes de qualquer integração é necessário perguntar por API, webhooks, sandbox, export, multi-loja, contingência e backup.

## Omie / Omie.PDV
Forte referência de ERP + PDV com operação offline, NFC-e, TEF/Pix, estoque/financeiro. API historicamente menos developer-friendly que REST moderno em algumas áreas; revalidar documentação.

## Olist ERP / Tiny
Referência de integração marketplace/e-commerce, estoque, PDV e multiempresa. Útil para estudar hub omnichannel e migração de catálogo.

## Linx Microvix
Benchmark forte de varejo/moda: grade cor/tamanho, coleções, promoções, fidelidade, comissões, multi-loja, consignação, mobile e omnichannel. Provavelmente excessivo para comprar para duas lojas, excelente para modelagem de domínio.

## TOTVS
Benchmark enterprise de varejo: planejamento, compras, grade, estoque, logística, PDV, financeiro, CRM e omnichannel. Usar como referência de breadth, não como modelo de complexidade arquitetural para v1.

## MarketUP
Referência de baixo custo para PDV/ERP físico/fiscal/ecommerce e operação multi-loja. Avaliar experiência/offline e trade-offs de suporte/ecossistema.

## Nex
Boa referência de simplicidade para pequenos comércios, desktop/mobile, estoque, caixa, catálogo, pedidos, clientes, etiquetas, fornecedores e operação offline/sync.

## Diferenciação pretendida do Mora Core

- mobile-first para entrada de mercadoria;
- foto → IA → catálogo → site/canais;
- site builder integrado ao ERP;
- PIM/OMS + operação física;
- commission/cash auditáveis;
- multi-tenant SaaS desde o domínio;
- integração fácil e exportável;
- produto real como primeira fonte de requisitos.

O diferencial não deve ser “ter mais features”, mas reduzir trabalho operacional sem sacrificar integridade e segurança.
