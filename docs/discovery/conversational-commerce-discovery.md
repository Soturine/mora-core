# Discovery — WhatsApp, Demanda, Encomendas e Sourcing

> **Objetivo:** levantar como a operação real vende e recebe pedidos pelo WhatsApp/site antes de automatizar conversa, pagamento, reserva, fiscal ou compra de mercadoria.

Este documento complementa o [Discovery Operacional](operational-discovery.md).

## 1. WhatsApp atual

Registrar:

```text
Número(s):
Conta/app atual:
Quem atende:
Horário:
Volume aproximado/dia:
Tempo médio de resposta:
Uso de catálogo:
Uso de etiquetas/labels:
Uso de respostas rápidas:
Uso de múltiplos aparelhos:
Problemas atuais:
```

Perguntas:

- é WhatsApp comum, Business App ou já existe Business Platform/API?
- o mesmo número atende as duas lojas?
- como sabem qual loja o cliente quer?
- quem assume a conversa quando outra pessoa começou?
- há histórico perdido/troca de aparelho?
- fazem campanhas/avisos de reposição?
- clientes mandam áudio com frequência?
- clientes mandam fotos perguntando por peças?

## 2. Entrada pelo site

- hoje o botão WhatsApp já informa qual produto foi clicado?
- cliente costuma escolher tamanho/cor antes de chamar?
- desejam carrinho no site antes do WhatsApp?
- o preço online é sempre o mesmo da loja?
- quando produto está sem estoque, qual resposta desejada?
- site deve oferecer “envie uma foto”?
- site deve oferecer “avise quando voltar”?
- site deve oferecer “quero pedir algo assim”?

## 3. Alternativas

Validar escopo comercial:

```text
STORE_ONLY
BRAND_ONLY
ORGANIZATION
```

Perguntas:

- se a peça está em outra loja da mesma organização, pode oferecer?
- pode transferir entre lojas?
- pode entregar a partir de outra loja?
- pode direcionar para listing próprio no Mercado Livre/Shopee/TikTok?
- existem produtos/marcas que não devem ser cruzados entre sites?

**Regra de plataforma:** nunca sugerir outro tenant/concorrente.

## 4. Busca por foto

Observar exemplos reais de clientes que mandam foto.

Registrar:

- o que a funcionária olha primeiro?
- cor, tamanho, estilo, marca, ocasião?
- cliente normalmente informa orçamento?
- quando há três opções parecidas, como escolhe o que mostrar?
- pode mostrar foto com modelo virtual?
- quanto tempo a imagem do cliente precisa ser mantida?

Testar casos:

- uma foto com uma peça;
- look inteiro;
- print de marketplace;
- baixa resolução;
- foto de criança;
- imagem com marca/logotipo;
- cliente pede réplica/cópia.

## 5. Produto sem estoque

Mapear a ordem real desejada:

```text
outra unidade
outra variante
produto semelhante
listing próprio externo
back-in-stock
pedido/encomenda
atendente
```

Perguntar:

- quando é aceitável sugerir tamanho diferente?
- há política para “última unidade”?
- reservam peça por WhatsApp?
- por quanto tempo?
- reserva exige sinal?

## 6. “Tragam para mim quando forem comprar”

Levantar o processo informal atual:

- clientes já fazem esse pedido?
- quem anota?
- onde anota?
- o que precisa constar?
- a loja realmente se compromete ou apenas tenta procurar?
- cliente define preço máximo?
- há sinal/depósito?
- quem assume risco se comprar e cliente desistir?
- há regra de troca de encomenda?
- quanto tempo a peça fica reservada depois que chega?

## 7. Viagens de compra

Para cada tipo de viagem:

```text
Destino:
Frequência:
Quem vai:
Fornecedores/regiões:
Duração:
Internet disponível:
Budget:
Como fazem lista hoje:
Como registram preços/fotos:
Como compram/recebem nota:
```

Perguntas:

- precisam usar lista offline?
- compram por grade/lote?
- fornecedor aceita reservar enquanto consultam cliente?
- dá para mandar foto para cliente durante a viagem?
- qual tempo de resposta aceitável?
- se cliente não responde, comprador pode comprar por conta própria?

## 8. Aprovação do cliente

Definir estados diferentes:

- interesse;
- pedido para procurar;
- candidato encontrado;
- preço informado;
- cliente aprovou;
- sinal pago;
- item comprado;
- item recebido;
- item reservado;
- retirado/entregue.

Não condensar tudo em “encomenda”.

## 9. Pagamento remoto

- Pix para qual conta?
- usam QR/link?
- cliente costuma mandar comprovante?
- alguém confere manualmente?
- há integração bancária/gateway?
- quanto tempo para confirmar?
- o que acontece se comprovante for falso/duplicado?
- reserva começa antes ou depois do pagamento?

Regra arquitetural: imagem do comprovante não confirma automaticamente.

## 10. Fiscal de venda remota

Com contador/operador:

- como emitem nota para venda via WhatsApp hoje?
- NFC-e ou NF-e em quais casos?
- venda para CNPJ?
- entrega muda o documento/processo?
- emitem pelo Bling?
- documento é enviado ao cliente como?
- o marketplace exige upload de nota?
- há contingência?

## 11. Handoff humano

- quando a IA deve chamar pessoa automaticamente?
- quem recebe a conversa?
- distribuição por loja/vendedora?
- venda concluída por humana gera comissão?
- venda 100% IA gera comissão?
- como atribuir atendimento iniciado por uma e finalizado por outra?

## 12. Cross-sell e atendimento

Perguntar como as melhores vendedoras trabalham hoje:

- que perguntas fazem?
- como descobrem ocasião?
- como sugerem complemento?
- quando param de insistir?
- como tratam cliente indeciso?
- quais informações nunca devem ser prometidas sem verificar?

Transformar prática boa em política/retrieval, não em prompt solto.

## 13. Back-in-stock e relacionamento

- clientes pedem para avisar quando chegar?
- como anotam hoje?
- por quanto tempo manter interesse?
- podem enviar mensagem ativa?
- há consentimento de marketing?
- querem carrinho abandonado?
- querem aviso de nova coleção compatível?

Revalidar políticas/templates do WhatsApp antes da implementação.

## 14. Marketplace próprio como alternativa

Para cada canal conectado:

- a conta é da mesma organização?
- preço difere?
- frete/promoção do canal pode ser melhor?
- há motivo para direcionar cliente do WhatsApp para o canal?
- comissão/taxa torna isso indesejável?
- como rastrear conversão?

Nunca pesquisar listing de terceiros para recomendar.

## 15. Demanda perdida

Definir quais motivos interessam:

```text
sem estoque
sem tamanho
sem cor
sem produto parecido
preço
prazo
cliente desistiu
fornecedor não encontrou
```

Perguntar quais relatórios ajudariam compras:

- top pedidos não atendidos;
- tamanho/cor ausente;
- demanda por categoria;
- origem WhatsApp/site;
- quantas alternativas recuperaram a venda;
- quantos sourcing requests viraram venda.

## 16. Segurança e privacidade

- por quanto tempo guardar conversa?
- por quanto tempo guardar foto enviada por cliente?
- quem pode acessar?
- clientes podem pedir exclusão/export?
- mensagens contêm documentos/endereços?
- atendimento usa aparelhos pessoais?
- há risco de funcionária levar contatos ao sair?

## 17. Critérios para o piloto

Antes de automatizar pagamento/fiscal, medir primeiro:

- taxa de pergunta respondida corretamente;
- disponibilidade correta;
- tempo até handoff;
- zero cross-tenant;
- satisfação das funcionárias;
- quantas vendas recuperadas por alternativa;
- quantos pedidos de sourcing realmente são úteis.

## Saída esperada

Depois do Discovery, produzir:

1. mapa `as-is`;
2. política de recomendação por organização/loja/marca;
3. política de reserva;
4. política de sourcing/encomenda;
5. política de pagamento remoto;
6. handoff/commission attribution;
7. requisitos fiscais;
8. dados/retention;
9. MVP e acceptance criteria;
10. ADRs necessários.

## Relacionados

- [Discovery Operacional](operational-discovery.md)
- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda e Sourcing](../commerce/customer-demand-and-sourcing.md)
- [Compras](../domain/purchasing-and-suppliers.md)
- [Fiscal Brasil](../integrations/fiscal-brazil.md)
