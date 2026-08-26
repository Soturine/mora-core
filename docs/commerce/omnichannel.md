# Omnichannel, PIM e OMS

## PIM

O Mora Core mantém produto canônico e variantes. Canais recebem projeções e overrides, nunca cópias independentes sem vínculo.

`ChannelListing` guarda canal, external IDs, categoria externa, title/description override, status, sync e erro.

## OMS

Pedidos de site/marketplaces entram em um modelo canônico de `Order`, itens, pagamento, reserva, fulfillment, cancelamento e devolução.

## Estoque compartilhado

3 unidades listadas em 4 canais continuam sendo 3 unidades. Venda/reserva em um canal reduz disponibilidade global e dispara sync.

## Safety stock

Disponibilidade por canal pode aplicar estoque de segurança para reduzir overselling.

## Ranking

Mais vendidos usa `SaleItem` real por janela (ex.: 30/90 dias), filtrado por publicação/disponibilidade conforme policy. Estoque não é proxy de venda.

## Conciliação

Jobs periódicos reconciliam catálogo, preço, estoque, pedidos e status para detectar webhooks perdidos.

## Falhas

Uma publicação multi-canal é composta por jobs independentes; sucesso em TikTok não é revertido só porque Shopee falhou. Erro deve ser acionável e reprocessável.
