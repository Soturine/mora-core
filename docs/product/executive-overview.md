# Visão Executiva do Mora Core

> **Para proprietários, familiares, parceiros e pessoas não técnicas.**
>
> **Status atual:** visão e arquitetura documentadas; o sistema ainda será implementado por fases. Este arquivo explica **o que o Mora Core pretende resolver e como funcionará**, sem fingir que todas as funções já estão prontas.

## Em uma frase

O **Mora Core** é uma plataforma para organizar a operação de uma loja — do momento em que a mercadoria chega até a venda, o estoque, o caixa, a comissão, o site, o WhatsApp e os canais online — evitando cadastrar a mesma informação várias vezes e transformando pedidos perdidos em informação para novas compras.

## Por que o projeto existe

Hoje um comércio pode acabar usando vários lugares diferentes para trabalhar:

- ERP/PDV;
- planilha;
- site;
- WhatsApp;
- marketplace;
- fotos no celular;
- relatórios separados;
- controles manuais de comissão e caixa.

Isso cria retrabalho e divergência. Um produto pode existir no estoque físico, mas não estar no site; pode vender em um canal e continuar aparecendo disponível em outro; uma funcionária pode precisar calcular comissão manualmente; e uma foto precisa ser reenviada e cadastrada várias vezes.

A ideia do Mora Core é ter **uma fonte central de informação e operação**.

```text
Mercadoria chega
      ↓
Cadastrar/identificar produto
      ↓
Variantes: cor, tamanho, modelo
      ↓
Fotos
      ↓
Entrada de estoque
      ↓
Publicação
 ┌────┼─────────────┬──────────────┬──────────────┐
 │    │             │              │              │
PDV  Site       WhatsApp      TikTok/ML/Shopee  Social
 │    │             │              │              │
 └────┴─────────────┴──────────────┴──────────────┘
      ↓
Venda / pedido
      ↓
Estoque atualizado
      ↓
Caixa / comissão / relatórios
      ↓
Demanda não atendida → compras/reposição
```

## Como funcionaria na prática

### 1. Chegou mercadoria

A pessoa abre o aplicativo no celular e pode:

1. apontar a câmera para o código de barras;
2. se o produto já existir, abrir o cadastro;
3. se não existir, cadastrar;
4. se não tiver código, gerar um código interno/etiqueta;
5. informar cor, tamanho, modelo e quantidade;
6. tirar fotos;
7. confirmar a entrada no estoque.

Para roupas, o cadastro será preparado para grade de variantes, por exemplo:

```text
           P   M   G   GG
Azul       ✓   ✓   ✓   —
Preto      ✓   ✓   —   —
Bege       —   ✓   ✓   ✓
```

Cada combinação real é um item controlável separadamente no estoque.

### 2. Fotos e inteligência artificial

A foto original será preservada. A IA poderá ajudar a:

- corrigir iluminação;
- enquadrar;
- remover ou limpar fundo;
- gerar versões adequadas ao site;
- sugerir título, descrição, categoria e tags;
- gerar, quando permitido, uma imagem de modelo virtual usando a peça;
- preparar conteúdo diferente para cada canal;
- encontrar produtos parecidos quando uma cliente envia uma foto.

A IA **não pode inventar características do produto**. Se a peça não tem determinado tecido, cor, botão ou detalhe, a imagem/descrição não pode criar isso.

A separação é:

- **Product Truth:** fatos reais do produto;
- **Generated Content:** conteúdo criado ou melhorado a partir dos fatos.

A publicação de conteúdo de maior risco passa por revisão humana.

### 3. Estoque

O estoque não será apenas um número que alguém digita por cima. O sistema registra o motivo de cada mudança:

```text
+ recebimento
- venda
+ devolução
- perda
- transferência para outra loja
+ transferência recebida
+/- ajuste autorizado
```

Assim fica possível descobrir **por que** o saldo chegou ao valor atual.

Também será possível fazer inventário pelo celular usando a câmera como coletor.

### 4. Venda e funcionárias

Cada funcionária terá login individual e permissões adequadas.

Uma venda pode registrar separadamente:

- quem vendeu (`seller`);
- quem recebeu no caixa (`cashier`).

Isso é importante quando uma pessoa atende o cliente e outra finaliza o pagamento.

A funcionária poderá, se a empresa permitir, visualizar no aplicativo:

- suas vendas;
- peças vendidas;
- comissão provisionada/estimada;
- metas;
- últimas vendas;
- estoque/produtos.

Ela não recebe automaticamente acesso ao custo das peças, margem, faturamento completo ou vendas de colegas.

### 5. Comissão

Comissão não será um número digitado sem explicação.

Cada venda elegível gera registros auditáveis. Se houver cancelamento ou devolução, o sistema registra uma reversão. No fechamento do mês, é possível abrir o extrato e ver quais vendas formaram o total.

A regra real de comissão será configurável e precisa ser confirmada com a operação/contabilidade antes de ser implementada.

### 6. Caixa

O sistema prevê:

- abertura;
- fundo inicial;
- vendas;
- sangria;
- suprimento;
- devolução;
- fechamento;
- reconciliação por meio de pagamento;
- diferença entre esperado e contado;
- aprovação quando necessária.

Pode existir **fechamento cego**, no qual o operador conta o dinheiro antes de ver o valor esperado.

### 7. Duas ou mais lojas

O Mora Core não será limitado a uma loja.

```text
Organização: Mora
├── Loja/Marca feminina e familiar
└── Loja/Marca masculina
```

Funcionários podem ter acesso a uma ou às duas, e relatórios podem ser separados ou consolidados.

No futuro SaaS, outra empresa pode possuir uma, três ou dezenas de lojas sem misturar dados com a Mora.

### Regra muito importante

Se uma cliente está comprando de uma empresa, o sistema **nunca sugere produtos de um concorrente ou de outra empresa usuária do Mora Core**.

```text
Cliente da Organização A
→ busca/recomendação
→ SOMENTE produtos e canais da Organização A
```

Dentro da mesma organização, pode procurar outra loja, marca ou depósito quando a política permitir.

### 8. Sites

Os sites deixam de precisar de cadastro manual de produtos no código.

```text
Mora Core
   ↓
Catálogo público
   ↓
Site
```

Cadastrar/publicar um produto no Core passa a alimentar o site automaticamente conforme as regras de publicação.

A plataforma também é pensada para, no futuro, permitir que um novo cliente do SaaS que **não tenha site** crie um storefront com tema, logo, cores, categorias, páginas de produto e domínio próprio.

### 9. WhatsApp como vendedor virtual

Uma das primeiras formas de vender online pode ser pelo próprio WhatsApp.

Exemplo:

```text
Cliente abre o site
→ escolhe vestido preto M
→ toca “Comprar pelo WhatsApp”
→ assistente já sabe qual produto foi aberto
→ consulta preço/estoque real
→ conversa naturalmente
→ monta carrinho
→ chama uma funcionária quando necessário
```

O assistente pode perguntar:

> “Quer retirar na loja ou prefere entrega?”

> “Quer que eu mostre uma bolsa que combina?”

Mas o backend é quem confirma preço, estoque, variante, reserva e pagamento.

### 10. Quando a peça acabou

A resposta não precisa ser apenas “não temos”.

O Mora Core pode procurar, nesta ordem:

```text
mesma peça em outra loja da mesma organização
→ outra cor/tamanho
→ produto parecido da mesma organização
→ listing oficial da própria organização em outro canal
→ avisar quando voltar
→ registrar pedido da cliente
→ avaliar trazer na próxima compra
→ chamar atendente
```

Nunca procurar concorrente para oferecer ao cliente.

### 11. Cliente manda foto

A cliente pode mandar uma foto pelo WhatsApp ou site e perguntar:

> “Tem algo assim?”

O sistema analisa a imagem e pesquisa **somente no catálogo daquela organização**.

Se encontrar, mostra opções reais com fotos e estoque.

Se não encontrar, pode perguntar:

> “Quer que eu registre esse estilo para a equipe procurar na próxima compra?”

### 12. “Quando forem para São Paulo, tragam para mim”

Em vez de isso ficar perdido no histórico do WhatsApp, vira uma solicitação organizada.

```text
Pedido da cliente
→ foto/referência
→ tamanho/cor/faixa de preço
→ lista de demanda
→ próxima viagem de compras
→ encontraram candidato
→ mandam foto/preço para cliente
→ cliente aprova
→ compra
→ recebimento
→ reserva para cliente
→ aviso de chegada
```

A loja continua decidindo o que realmente vai comprar. A IA não promete nem compra mercadoria sozinha.

### 13. O sistema aprende demanda perdida

Mesmo quando não há venda, a pergunta da cliente tem valor.

Exemplo de relatório futuro:

```text
Pedidos que não conseguimos atender — 30 dias

Vestido preto M           23
Calça feminina 44         17
Conjunto infantil 8 anos  14
Body RN branco            11
```

Isso ajuda seus proprietários a decidir o que procurar/reposicionar na próxima compra.

**Demanda não atendida não é contabilizada como venda.**

### 14. Novidades, mais vendidos e destaques

Esses conceitos não serão misturados:

- **Novidades:** produtos publicados/disponibilizados recentemente;
- **Mais vendidos:** calculado a partir de vendas reais, nunca do estoque;
- **Destaques:** curadoria manual;
- **Promoções:** regra comercial/preço.

### 15. TikTok Shop, Mercado Livre e Shopee

A direção é cadastrar uma vez e adaptar para os canais:

```text
Produto canônico
       ↓
 ┌─────┼──────────┬───────────┐
Site WhatsApp   TikTok      ML/Shopee
```

Cada canal possui regras diferentes. O Mora Core terá adapters e validações antes de publicar.

Quando uma unidade vende em um canal, o saldo central muda e os demais canais precisam receber a atualização. Isso reduz risco de vender uma peça que já acabou.

Se o assistente do WhatsApp oferecer um link do Mercado Livre/Shopee, esse listing precisa ser **da própria organização**, nunca de terceiros.

### 16. Pedidos online

O futuro OMS centraliza pedidos de diferentes canais e acompanha:

- pedido;
- pagamento/status;
- reserva de estoque;
- separação;
- nota fiscal quando aplicável;
- envio;
- tracking;
- cancelamento;
- devolução.

### 17. Pagamento e comprovante

A cliente pode pagar por Pix, link/provider ou recurso do próprio canal quando disponível.

A foto de um comprovante **não deve dar baixa automática no estoque**.

O sistema prefere confirmação direta do banco/gateway/provedor. Se não houver integração, o pagamento fica aguardando revisão humana.

### 18. Nota fiscal

O governo possui interfaces oficiais, mas não existe uma única “API mágica” para qualquer nota.

Para mercadorias, NF-e/NFC-e usam integrações oficiais das Secretarias de Fazenda, certificados, XML e regras fiscais. A NFS-e nacional possui APIs próprias para **serviços**.

No início, a estratégia mais segura é deixar o Bling ou um provedor fiscal maduro fazer essa parte e o Mora Core coordenar o processo.

## Aplicativo

O aplicativo não é apenas “o painel do computador menor”. Ele usa o que o celular faz melhor:

- câmera;
- scanner;
- fotografia;
- mobilidade pelo estoque;
- inventário;
- consulta rápida;
- notificações;
- dashboards por perfil;
- lista de compras/sourcing durante viagens a fornecedores.

Alguns fluxos, como inventário e lista de compras, podem funcionar temporariamente sem internet e sincronizar depois com idempotência para não duplicar movimentos.

## Dashboard dos proprietários

Exemplos de informação futura:

- vendas por loja e consolidado;
- ticket médio;
- peças vendidas;
- estoque baixo;
- produtos sem estoque;
- mais vendidos;
- produtos parados/aging;
- curva ABC;
- vendas por funcionária;
- comissões;
- formas de pagamento;
- caixas abertos;
- divergências;
- vendas por canal;
- devoluções/trocas;
- pedidos feitos por clientes que não conseguimos atender;
- peças, tamanhos e cores mais procurados sem estoque;
- taxa de venda recuperada por alternativa;
- solicitações de compra/encomenda;
- conversão de sourcing em venda.

Os números devem sempre ser derivados de dados reais e rastreáveis.

## De ERP interno para SaaS

A primeira operação é a Mora, funcionando como **design partner**: problemas reais orientam o produto.

A arquitetura, porém, já evita assumir que existe apenas uma empresa.

```text
Mora Core Platform
├── Organização A
│   ├── marcas
│   ├── lojas
│   ├── funcionários
│   ├── produtos
│   ├── WhatsApp
│   └── sites/canais
├── Organização B
│   └── ...
└── Organização C
    └── ...
```

Dados, recomendações e busca de uma organização não podem vazar para outra.

## O que “SaaS-ready” significa

Significa preparar desde cedo:

- isolamento de dados;
- organizações e lojas configuráveis;
- permissões;
- onboarding;
- importação/exportação;
- integrações por conta/tenant;
- billing separado;
- quotas/entitlements;
- backups e recuperação;
- observabilidade;
- segurança e auditoria;
- isolamento de buscas, IA e recomendações.

Não significa construir Kubernetes, dezenas de microserviços ou infraestrutura gigante antes de existir necessidade.

## Segurança

Dinheiro, estoque, comissão e dados de empresas exigem controles fortes:

- login individual;
- permissões por papel e loja;
- MFA para perfis privilegiados quando aplicável;
- isolamento multi-tenant;
- ações críticas auditadas;
- secrets protegidos;
- tokens de marketplace/WhatsApp somente no backend;
- criptografia/TLS;
- backups testados;
- validação de uploads;
- proteção contra ações duplicadas;
- testes de segurança e supply chain;
- busca visual/vector search tenant-scoped.

## Fiscal e pagamentos

Essa é uma área de alto risco. O Mora Core não deve inventar uma solução fiscal própria sem necessidade.

A estratégia é começar integrando sistemas/provedores já maduros e somente substituir funções críticas quando houver evidência, validação contábil/fiscal e contingência real.

## O que NÃO será feito de uma vez

A visão é grande, mas o projeto será incremental. A sequência planejada é:

1. entender a operação real;
2. fundação de organização, usuários, catálogo e estoque;
3. aplicativo operacional;
4. integração dos sites;
5. commerce conversacional básico;
6. venda/PDV/caixa/comissão;
7. analytics;
8. IA;
9. omnichannel;
10. maturação SaaS.

## Como a família pode ajudar agora

O passo mais valioso é revisar o [Discovery da operação](../discovery/operational-discovery.md) e responder como a loja realmente trabalha hoje: recebimento, cadastro, estoque, venda, WhatsApp, pedidos por foto, encomendas, viagens de compra, troca, comissão, caixa, fiscal, hardware e Bling.

Essas respostas transformam “ideias de software” em requisitos corretos.

## Onde ler mais

- [Visão do produto](vision.md)
- [Módulos e capacidades](modules.md)
- [Jornadas e usuários](personas-and-journeys.md)
- [WhatsApp Commerce Agent](../commerce/whatsapp-commerce-agent.md)
- [Demanda, encomendas e sourcing](../commerce/customer-demand-and-sourcing.md)
- [Aplicativo mobile](../mobile/mobile-app.md)
- [IA e fotos](../ai/media-pipeline.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Storefront e criação de sites](storefront.md)
- [Marketplaces](../integrations/marketplaces.md)
- [Fiscal Brasil](../integrations/fiscal-brazil.md)
- [Benchmark de commerce conversacional](../research/conversational-commerce-benchmark.md)
- [Benchmark de sistemas](../research/competitive-benchmark.md)
- [Roadmap](../roadmap/roadmap.md)

---

**Resumo:** o objetivo é que o lojista cadastre a mercadoria uma vez e o Mora Core mantenha conectados produto, estoque, venda, caixa, comissão, site, WhatsApp, aplicativo e canais digitais — e ainda transforme o que o cliente pede e a loja não tem em sinal para compras futuras, sempre com segurança e isolamento entre empresas.
