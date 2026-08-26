# Marketplaces e Canais Externos

> **Status:** estratégia e requisitos arquiteturais. Endpoints, quotas, políticas e requisitos comerciais de cada provedor devem ser revalidados na documentação oficial no momento da integração.

## Canais prioritários planejados

- TikTok Shop;
- Mercado Livre;
- Shopee;
- site próprio Mora Storefront;
- outros canais apenas após justificativa.

## Princípio

O Mora Core mantém um modelo canônico e traduz para cada plataforma via adapter. Nenhum marketplace define o modelo interno da plataforma.

## TikTok Shop

Casos esperados:

- sincronizar produtos/variantes;
- mídia e atributos;
- preço/estoque;
- receber pedidos;
- fulfillment/logística;
- cancelamento/devolução;
- webhooks;
- requisitos fiscais específicos do Brasil quando aplicáveis.

A plataforma possui políticas próprias para imagens, conteúdo gerado/modificado por IA e categorias. Revalidar sempre antes de publicar; não assumir que uma mídia aceita no site próprio será aceita no TikTok Shop.

## Mercado Livre

Casos esperados:

- publicação/listing;
- mapeamento de categoria/atributos;
- variantes;
- preço/estoque;
- pedidos;
- status/logística;
- devolução/cancelamento;
- notificações/webhooks conforme contrato atual.

O modelo de publicação e identificadores externos pode evoluir; adapter precisa isolar essas mudanças.

## Shopee

Planejar adapter separado. Não congelar endpoints, versões ou comportamento com base em documentação não confirmada. O onboarding real precisa validar Open Platform, autenticação, catálogo, pedidos, estoque, limites, webhooks e requisitos regionais.

## `ChannelConnection`

```text
ChannelConnection
├── id
├── organizationId
├── provider
├── externalAccountId
├── status
├── scopes
├── credentialReference
├── tokenExpiresAt?
├── lastHealthyAt?
└── metadata mínima
```

Credenciais ficam no backend/secret store, criptografadas e com least privilege. Nunca no frontend/mobile/Git/logs.

## OAuth e SaaS

O primeiro uso pode integrar contas próprias da Mora. Para SaaS, cada organização conecta sua própria conta por fluxo autorizado do provedor.

```text
Cliente SaaS
→ Conectar canal
→ OAuth/authorization
→ callback server-side
→ validar state/tenant
→ armazenar credencial protegida
→ testar conexão
```

## Publicação

Tela planejada:

```text
Produto: Vestido Helena

Site próprio      READY
Mercado Livre     WARNING: falta peso do pacote
Shopee            READY
TikTok Shop       BLOCKED: asset principal não atende regra atual
```

“Publicar em todos” cria jobs independentes e mostra resultado por canal.

## IA e conteúdo

IA pode sugerir títulos/descriptions específicos por canal, sempre derivados de fatos reais. Mídia gerada por IA é marcada e submetida às políticas do canal.

## Estoque

O saldo é central. Cada channel listing recebe projeção de disponibilidade. Reserva e safety stock reduzem overselling. O canal externo nunca se torna a fonte primária de estoque do Mora Core após a transição para Core-as-source-of-truth.

## Pedidos

Pedido externo deve preservar:

- externalOrderId;
- provider/account;
- payload version/reference;
- timestamps externos;
- itens/mappings;
- status canônico;
- eventos recebidos;
- idempotency/dedupe state.

## Fiscal

Fluxos de marketplace podem exigir emissão/upload de documentos fiscais antes de certas etapas. O domínio fiscal fica atrás de adapter e é validado conforme legislação e regras do canal vigentes.

## Falhas

- rate limit;
- token expirado/revogado;
- schema incompatível;
- listing rejeitado;
- webhook duplicado;
- webhook fora de ordem;
- timeout;
- pedido sem mapping;
- drift de estoque;
- indisponibilidade do provedor.

Todos precisam de estado acionável, métricas e replay seguro quando aplicável.

## SaaS e isolamento

Cada job, webhook, token e listing deve carregar contexto de tenant. Não permitir que `externalAccountId` sozinho determine organização sem mapping seguro.

## Não objetivos iniciais

- suportar dezenas de marketplaces;
- criar abstraction “universal” que esconda diferenças reais;
- prometer sync instantâneo absoluto;
- auto-publicar produto gerado por IA sem regras/aprovação;
- implementar scraping quando API oficial adequada existir.

## Relacionados

- [PIM/OMS/Omnichannel](../commerce/omnichannel.md)
- [Confiabilidade](reliability-patterns.md)
- [Mídia e IA](../ai/media-pipeline.md)
- [Fiscal](../domain/fiscal-and-payments.md)
