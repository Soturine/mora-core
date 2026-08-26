# Visão Executiva do Mora Core

> **Para proprietários, familiares, parceiros e pessoas não técnicas.**
>
> **Status atual:** visão e arquitetura documentadas; o sistema ainda será implementado por fases. Este arquivo explica **o que o Mora Core pretende resolver e como funcionará**, sem fingir que todas as funções já estão prontas.

## Em uma frase

O **Mora Core** é uma plataforma para organizar a operação de uma loja — do momento em que a mercadoria chega até a venda, o estoque, o caixa, a comissão, o site e os canais online — evitando cadastrar a mesma informação várias vezes.

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

A ideia do Mora Core é ter **uma fonte central de informação**.

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
 ┌────┼─────────────┬──────────────┐
 │    │             │              │
PDV  Site       TikTok Shop   Mercado Livre/Shopee
 │    │             │              │
 └────┴─────────────┴──────────────┘
      ↓
Venda / pedido
      ↓
Estoque atualizado
      ↓
Caixa / comissão / relatórios
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
- preparar conteúdo diferente para cada canal.

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

### 9. Novidades, mais vendidos e destaques

Esses conceitos não serão misturados:

- **Novidades:** produtos publicados/disponibilizados recentemente;
- **Mais vendidos:** calculado a partir de vendas reais, nunca do estoque;
- **Destaques:** curadoria manual;
- **Promoções:** regra comercial/preço.

### 10. TikTok Shop, Mercado Livre e Shopee

A direção é cadastrar uma vez e adaptar para os canais:

```text
Produto canônico
       ↓
 ┌─────┼─────────┐
Site   TikTok    Mercado Livre    Shopee
```

Cada canal possui regras diferentes. O Mora Core terá adapters e validações antes de publicar.

Quando uma unidade vende em um canal, o saldo central muda e os demais canais precisam receber a atualização. Isso reduz risco de vender uma peça que já acabou.

### 11. Pedidos online

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

## Aplicativo

O aplicativo não é apenas “o painel do computador menor”. Ele usa o que o celular faz melhor:

- câmera;
- scanner;
- fotografia;
- mobilidade pelo estoque;
- inventário;
- consulta rápida;
- notificações;
- dashboards por perfil.

Alguns fluxos, como inventário, podem funcionar temporariamente sem internet e sincronizar depois com idempotência para não duplicar movimentos.

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
- devoluções/trocas.

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
│   └── sites/canais
├── Organização B
│   └── ...
└── Organização C
    └── ...
```

Dados de uma organização não podem ser vistos por outra.

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
- segurança e auditoria.

Não significa construir Kubernetes, dezenas de microserviços ou infraestrutura gigante antes de existir necessidade.

## Segurança

Dinheiro, estoque, comissão e dados de empresas exigem controles fortes:

- login individual;
- permissões por papel e loja;
- MFA para perfis privilegiados quando aplicável;
- isolamento multi-tenant;
- ações críticas auditadas;
- secrets protegidos;
- tokens de marketplace somente no backend;
- criptografia/TLS;
- backups testados;
- validação de uploads;
- proteção contra ações duplicadas;
- testes de segurança e supply chain.

## Fiscal e pagamentos

Essa é uma área de alto risco. O Mora Core não deve inventar uma solução fiscal própria sem necessidade.

A estratégia é começar integrando sistemas/provedores já maduros e somente substituir funções críticas quando houver evidência, validação contábil/fiscal e contingência real.

## O que NÃO será feito de uma vez

A visão é grande, mas o projeto será incremental. A sequência planejada é:

1. entender a operação real;
2. fundação de organização, usuários, catálogo e estoque;
3. aplicativo operacional;
4. integração dos sites;
5. venda/PDV/caixa/comissão;
6. analytics;
7. IA;
8. omnichannel;
9. maturação SaaS.

## Como a família pode ajudar agora

O passo mais valioso é revisar o [Discovery da operação](../discovery/operational-discovery.md) e responder como a loja realmente trabalha hoje: recebimento, cadastro, estoque, venda, troca, comissão, caixa, fiscal, hardware e Bling.

Essas respostas transformam “ideias de software” em requisitos corretos.

## Onde ler mais

- [Visão do produto](vision.md)
- [Módulos e capacidades](modules.md)
- [Jornadas e usuários](personas-and-journeys.md)
- [Aplicativo mobile](../mobile/mobile-app.md)
- [IA e fotos](../ai/media-pipeline.md)
- [Vendas, caixa e comissões](../domain/sales-cash-commissions.md)
- [Storefront e criação de sites](storefront.md)
- [Marketplaces](../integrations/marketplaces.md)
- [Benchmark de sistemas](../research/competitive-benchmark.md)
- [Roadmap](../roadmap/roadmap.md)

---

**Resumo:** o objetivo é que o lojista cadastre a mercadoria uma vez e o Mora Core mantenha conectados produto, estoque, venda, caixa, comissão, site, aplicativo e canais digitais com segurança e rastreabilidade.
